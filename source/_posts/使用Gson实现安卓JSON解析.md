---
title: "使用Gson实现安卓JSON解析"
categories: [ "Android" ]
tags: [ "json","android","gson" ]
draft: false
slug: "the-use-of-gson-to-achieve-the-android-json-analysis"
date: "2013-11-20 11:18:00"
---

除了XML，JSON是一种很常见的API 响应中使用的格式。相比采用更冗长的XML，它的简单性已经获得了相当的赞成。此外，JSON可以很容易地与REST清晰且易于使用的API相结合。 Android基础包已经支持JSON，但是使用这些类，要在低的水平上开发，这是乏味和枯燥的。出于这个原因，在本教程中，将告诉你如何进行自动JSON解析。

这里使用 [Google Gson](http://code.google.com/p/google-gson/)框架来进行Json解

GSON是一个Java库，可用于将Java对象转换成JSON。它也可以被用于将一个JSON字符串转换为等效的Java对象。 GSON可以与任意Java对象，包括预先存在的对象或者你没有源代码结合。
已经有很多开源项目，可以转换Java对象到JSON。然而，他们中的大多数要求在你的类上使用元注释Annotation，如果你没有源码，将不能给代码加上Java这些元注解。大多数人也并不完全支持Java泛型的使用。 GSON认为这些是非常重要的设计目标。

在Eclipse 中创建AndroidJsonProject项目。
![android_json.jpg][1]
下面是带抓取的Json解析类：增强功能：
 1. 作为异步任务运行，与UI线程分离
 2. 处理 socket/网络timeouts
 3. 处理404错误
 4. 处理JSON语法错误
```
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.io.Reader;
    import java.util.List;
     
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.impl.client.DefaultHttpClient;
     
    import android.app.Activity;
    import android.content.Context;
    import android.os.AsyncTask;
    import android.os.Bundle;
    import android.util.Log;
    import android.view.View;
    import android.widget.Button;
    import android.widget.Toast;
     
    import com.google.gson.Gson;
    import com.google.gson.JsonIOException;
    import com.google.gson.JsonSyntaxException;
    import com.javacodegeeks.android.json.model.Result;
    import com.javacodegeeks.android.json.model.SearchResponse;
     
    public class JsonParsingActivity extends Activity {
     
        private static final String url = "http://search.twitter.com/search.json?q=jdon";
        protected InitTask _initTask;
     
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
           
            Button button = (Button)findViewById(R.id.button1);
            button.setOnClickListener(new View.OnClickListener() {
                public void onClick(View v) {
                    _initTask = new InitTask();
                    _initTask.execute( getApplicationContext() );
                }
            });
        }
     
        @Override
        public void onStop() {
            super.onStop();
            _initTask.cancel(true);
        }
     
        protected class InitTask extends AsyncTask<Context, String, SearchResponse>
        {
            @Override
            protected SearchResponse doInBackground( Context... params )
            {
                InputStream source = retrieveStream(url);
                SearchResponse response = null;
                if (source != null) {
                    Gson gson = new Gson();
                    Reader reader = new InputStreamReader(source);
                    try {
                        response = gson.fromJson(reader, SearchResponse.class);
                        publishProgress( response.query );
                    } catch (JsonSyntaxException e) {
                        Log.w(getClass().getSimpleName(), "Error: " + e.getMessage() + " for URL " + url);
                        return null;
                    } catch (JsonIOException e) {
                        Log.w(getClass().getSimpleName(), "Error: " + e.getMessage() + " for URL " + url);
                        return null;
                    } finally {
                        try {
                            reader.close();
                        } catch (IOException e) {
                            Log.w(getClass().getSimpleName(), "Error: " + e.getMessage() + " for URL " + url);
                        }
                    }
                }
                if (!this.isCancelled()) {
                    return response;
                } else {
                    return null;
                }
            }
     
            @Override
            protected void onProgressUpdate(String... s)
            {
                super.onProgressUpdate(s);
                Toast.makeText(getApplicationContext(), s[0], Toast.LENGTH_SHORT).show();
            }
     
            @Override
            protected void onPostExecute( SearchResponse response )
            {
                super.onPostExecute(response);
                StringBuilder builder = new StringBuilder();
                if (response != null) {
                    String delim = "* ";
                    List<Result> results = response.results;
                    for (Result result : results) {
                        builder.append(delim).append(result.fromUser);
                        delim="\n* ";
                    }
                }
                if (builder.length() > 0) {
                    Toast.makeText(getApplicationContext(), builder.toString(), Toast.LENGTH_SHORT).show();
                } else {
                    Toast.makeText(getApplicationContext(), "The response was empty.", Toast.LENGTH_SHORT).show();
                }
               
            }
           
            @Override
            protected void onCancelled() {
                super.onCancelled();
                Toast.makeText(getApplicationContext(), "The operation was cancelled.", 1).show();
            }
     
            private InputStream retrieveStream(String url) {
                DefaultHttpClient client = new DefaultHttpClient();
                HttpGet getRequest;
                try {
                    getRequest = new HttpGet(url);
                    try {
                        HttpResponse getResponse = client.execute(getRequest);
                        final int statusCode = getResponse.getStatusLine().getStatusCode();
                        if (statusCode != HttpStatus.SC_OK) {
                            Log.w(getClass().getSimpleName(), "Error " + statusCode + " for URL " + url);
                            return null;
                        }
                        HttpEntity getResponseEntity = getResponse.getEntity();
                        try {
                            return getResponseEntity.getContent();
                        } catch (IllegalStateException e) {
                            getRequest.abort();
                            Log.w(getClass().getSimpleName(), "Error for URL " + url, e);
                            return null;
                        } catch (IOException e) {
                            getRequest.abort();
                            Log.w(getClass().getSimpleName(), "Error for URL " + url, e);
                            return null;
                        }
                    } catch (ClientProtocolException e) {
                        getRequest.abort();
                        Log.w(getClass().getSimpleName(), "Error for URL " + url, e);
                    } catch (IOException e) {
                        getRequest.abort();
                        Log.w(getClass().getSimpleName(), "Error for URL " + url, e);
                    }
                } catch (IllegalArgumentException e) {
                    Log.w(getClass().getSimpleName(), "Error for URL " + url, e);
                }
                return null;
            }
     
        }
     
    }
```
注意：凡是可以抛出的异常已经被包围了一个try / catch块中，应用对此情况处理为返回null。
实现的onStop（）方法来取消异步Task，如果应用程序创造超过一个异步任务将导致强制终止关闭。
注意，我们检查'this.isCancelled'之前，我们完成doInBackground方法 - 你可以强制通过启动应用程序，并迅速击中后面的按钮。
可以抓住失败，然后重试一段时间。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/2846457047.jpg