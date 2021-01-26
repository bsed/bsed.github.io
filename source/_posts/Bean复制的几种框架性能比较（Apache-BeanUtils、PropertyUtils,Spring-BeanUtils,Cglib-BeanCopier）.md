---
title: "Bean复制的几种框架性能比较（Apache BeanUtils、PropertyUtils,Spring BeanUtils,Cglib BeanCopier）"
categories: [ "Java" ]
tags: [ "bean","cglib" ]
draft: false
slug: "bean-apache-propertyutils-beanutils-beanutils-spring-cglib-beancopier"
date: "2014-02-18 09:31:00"
---

  比较的是四种复制的方式，分别为Apache的BeanUtils和PropertyUtils,Spring的BeanUtils,Cglib的 BeanCopier。做法是在Eclipse新建了一个Project，专门用于专门测试几种代码的性能。具体的代码如下：
  一个FromBean和一个ToBean，两个的代码基本上一样，除了类名称不一样，所以只是贴出来了一份。


<!--more-->


    public class FromBean {
        private String name;
        private int age;
        private String address;
        private String idno;
        private double money;
      
        public double getMoney() {
            return money;
        }
      
        public void setMoney(double money) {
            this.money = money;
        }
      
        public String getName() {
            return name;
        }
      
        public void setName(String name) {
            this.name = name;
        }
      
        public int getAge() {
            return age;
        }
      
        public void setAge(int age) {
            this.age = age;
        }
      
        public String getAddress() {
            return address;
        }
      
        public void setAddress(String address) {
            this.address = address;
        }
      
        public String getIdno() {
            return idno;
        }
      
        public void setIdno(String idno) {
            this.idno = idno;
        }
      
    }

