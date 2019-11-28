### Introduction

In earlier days, suppose we have a web server (say Apache with port number 80) and there is a container named "www" and all the pre built HTMl files, images and so on are placed in that container (www)

Now, when a request from browser is made to access the HTML page (say localhost:80/myapp1/index.html), then index.html present in myapp1 which is placed in www container will be rendered to the browser, however the content that is rendered is STATIC data and this model could not handle dynamic data (taking input from the user and displaying data accordingly).

So, when need arose for dynamic content, JAVA proposed SERVLET as solution. Now we can says that it Webserver + servlet support.

Now, when a request from browser is made to render the HTML page (say localhost:80/myapp1/index.html), then index.html present in myapp1 which is placed in webapp container will be rendered to the browser, but with the help of servlets. for end user it doesn't make any difference, however at the backend to handle dynamic content display, "Deployment Descriptor (web.xml)" comes into picture and based on the url, it decides which servlet class will handle the request and calls that respective which process the request and gives the output accordingly.

> Note: Deployment Descriptor are nothing but web.xml where we define URL Pattern, servlet class name and other details.

> Note: One important thing to remember is that there is only ONE instance of every servlet gets created.

Now when multiple users access the same class what happens ?
There is a concept of Thread pool. say by default, 10 threads are available to handle the request, and say three different request comes then every request is assigned a thread to handle it.

All the three different thread will call the same class INSTANCE. Now any variable that is defined at class level can be accessed/manipulated by all the 3 different threads, so, for that reason, if its specific to the user, then it should be declared at method level (doGet, doPOST, and so on) and not at the class level.
Because a different stacks gets created for every method based on thread.
