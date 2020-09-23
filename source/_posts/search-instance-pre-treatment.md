---
title: [转]搜索实例预热处理
date: 2014-11-08 17:13:00
updated: 2015-01-08 15:32:59
tags: 
- golang
categories: 
- go

---
搜索平台化中预热的需求描述为：当线上集群需要水平扩容时，新部署实例的机器需要用已存在的日志进行预查询，其目的是将用户的常用查询加载到扩容机内存中，最终对外开放时以提高缓存命中率，减少搜索RT
比如现在要用A机器上的日志对B机器进行预热，实现很简单：
步骤一、拿到A机器上的日志，%TOMCAT_HOME%/logs/catalina.2014-06-21.log
<!--more-->
搜索平台化中预热的需求描述为：当线上集群需要水平扩容时，新部署实例的机器需要用已存在的日志进行预查询，其目的是将用户的常用查询加载到扩容机内存中，最终对外开放时以提高缓存命中率，减少搜索RT
比如现在要用A机器上的日志对B机器进行预热，实现很简单：
步骤一、拿到A机器上的日志，%TOMCAT_HOME%/logs/catalina.2014-06-21.log
![4ea8c487-4f50-34e3-83fe-a851d6f3f260.png][1]
其中用关键字“param”过滤行文本，再以分隔符“{”或“}”解析后将第二个字段重定向到preload.log文件下，取前10万行处理结果得到preload.log如下
![0a9b5aef-fe51-30ec-b0df-8322eb80569c.png][2]
步骤二、将A机器上生成的preload.log文件scp到平台机，通过平台机读取文件并向B机器查询，代码如下

 1. 首先读取文件到到内存
代码如下：
```
    private List readPreloadFile(File preloadLog) { 
       List ret = Lists.newArrayList(); 
       BufferedReader br = null; 
       String s = null;
       try {
          br = new BufferedReader(new FileReader(preloadLog));
          while((s = br.readLine()) != null) { 
             ret.add(s);            
          } 
       } catch (IOException e) {                   
       } finally {                         
          if (br != null) {                      
             IOUtils.closeQuietly(br);            
          } 
       } 
       return ret;     
    }
```
考虑到通过NIO中的`MappedByteBuffer`直接将整个文件映射到内存可以避免一条条read的io开销，尝试如下

    private List readPreloadFile(File preloadLog) { 
        List logs = Lists.newArrayList();               
        FileChannel fc = null; 
        MappedByteBuffer fout = null; 
        byte[] ret = null;
    try {
        fc = new RandomAccessFile(preloadLog, "r").getChannel();     
        fout = fc.map(FileChannel.MapMode.READ_ONLY, 0, fc.size());
        ret = new byte[(int)fc.size()];
        fout.get(ret);  
    } catch (FileNotFoundException e) { 
    } catch (IOException e) { 
    } finally {
        fout = null; 
        if (fc != null) { 
            IOUtils.closeQuietly(fc); 
        } 
    } 
    logs = Arrays.asList(StringUtils.split(new String(ret), '\n'));    
    return logs;          
    
    }

实际对10万数据测试结果显示后者452ms，比前者305ms还差，发现主要还是耗在将byte数组转化为字符串（243ms）和split动作（164）ms
注意，以上两种方式都是一次性将日志文件的内容读到内存，现在10万条线上日志大约40M，考虑到后续会加大预热量以及同时预热多台机器，这样可直接导致OOM，所以更佳的方式应该从文件中读一条预热一条  

2. 将第一步返回的结果分解给多个线程去查询