一个用于测试的BenchmarkTest类，为了减少重复代码，写了一个策略模式

    public class BenchmarkTest {
        private int count;
     
        public BenchmarkTest(int count) {
            this.count = count;
            System.out.println("性能测试" + this.count + "==================");
        }
     
        public void benchmark(IMethodCallBack m, FromBean frombean) {
            try {
                long begin = new java.util.Date().getTime();
                ToBean tobean = null;
                System.out.println(m.getMethodName() + "开始进行测试");
                for (int i = 0; i < count; i++) {
     
                    tobean = m.callMethod(frombean);
     
                }
                long end = new java.util.Date().getTime();
                System.out.println(m.getMethodName() + "耗时" + (end - begin));
                System.out.println(tobean.getAddress());
                System.out.println(tobean.getAge());
                System.out.println(tobean.getIdno());
                System.out.println(tobean.getMoney());
                System.out.println(tobean.getName());
                System.out.println("                                      ");
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

 策略中使用的接口声明

    public interface IMethodCallBack {
     
        String getMethodName();
     
        ToBean callMethod(FromBean frombean)  throws Exception;
     
    }

使用的测试类

    public class TestMain {
     
        /**
         * @param args
         */
        public static void main(String[] args) {
            FromBean fb = new FromBean();
            fb.setAddress("北京市朝阳区大屯路");
            fb.setAge(20);
            fb.setMoney(30000.111);
            fb.setIdno("110330219879208733");
            fb.setName("测试");
     
            IMethodCallBack beanutilCB = new IMethodCallBack() {
     
                @Override
                public String getMethodName() {
                    return "BeanUtil.copyProperties";
                }
     
                @Override
                public ToBean callMethod(FromBean frombean) throws Exception {
     
                    ToBean toBean = new ToBean();
                    BeanUtils.copyProperties(toBean, frombean);
                    return toBean;
                }
            };
     
            IMethodCallBack propertyCB = new IMethodCallBack() {
     
                @Override
                public String getMethodName() {
                    return "PropertyUtils.copyProperties";
                }
     
                @Override
                public ToBean callMethod(FromBean frombean) throws Exception {
                    ToBean toBean = new ToBean();
                    PropertyUtils.copyProperties(toBean, frombean);
                    return toBean;
                }
            };
     
            IMethodCallBack springCB = new IMethodCallBack() {
     
                @Override
                public String getMethodName() {
                    return "org.springframework.beans.BeanUtils.copyProperties";
                }
     
                @Override
                public ToBean callMethod(FromBean frombean) throws Exception {
                    ToBean toBean = new ToBean();
                    org.springframework.beans.BeanUtils.copyProperties(frombean,
                            toBean);
                    return toBean;
                }
            };
     
            IMethodCallBack cglibCB = new IMethodCallBack() {
                BeanCopier bc = BeanCopier.create(FromBean.class, ToBean.class,
                        false);
     
                @Override
                public String getMethodName() {
                    return "BeanCopier.create";
                }
     
                @Override
                public ToBean callMethod(FromBean frombean) throws Exception {
                    ToBean toBean = new ToBean();
                    bc.copy(frombean, toBean, null);
                    return toBean;
                }
            };
     
            // 数量较少的时候，测试性能
            BenchmarkTest bt = new BenchmarkTest(10);
            bt.benchmark(beanutilCB, fb);
            bt.benchmark(propertyCB, fb);
            bt.benchmark(springCB, fb);
            bt.benchmark(cglibCB, fb);
     
            // 测试一万次性能测试
            BenchmarkTest bt10000 = new BenchmarkTest(10000);
            bt10000.benchmark(beanutilCB, fb);
            bt10000.benchmark(propertyCB, fb);
            bt10000.benchmark(springCB, fb);
            bt10000.benchmark(cglibCB, fb);
     
            // 担心因为顺序问题影响测试结果
            BenchmarkTest bt1000R = new BenchmarkTest(10000);
            bt1000R.benchmark(cglibCB, fb);
            bt1000R.benchmark(springCB, fb);
            bt1000R.benchmark(propertyCB, fb);
            bt1000R.benchmark(beanutilCB, fb);
     
        }
     
    }

 进行了三次测试，最后的结果如下：

| 10次测验 | 第一次 | 第二次 | 第三次 | 平均值 | 每次平均值 |
| ----------- | --- | ------- | ------| -------- | --------- |
| BeanUtil.copyProperties | 54 | 57 | 50 | 53.66667 | 5.366666667 | 
| PropertyUtils.copyProperties | 4 | 4 | 4 | 4 | 0.4 | 
| org.springframework.beans.BeanUtils.copyProperties | 12 | 10 | 11 | 11 | 1.1 | 
| BeanCopier.create | 0 | 0 | 0 | 0 | 0 | 
 

| 10000次测验 | 第一次 | 第二次 | 第三次 | 平均值 | 每次平均值 | 
| ----------- | --- | ------- | ------| -------- | --------- |
| BeanUtil.copyProperties | 241 | 222 | 226 | 229.6667 | 0.022966667 | 
| PropertyUtils.copyProperties | 92 | 90 | 92 | 91.33333 | 0.009133333 | 
| org.springframework.beans.BeanUtils.copyProperties | 29 | 30 | 32 | 30.33333	| 0.003033333
| BeanCopier.create | 1 | 1 | 1 | 1 | 0.1 | 
 

| 10000次反转测验 | 第一次 | 第二次 | 第三次 | 平均值 | 每次平均值 | 
| ----------- | --- | ------- | ------| -------- | --------- |
| BeanUtil.copyProperties | 178 | 174 | 178 | 176.6667 | 0.017666667 | 
| PropertyUtils.copyProperties | 91 | 87 | 89 | 89 | 0.0089 | 
| org.springframework.beans.BeanUtils.copyProperties | 21 | 21 | 21 | 21 | 0.0021 | 
| BeanCopier.create | 0 | 1 | 1	0.666667 | 6.66667E-05 | 
 

不过需要注意的是，Cglib在测试的时候，先进行了实例的缓存，这个也是他性能较好的原因之一。如果把缓存去掉的话，性能就会出现了一些的差异，但是整 体的性能还是很好，不过奇怪的是10000次反而比10次少，而且后面的反转1万次反而耗时最少，进行多次测试效果也是如此。    从整体的表现来看，Cglib的BeanCopier的性能是最好的无论是数量较大的1万次的测试，还是数量较少10次，几乎都是趋近与零损 耗，Spring是在次数增多的情况下，性能较好，在数据较少的时候，性能比PropertyUtils的性能差一些。PropertyUtils的性能 相对稳定，表现是呈现线性增长的趋势。而Apache的BeanUtil的性能最差，无论是单次Copy还是大数量的多次Copy性能都不是很好。

10次	10000次	10000次反转
BeanCopier.create	41	28	10

性能测试就到这里，数据也展示如上，后续会继续编写剩余两篇文章，这一片关注性能，后面的一篇是就每种方式的使用上的差异进行详解，最后一篇想进行探讨是什么早就了这四种方式的性能差异。

来自：[http://www.cnblogs.com/kaka/archive/2013/03/06/2945514.html](http://www.cnblogs.com/kaka/archive/2013/03/06/2945514.html)