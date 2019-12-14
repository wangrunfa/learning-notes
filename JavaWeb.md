# Ajax总结

## Ajax作用

Ajax表示使用javascript收发来自web服务器的数据，且无须重载整个页面（仅刷新页面的局部）而无须刷新页面。这种技术允许开发人员创建不中断的应用程序，用新数据重载页面的某些部分。（**简单理解-->就是客户端与服务器端进行交互,而无需刷新当前页面的技术,称之为Ajax**）

## Ajax同步交互与异步交互的概念与不同

  \* 同步交互：客户端向服务器端发送请求,到服务器端进行响应,这个过程中,用户不能做任何其他事情(只能等待响应完才能继续其他请求).----同步可以避免死锁，避免脏读。	异步是非阻塞式

​       1、执行速度相对比较慢

​       2、响应的是完整的HTML页面



​     \* 异步交互：客户端向服务器端发送请求，直到服务器端进行响应，这个过程中，用户可以做其他任何事情(不等)；

​       1、 执行速度相对比较快

​       2、 响应的是部分数据

## Ajax核心对象

**XMLHttpRequest对象**（通过该对象向服务器发送请求。）

## JavaScript-Ajax （原生）

```HTML
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<!DOCTYPE HTML>
<html>
  <head>
    <title>Ajax原生</title>
  </head>
  <body>
    
	<a href="/项目名/ajax" >普通请求到后台</a>	  
	<a href="javascript:;" onclick="demoAjax()">AJAX请求到后台</a>	 
	<span class="show"></span> 
	<script type="text/javascript">
    	//创建 XMLHttpRequest，并做浏览器的兼容
	  	// var xhr = new XMLHttpRequest;
    	// 原本如上可以直接创建AJAX的XMLHttpRequest对象，当时为了兼容IE5/6，所以写法如下：
    	var xhr;
	  	function createXMLHttpRequest(){
	  		// 浏览器兼容
	    	if(window.XMLHttpRequest){
	    		// 通用的浏览器（ iE7 > 以上）
	    		xhr = new XMLHttpRequest; 
	    	}else{
	    	    // IE6, IE5 浏览器执行代码，ActiveXObject 此对象只有在IE浏览器中才会被识别
	    	    xhr=new ActiveXObject("Microsoft.XMLHTTP");
	    	}
	  	}
	  	
	  	// AJAX操作
	  	function demoAjax(){
	  		//　创建XMLHttpRequest
	  		createXMLHttpRequest(); 
	  		// 初始化基本参数，包括请求方式、请求服务器（servlet）的地址、是否同步（flase）异步（true）
	  		// 当为同步请求时，需等待当前ajax响应完成后，才可继续操作其他操作
            //xhr.open("请求方式", "/项目名/ajax", 同步或异步);
            //xhr.open("get", "/项目名/ajax?name="+1, true);
	  		xhr.open("post", "/项目名/ajax", true);
	  		// 判断状态,是否响应成功
	  		xhr.onreadystatechange=success;
	  		// post传递参数需要添加请求头
	  		xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded");
	  		// 发送（提交）参数，如果为post提交，则参数为提交的数据，如果为get提交，则参数为null即可。
	  		xhr.send("name=1&age=20");
	  	}
	  	
	  	function success(){
	  		// 表示请求已完成
	  		if(xhr.readyState==4){
		  		// 判断是否请求成功
		  		if(xhr.status==200){ 
		  			console.log(xhr.status);
		  			// 获取服务器返回的参数
		  			var text = xhr.responseText;
		  			alert(text);
		  		}
	  		}
	  	}
    </script>    
  </body>
</html>
```

## JQuery-Ajax

```HTML
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<!DOCTYPE HTML>
<html>
  <head>
    <title>My JSP 'index.jsp' starting page</title>
  </head>
  <body>
	<a href="/y04-ajax-demo/ajax" >普通请求到后台</a>	  
	<a href="javascript:;" id="get-ajax-btn">Jquery AJAX  get请求到后台</a>	 
	<a href="javascript:;" id="post-ajax-btn">Jquery AJAX  post请求到后台</a>	
	<a href="javascript:;" id="diy-ajax-btn">Jquery AJAX  自定义请求方式</a>	 
	<span class="show"></span> 
     //引入jquery.js文件
	<script type="text/javascript" src="https://code.jquery.com/jquery-1.11.3.min.js"></script></script>
	<script type="text/javascript">
		$("#get-ajax-btn").click(function(){
		  // jquery ajax 的get请求方式：$.get(url,callback)
		  $.get("/y04-ajax-demo/ajax?name=admin",function(data,status){
		     console.info("data:"+data);
		     console.info("status:"+status);
		  });
		});
		$("#post-ajax-btn").click(function(){
		  // jquery ajax 的post请求方式：$.post(url,data,callback)
		  $.post("/y04-ajax-demo/ajax",{"name":"postadmin","age":100},function(data,status){
		     console.info("data:"+data);
		     console.info("status:"+status);
		  });
		});
		//自定义请求
		$("#diy-ajax-btn").click(function(){
			$.ajax({
				url:"/y04-ajax-demo/ajax",
				type:"POST",
				data:{"name":"postadmin","age":100},
				async:true,
				// json,xml,text
				dataType:'json',
                    //请求成功执行
				success: function(result,status,xhr){
					console.info("result:"+result);
					console.info("status:"+status);
					console.info("xhr:"+xhr);
				},
				error: function(xhr,status,error){
					// 如果请求报错，就执行这里，一般不写
					console.info("xhr:"+xhr);
					console.info("status:"+status);
					console.info("error:"+error);
				}
				
			});
		});
    </script>    
  </body>
</html>

```

## Servlet-后台请求执行

```java
package com.ajax;


import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@WebServlet("/ajax")
public class AjaxTest extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("=======get=============");
        String str = req.getParameter("name");
        System.out.println(str);
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        //返回数据
        String strw = "get请求成功返回";
        out.write(strw);
        // 清空缓存区
        out.flush();
        // 结束
        out.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("========post============");
        String str = req.getParameter("name");
        String age = req.getParameter("age");
        System.out.println(str);
        System.out.println(age);

        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        //返回数据
        Map<Integer,Integer> maps = new HashMap<Integer,Integer>();
        List<Map<Integer,Integer>> lists=new ArrayList<>();
        for(int i=0;i<=20;i++){
            maps.put(i,i);
            if(i%4==0) {
                lists.add(maps);
                maps=new HashMap<Integer,Integer>();
            }
        }
        System.out.println(maps);
        System.out.println(lists);
        out.write("/"+lists+"/");
        // 清空缓存区
        out.flush();
        // 结束
        out.close();
    }
}

```

**Jar包：servlet-api.jar    路径：http://central.maven.org/maven2/servletapi/servlet-api/2.4/**

# 

# Servlet总结

## 什么是Servlet

 **Servlet（Server Applet），全称Java Servlet**。

**是用[java]编写的[服务器]端[程序]。其主要功能在于交互式地浏览和修改数据，生成动态[Web]内容**。

**Servlet 运行于支持Java的应用服务器中，从实现上讲可以相响应任何类型的请求，但绝大多数情况下，Servlet只用于扩展基于HTTP协议的web服务器。**



## Servlet流程图

![捕获](C:\Users\wangrunfa\Desktop\捕获.PNG)

