---
title: Servlet中读取txt文本文件路径问题
date: 2018-02-10 13:56:32
tags: [Java]
categories: Java学习
copyright: true
toc: true
---

在进行javaweb开发中，遇到了一个问题，就是Servlet读取webcontent下的文本文件路径问题，会出现系统找不到文件的各种错误，经过查找问题，发现原来是由于这个项目部署在tomcat上之后，文件的目录结构会发生相应的一些变化，所以说使用常规的获取相对或绝对路径的方式在这个照常使用会导致运行之后，系统提示找不到文件路径的一些错误提示。
下面对如何在servlet中读取WEB-INF下的我存放的txt文件进行记录。

<!--more-->

下图为项目路径，而这里要实现的便是使用teacherProduce这一个Servlet来读取WEB-INF目录下的teacher1.txt文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190122091729965.png)
在这里说明一些获取文件路径的问题
常规的相对路径在这里无效，在tomcat上部署后目录结构变了
要使用getServletContext().getRealPath 来获取文件的路径。

```java
String pathname = this.getServletContext().getRealPath("/txt/teacher1.txt");
```
需要注意的是：getServletContext().getRealPath只能在servlet中使用。

完整代码如下

```java
public class teacherProduce extends HttpServlet {
	
	private static final long serialVersionUID = 1L;

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		request.setCharacterEncoding("UTF-8");		
		//读取文件
		String content = null;
		
		
		String pathname = this.getServletContext().getRealPath("/txt/teacher1.txt");
		
		String encoding = "UTF-8";
	    File file = new File(pathname);	    
	    System.out.println(file);
	    
	    BufferedReader br = null;
        try {
            InputStreamReader reader = new InputStreamReader(new FileInputStream(file));
             br = new BufferedReader(reader);
             String line = "";
             while ((line = br.readLine()) != null) {
                 System.out.println(line);
                 content = content +line;
             }
             
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            if(br != null){
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
	    		                
        request.setAttribute("content", content);	
		
		request.getRequestDispatcher("/personProduce/personProduce.jsp").forward(request, response);				
		
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doGet(request, response);
	}


}

```