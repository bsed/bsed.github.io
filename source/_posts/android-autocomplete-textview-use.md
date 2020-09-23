---
title: Android中AutoCompleteTextView使用小结
date: 2013-10-08 15:09:00
updated: 2014-12-25 15:20:41
tags: 
- php
- download
- utf8
categories: 
- php

---
[AutoCompleteTextView](http://developer.android.com/reference/android/widget/AutoCompleteTextView.html)这个控件用于输入框的自动完成提示，非常适合搜索框等。它本质上是个`EditText`，实际上它也是从`EditText`继承的，使用起来也十分简单。


<!--more-->


使用要点如下：

 1. 利用`ListAdapter`（一般使用ArrayAdapter）为`AutoCompleteTextView`提供数据，若有需要还可以重载`getView()`以自定义列表项的显示方式。这一步同`ListView`。

 2. 如果需要根据输入内容进行动态提示，那么`adapter`需要实现Filterable接口，重载getFilter()函数实现提示算法。getFilter()需返回Filter对象，该对象包含至少两个方法：`performFiltering()`在后台执行过滤，`publishResults()`在UI线程执行，负责将过滤结果显示到列表中。还有第三个方法`convertResultToString()`可以控制用户点击提示时要填充至输入框的文本内容。

示例代码大致如下。

    AutoCompleteTextView autoComplete = new AutoCompleteTextView(context);
    autoComplete.setThreshold(2);      // 设置最少几个字符触发自动完成
    MyAdapter adapter = new MyAdapter(context);
    autoComplete.setAdapter(adapter);
    
    /**
     * Adapter定义
     */
    class MyAdapter extends ArrayAdapter<MyObject> implements Filterable{
    
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            // define your list item view here 在此处定义列表项的视图
        }
    
        /**
         * 实现自动完成的过滤算法
         */
        @Override
        public Filter getFilter() {
            Filter filter = new Filter() {
    
                /**
                 * 本方法在后台线程执行，定义过滤算法
                 */
                @Override
                protected FilterResults performFiltering(CharSequence constraint) {
                    String keyword = String.valueOf(constraint).toLowerCase();
                    // 此处实现过滤
    
                    // 过滤后利用FilterResults将过滤结果返回
                    FilterResults filterResults = new FilterResults();
                    filterResults.values = results;   // results是上面的过滤结果
                    filterResults.count = results.size();  // 结果数量
    
                    return filterResults;
                }
    
                /**
                 * 本方法在UI线程执行，用于更新自动完成列表
                 */
                @Override
                protected void publishResults(CharSequence constraint, FilterResults results) {
                    if (results != null && results.count > 0) {
                        // 有过滤结果，显示自动完成列表
                        MyAdapter.this.clear();   // 清空旧列表
                        MyAdapter.this.addAll((List<MyObject>)results.values);
                        notifiDataSetChanged();
                    } else {
                        // 无过滤结果，关闭列表
                        notifyDataSetInvalidated();
                    }
                }
    
                /**
                 * 如果需要控制提示文本的显示方式，重载此函数
                 */
                @Override
                public CharSequence convertResultToString(Object resultValue) {
                    MyObject obj = (MyObject) resultValue;
                    return obj.name;
                }
    
            };
        }
    }