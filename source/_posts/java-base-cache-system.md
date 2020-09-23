---
title: [转]基于JAVA的简易缓存系统
date: 2014-11-08 15:40:00
updated: 2014-11-08 15:54:25
tags: 
- golang
categories: 
- go

---
### 基于JAVA的简易缓存系统 ###
#### 背景 ####
几乎任何一个应用，特别是WEB应用，缓存都是非常重要的一块内容，在过去的一段PHP开发中，比较常见的使用缓存的例子如下：

    1、根据一个key从缓存中取值
    2、如果取到值，则直接返回这个值
    3、如果没取到值，则根据业务从solr或是db或是某一段业务逻辑中取值，然后再把这个key-value存入缓存


<!--more-->


以上步骤在每一个需要的地方都进行一遍。逻辑其实没有问题，代码跑起来也是OK的，就是看起来有点ugly，不够优雅，扩展性也不好。

正好现在在做一个基于JAVA的WEB应用，可以对缓存做一次改造

#### 实现 ####
首先，缓存有很多种，本地堆内内存，堆外缓存，redis，memcached等等，所以很容易的会想到构建一个超类，并且有put, get等：

    public abstract class Cache {
    public abstract void put(String key, Object object);
    
    public abstract Object get(String key);
    
    }

这里假定key都是String类型的，另外应该还有一个缓存管理的功能，可以方遍查询缓存，删除缓存，清空缓存等，所以再加上几个方法：

     public abstract void remove(String key);
    public abstract void clear();
    
    /**
     * 获取缓存对象个数
     * @return
     */
    public abstract int getTotalNum();
    
    /**
     * 获取匹配的key
     * @param key
     * @return
     */
    public abstract List<String> getMatchKey(String key);

缓存大多是有有效期的，所以还需再加一个方法：

    public abstract void put(String key, Object object, long expire);

缓存基本也是保存二进制流的，方便存储，也避免对缓存的值进行修改，特别是本地缓存（JAVA是值传递），所以还需要有对象的序列化和反序列化：

        /**
         * 对象序列化
         * 
         * @param value
         * @return
         */
        protected byte[] value(Object value) {
            try {
                Kryo kryo = new Kryo();
                byte[] buffer = new byte[2048];
                Output output = new Output(buffer);
                kryo.writeClassAndObject(output, value);
                return output.toBytes();
            } catch (Exception e) {
                return null;
            }
        }
    /**
     * 对象反序列化
     * 
     * @param value
     * @return
     */
    protected Object object(byte[] value) {
        try {
            Kryo kryo = new Kryo();
            Input input = new Input(value);
            return kryo.readClassAndObject(input);
        } catch (Exception e) {
            return null;
        }
    }

这里是通过kryo进行对象序列化
另外，缓存是有有效期的，序列化后怎么判断有效期呢？在java中，long是8字节的，所以会想到将这个8字节的long转换为8个1字节的byte，新增方法如下：

        /**
         * 组装缓存对象
         * @param object
         * @param expire
         * @return
         */
        protected byte[] assembleValue(Object object, long expire) {
            byte[] value = value(object);
            byte[] cacheValue = new byte[value.length + 8];
            long expireTime = Long.MAX_VALUE;
            if (expire >= 0) {
                expireTime = System.currentTimeMillis() + expire;
            }
            cacheValue[0] = (byte) ((expireTime >> 56) & 0xFF);
            cacheValue[1] = (byte) ((expireTime >> 48) & 0xFF);
            cacheValue[2] = (byte) ((expireTime >> 40) & 0xFF);
            cacheValue[3] = (byte) ((expireTime >> 32) & 0xFF);
            cacheValue[4] = (byte) ((expireTime >> 24) & 0xFF);
            cacheValue[5] = (byte) ((expireTime >> 16) & 0xFF);
            cacheValue[6] = (byte) ((expireTime >> 8) & 0xFF);
            cacheValue[7] = (byte) ((expireTime) & 0xFF);
            System.arraycopy(value, 0, cacheValue, 8, value.length);
            return cacheValue;
        }
    /**
     * 拆解缓存对象
     * @param key
     * @param cacheValue
     * @return
     */
    protected Object disassembleValue(String key, byte[] cacheValue) {
        if (cacheValue == null) {
            return null;
        }
        long expireTime = ((long) (cacheValue[0] & 0xFF) << 56)
                  + ((long) (cacheValue[1] & 0xFF) << 48)
                  + ((long) (cacheValue[2] & 0xFF) << 40)
                  + ((long) (cacheValue[3] & 0xFF) << 32)
                  + ((long) (cacheValue[4] & 0xFF) << 24)
                  + ((long) (cacheValue[5] & 0xFF) << 16)
                  + ((long) (cacheValue[6] & 0xFF) << 8)
                  + ((long) (cacheValue[7] & 0xFF));
        if (expireTime >= System.currentTimeMillis()) {
            byte[] value = new byte[cacheValue.length - 8];
            System.arraycopy(cacheValue, 8, value, 0, value.length);
            return object(value);
        } else {
            remove(key);
            return null;
        }
    }