1)定义callable类型的线程，返回该线程总共处理的查询次数

    class PreloadHandler implements Callable {                 
        private String name;        
        private String solrPath;        
        private List subQueryLogs;              
    private ThreadLocal<Integer> localDoneNum = new ThreadLocal<Integer>(){ 
        @Override           
        protected Integer initialValue() {              
            return 0;           
        } 
    };  
    
    private ThreadLocal<Integer> localErrNum = new ThreadLocal<Integer>(){  
        @Override           
        protected Integer initialValue() {              
            return 0;           
        } 
    };
        
    PreloadHandler(String name, String solrPath, List<String> subQueryLogs) {
        this.name = name;           
        this.solrPath = solrPath;           
        this.subQueryLogs = subQueryLogs;       
    }               
    
    public Integer call() throws Exception {                
        for (String queryLog : subQueryLogs) {              
            if (!isRunning) {                   
                break;              
            }                                   
            String url = solrPath + queryLog;               
            try {                   
                HttpUtil.httpGet(url); //真正查询处理     
            } catch (Exception e) {              
                errNum.getAndIncrement();       
                int _errNum = localErrNum.get(); 
                localErrNum.set(++_errNum);         
            }               
            doneNum.getAndIncrement();              
        int _doneNum = localDoneNum.get(); 
            localDoneNum.set(++_doneNum);
        }                       
        return localDoneNum.get(); 
    }


构造函数中传入三个参数，分别是线程名称，如“搜索实例名_机器B_handler_线程序号”；查询路径solrPath，如“http://机器B的ip:端口号port/solr/搜索实例名/select?”；分解后的子日志队列
localErrNum和localDoneNum是线程处理失败和完成的查询计数；errNum和doneNum是多个线程总的处理失败和完成的查询计数。前者定义为threadlocal，后者必须是atomic类型的

2)主线程中进行分解和子任务提交

    public void run() {                 
        isRunning = true;               
        String solrPath = String.format("http://%s:%s/solr/%s/select?",host.getInnerIp(), 
                                 port, instance.getInstanceName()); 
        <List> subList = Lists.partition(queryLogs, 
                                 queryLogs.size() / handlers); 
        futures = Lists.newArrayList();     
        for (int i = 0; i < handlers; i++) {
            futures.add(threadPool.submit( 
                new PreloadHandler(Joiner.on("_").join(instance.getInstanceName(),
                    host.getHostName(), "handler", (i + 1)), solrPath, subList.get(i))));
        }        
    }

3)因为异步的，页面触发action后就直接返回了。所以主线程中需要初始化一些状态信息并保存下来以供ajax查询

    private static Map statCache = Maps.newConcurrentMap();        
    public static void registor(String key, Preload preload) {
    
        statCache.put(key, preload);
    
    }
   
    public static void unRegistor(String key) {
    
        statCache.remove(key);
    
    }
    
    
    public void init() throws IOException {
    
    queryLogs = readPreloadFile(preloadLog);
    
    this.threadPool = Executors.newFixedThreadPool(handlers);
    
    totalNum = new AtomicInteger(queryLogs.size());
    
    doneNum = new AtomicInteger(0);
    
    errNum = new AtomicInteger(0);
    
    
    registor(
        Joiner.on("_").join(instance.getInstanceName(), host.getHostName()), this);
    
    }

并提供取消正在执行任务的能力

    public void cancel() {              
        isRunning = false;              
        for (Future f : futures) {             
            f.cancel(true);         
        }           
    }       
    public void close() {
    
    isRunning = false;
    
    if (threadPool != null) {
    
    threadPool.shutdown();
    
    }
    
    }

这里的isRunning变量必须是volatile的，因为当停止主线程时可以保证对子任务可见从而退出正在执行的子任务

3. 主线程类和“搜索实例+预热机器”进行绑定，外界通过如下查询预热状态
 

       public static Preload getPreload(String instanceName, String hostName) { 
            return statCache.get(Joiner.on("_").join(instanceName, hostName)); 
        }

综上所述便是整个Preload过程，下面给出预热一台机器的演示结果:
![ac624897-4961-3e10-a202-4fb67e7033f6.png][3]
![cb35fd6b-60aa-3231-81b2-c8c0c7b2b4ce.png][4]
总共50万条日志，5个线程并发预热，结果显示了每个线程处理的日志和错误数以及总的处理进度

作者：蘑菇街@不高，focus大数据、实时计算、分布式架构

  [1]: https://imgs.gnux.cn/usr/uploads/2014/11/863740546.png
  [2]: https://imgs.gnux.cn/usr/uploads/2014/11/590523239.png
  [3]: https://imgs.gnux.cn/usr/uploads/2014/11/2287445387.png
  [4]: https://imgs.gnux.cn/usr/uploads/2014/11/1306891003.png