剩下的就是实现具体的子类了，这里给出本地堆内缓存的例子：

    @Component
    public class LocalCache extends Cache {
    //定义缓存类型
    public static final String CACHE_KEY = "local";
    //设置初始化容量
    private static final int CAPACITY = 512;
    //设置最大缓存个数
    private static final int WEIGHT = 10000;
    //存放byte[]类型主要是为了防止缓存数据遭到污染
    private static Map<String, byte[]> map = new ConcurrentLinkedHashMap.Builder<String, byte[]>().initialCapacity(CAPACITY).maximumWeightedCapacity(WEIGHT).build();
    private static LocalCache localCache = new LocalCache();
    
    private LocalCache() {
        
    }
    
    /**
     * 实现这个方法是为了防止新的缓存加进来时会修改原有的代码或是配置文件
     */
    @PostConstruct
    @Override
    public void init() {
        CacheContext.register(CACHE_KEY, localCache);
    }
    
    @Override
    public void put(String key, Object object, long expire) {
        byte[] cacheValue = assembleValue(object, expire);
        map.put(key, cacheValue);
    }
    
    @Override
    public Object get(String key) {
        byte[] cacheValue = map.get(key);
        return disassembleValue(key, cacheValue);
    }
    
    @Override
    public void remove(String key) {
        map.remove(key);
    }
    
    @Override
    public void clear() {
        map.clear();
    }
    
    @Override
    public int getTotalNum() {
        return map.keySet().size();
    }
    
    @Override
    public List<String> getMatchKey(String key) {
        List<String> list = new ArrayList<String>();
        Set<String> set = map.keySet();
        for (String s : set) {
            if (s.contains(key)) {
                list.add(s);
            }
        }
        return list;
    }
    
    }

为了避免缓存无限制增加，这里用到了ConcurrentLinkedHashMap，可以设置最大缓存个数，当超过这个限制时，会按照先进先出的方式删除掉最老的缓存。
CacheContext的代码如下：

    public class CacheContext {
    private static Map<String, Cache> cacheMap = new HashMap<String, Cache>();
    
    /**
     * 刷新本地线程，如果值为true,则不从缓存中取数据，数据取得之后会将新的数据写入缓存
     * 初始值false
     */
    public static ThreadLocal<Boolean> refreshFlag = new ThreadLocal<Boolean>() {
        protected Boolean initialValue() {
            return false;
        }
    };
    
    /**
     * 只读本地线程，如果值为true,则新的值不会写进缓存
     * 初始值false
     */
    public static ThreadLocal<Boolean> onlyReadCache = new ThreadLocal<Boolean>() {
        protected Boolean initialValue() {
            return false;
        }
    };
    
    /**
     * 获取具体的缓存
     * @param key
     * @return
     */
    public static Cache getCache(String key) {
        return cacheMap.get(key);
    }
    
    /**
     * 注册新的缓存
     * @param key
     * @param cache
     */
    public static void register(String key, Cache cache) {
        cacheMap.put(key, cache);
    }
    
    }

这里有两个ThreadLocal对象，主要是为了方便以后使用，有可能有些请求在特殊时期是需要取实时数据（例如人工刷新等），有些又是不需要更新缓存的，所以加了这2个对象
#### 使用 ####
以上只是缓存的一个简易实现，要怎么样才能避免类似php的那种ugly调用方式呢，使用java的同学很容易会想到利用动态代理，其中spring的AOP就是一个很好的方式。
首先自定义一个annotation（个人觉得annotation简直就是java开发的利器阿

    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    public @interface CacheData {
    //缓存时间，默认永久，单位毫秒
    long interval() default -1;
    
    //缓存类型，默认使用本地缓存
    String cacheType() default LocalCache.CACHE_KEY;
    
    }

这里可以根据具体的业务需求增加新的变量。
接下来，需要自定义一个aspect

    @Aspect
    @Component
    public class CacheAspect {
    /**
     * 
     * @param pjpParam
     * @return
     * @throws Throwable
     */
    @Around("@annotation(com.mogujie.cpcadmin.annotation.CacheData)")
    public Object doCache(ProceedingJoinPoint pjpParam) throws Throwable{
        Signature sig = pjpParam.getSignature();
        if(sig instanceof MethodSignature){
                Method method = ((MethodSignature) sig).getMethod();
                CacheData cacheData = method.getAnnotation(CacheData.class);
                Object[] args = pjpParam.getArgs();
                String cacheKey = method.getDeclaringClass().getName()+method.getName();
                for (Object object : args) {
                    if (object != null) {
                        cacheKey += object.toString();
                    } else {
                        cacheKey += "NULL";
                    }
                }
                Cache cache = CacheContext.getCache(cacheData.cacheType());
                Object value = null;
                if (!CacheContext.refreshFlag.get()) {
                    value = cache.get(cacheKey);
                }
                if (value == null) {
                    value = pjpParam.proceed();
                    if (!CacheContext.onlyReadCache.get()) {
                        cache.put(cacheKey, value, cacheData.interval());
                    }
                }
                return value;
        } else {
                return pjpParam.proceed();
        }
    }
    
    }

这里缓存的key结构如下：类名+方法名+参数值，在一般情况下是够用了，当然也可以采用另外的方式，比如在annotation里定义一个namespace或是另外的，根据业务需求来就好了。
最后，终于到了调用的地方啦，调用非常简单，如下：

    @CacheData
    public List getDistinctReport() {
        return molitongDailyReportDao.getDistinctReport();
    }

只要在方法的上面加上@CacheData，当然也可以设置有效期及使用哪个缓存，比如 @CacheData(interval=10000, cacheType="local")，这样就可以利用缓存了，AOP会拦截所有方法头带有@CacheData的方法，然后先从缓存里取值，当取不到值的情况下，才会执行方法里的逻辑，再把结果存入缓存中；如果取到值，则直接返回，不执行方法体内的逻辑。 ##扩展一 对于某些需要手动刷新的请求来讲，可以统一使用拦截器来实现这个功能：

    @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                Object arg2) throws Exception {
            if ("true".equalsIgnoreCase(request.getParameter("refreshFlag"))) {
                CacheContext.refreshFlag.set(true);
            }
            return true;
        }
配置上拦截器之后，只要在请求后带上refreshFlag=true，就可以手动刷新缓存了，另外，也可以针对清空所有缓存，查找指定缓存，删除指定缓存等写个专用action ##扩展二 一个团队中会有很多个应用，所以可以把这个缓存系统和其他比较核心的封装在一个单独的jar包中，上传到maven私服库，那其他项目就可以比较方便的利用这些东西了 ##最后的最后 有任何建议的，欢迎指教！
* 作者：蘑菇街北风  
* 作者简介：蘑菇街CPC团队攻城狮。
  擅长领域：java。
原文链接：[http://mogu.io/java-base-cache-system](http://mogu.io/java-base-cache-system)