---
title: SpringBoot学习笔记8(过滤器、监听器、拦截器)
date: 2019-05-04 10:24:47
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
---
### 过滤器介绍
> filter是servlet规范中定义的一种特殊类。用于对指定的请求进行过滤，然后将请求交给对应的servlet进行处理并生成响应，最后filter再对服务器响应进行后处理，可以实现访问权限控制、过滤敏感词汇、压缩响应信息等功能。

### 监听器介绍
> listener也是servlet规范中定义的一种特殊类。用于监听servletContext、HttpSession和servletRequest等域对象的创建和销毁事件。在事件发生前后做一些必要的处理，可以实现在线人数统计等功能。

### 拦截器介绍
> interceptor在面向切面编程（Aspect-Oriented Programming，AOP）中用于在某个方法或字段被访问之前进行拦截，然后在处理前后加入某些功能，例如：日志，安全等功能。一般拦截器方法都是通过动态代理的方式实现，可以通过它来进行权限验证、判断用户是否登陆、日志记录。
#### 下面是一个简单的实例:
![文件夹结构.png](https://upload-images.jianshu.io/upload_images/13687958-d65dcac34d9d734a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### FilterConfig.java
``` java
// 标识本类为配置类
@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean<LoginFilter> registFilter() {
        // 创建注册器
        FilterRegistrationBean<LoginFilter> registration = new FilterRegistrationBean<LoginFilter>();
        // 设置过滤器
        registration.setFilter(new LoginFilter());

        // 设置属性exclusions，不需要过滤的URL
        registration.addInitParameter("exclusions", "/user/login");

        // 添加过滤规则
        registration.addUrlPatterns("/*");
        // 过滤器设置名称
        registration.setName("LoginFilter");
        // 设置过滤器顺序，值越小优先级越高
        registration.setOrder(1);

        return registration;
    }
}
```
#### InterceptorConfig.java
``` java
// 标识本类为配置类
@Configuration
public class InterceptorConfig extends WebMvcConfigurationSupport{

    @Autowired
    private WordInterceptor wordInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 注册敏感词汇拦截器，设置拦截路径
        registry.addInterceptor(wordInterceptor).addPathPatterns("/user/search");

        super.addInterceptors(registry);
    }
}
```
#### ListenerConfig.java
``` java
// 标识本类为配置类
@Configuration
public class ListenerConfig {

    @Bean
    public OnlineListener init() {
        return new OnlineListener();
    }
}
```
#### UserController.java
``` java
@RestController
@RequestMapping("user")
public class UserController {

    // 统计在线用户数，请求方法类型get，URL：/user/online
    @GetMapping("online")
    public ResultObject setSession(HttpServletRequest request,
                                   HttpServletResponse response) {
        // 获取session
        HttpSession session = request.getSession();
        // 获取sessionid，即JSESSIONID
        String sessionId = session.getId();
        System.out.println(sessionId);

        // 获取session属性
        Object onlineNumber = session.getServletContext().getAttribute("onlineNumber");
        System.out.println(onlineNumber);
        // 返回结果
        return new ResultObject(onlineNumber);
    }

    // 登录验证用户，请求方法类型post，URL：/user/login
    @PostMapping("login")
    public ResultObject login(@RequestBody User user, HttpServletRequest request, HttpServletResponse response) {

        // 正确账号密码，模拟数据库中的信息
        User realUser = new User("jacky", "112233");
        // 判断账号、密码是否正确
        if (realUser.equals(user)) {
            // 获取session，获取不到则新建
            HttpSession session = request.getSession();

            // 获取sessionid，即JSESSIONID
            String sessionId = session.getId();
            System.out.println(sessionId);

            // 设置session属性user
            session.setAttribute("user", user);

            return new ResultObject("login success");
        } else {
            return new ResultObject(-1, "fail", "login fail");
        }
    }

    // 搜索功能，过滤敏感词汇，请求方法类型get，URL：/user/search
    @GetMapping("search")
    public ResultObject search(@RequestParam String word) {
        // 若关键字是敏感词，在拦截器中已经被拦截不可能走到这里
        return new ResultObject("search word " + "'" + word + "'" + " is valid.");
    }
}
```
#### ResultObject.java
``` java
public class ResultObject {

    // 后台返回码
    private int code;
    // 后台返回消息
    private String msg;
    // 结果
    private Object result;

    // 构造函数
    public ResultObject() {
        super();
        // TODO Auto-generated constructor stub
    }

    // 构造函数
    public ResultObject(int code, String msg, Object result) {
        super();
        this.code = code;
        this.msg = msg;
        this.result = result;
    }

    // 后台处理成功时使用的构造函数
    public ResultObject(Object result) {
        super();
        this.code = 0;
        this.msg = "success";
        this.result = result;
    }

    // 属性的setter、getter方法
    public int getCode() {
        return code;
    }
    public void setCode(int code) {
        this.code = code;
    }
    public String getMsg() {
        return msg;
    }
    public void setMsg(String msg) {
        this.msg = msg;
    }
    public Object getResult() {
        return result;
    }
    public void setResult(Object result) {
        this.result = result;
    }
    @Override
    public String toString() {
        return "ResultObject [code=" + code + ", msg=" + msg + ", result=" + result + "]";
    }
}
```
#### User.java
``` java
public class User{

    // 用户名称
    private String username;
    // 用户密码
    private String password;

    // 构造函数
    public User() {
        super();
        // TODO Auto-generated constructor stub
    }

    // 构造函数
    public User(String username, String password) {
        super();
        this.username = username;
        this.password = password;
    }

    // 属性的setter、getter方法
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }

    // 重写toString方法
    @Override
    public String toString() {
        return "Book [username=" + username + ", password=" + password + "]";
    }

    // 重写hashCode方法
    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + ((password == null) ? 0 : password.hashCode());
        result = prime * result + ((username == null) ? 0 : username.hashCode());
        return result;
    }

    // 重写equals方法，便于直接比较user对象
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        User other = (User) obj;
        if (password == null) {
            if (other.password != null)
                return false;
        } else if (!password.equals(other.password))
            return false;
        if (username == null) {
            if (other.username != null)
                return false;
        } else if (!username.equals(other.username))
            return false;
        return true;
    }
}
```
#### LoginFilter.java
``` java
public class LoginFilter implements Filter {

    // 存储不需要过滤的URLs,一个对象
    private static String exclusions;
    // 存储不需要过滤的URLs,exclusions分解而来的字符串数组
    private static String[] ALLOWEDURLS;

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // TODO Auto-generated method stub
        // 获取在filterconfig中配置好的，不需要过滤的URLs
        exclusions = filterConfig.getInitParameter("exclusions");
        if (!exclusions.isEmpty()) {
            // 获取不需要过滤的URLs，可以直接访问的URLs
            ALLOWEDURLS = exclusions.split(",");
        }
    }

    @Override
    public void doFilter(ServletRequest servletRequest,
                         ServletResponse servletResponse, FilterChain chain)
            throws IOException, ServletException {

        // 强转
        HttpServletRequest request = (HttpServletRequest)servletRequest;
        HttpServletResponse response = (HttpServletResponse)servletResponse;

        // 获取当前请求的URL
        String url = request.getRequestURI();

        // 获取session，false表示如果没有获取到也不会创建
        HttpSession session = request.getSession(false);
        // 如果当前请求的URL不需要过滤或者已经登录的用户的请求，则继续处理
        if (isAllowed(url) || session != null && session.getAttribute("user") != null) {
            chain.doFilter(request, response);
        } else {
            // 若用户没有登录，否则直接返回
            render(response, "not login");
        }
    }

    @Override
    public void destroy() {
        // TODO Auto-generated method stub

    }

    // 当前请求是否需要过滤
    public boolean isAllowed(String url) {
        for (String ALLOWEDURL: ALLOWEDURLS) {
            if (ALLOWEDURL.equals(url)) {
                return true;
            }
        }
        return false;
    }

    // 返回请求失败消息
    public void render(HttpServletResponse response, String msg) throws IOException {
        // 构造返回消息
        ResultObject resultObject = new ResultObject(-1, "fail", msg);

        // 生成json数据
        JSONObject object = new JSONObject(resultObject);

        // 返回json数据
        response.setContentType("application/json;charset=UTF-8");
        OutputStream out = response.getOutputStream();
        out.write(object.toString().getBytes("UTF-8"));
        out.flush();
        out.close();
    }

}
```
#### WordInterceptor.java
``` java
@Component
public class WordInterceptor implements HandlerInterceptor{

    // 设置敏感词汇
    private static final String[] forbitWords = {"apple", "pear", "peach", "Lemon "};

    // 处理前方法
    @Override
    public boolean preHandle(HttpServletRequest request,
                             HttpServletResponse response, Object handler) throws Exception {
        // 获取请求中的参数word
        String word = request.getParameter("word");
        // 与敏感词汇对比
        for (String forbitWord: forbitWords) {
            if (word.equals(forbitWord)) {
                render(response, "search word " + "'" + word + "'" + " is not allowed.");
                // 返回假，中断请求
                return false;
            }
        }

        // 返回真，继续处理请求
        return true;
    }

    // 处理后方法
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                           ModelAndView modelAndView) throws Exception {
    }

    // 完成后方法
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
    }

    // 返回失败消息
    public void render(HttpServletResponse response, String msg) throws IOException {
        ResultObject resultObject = new ResultObject(-1, msg, null);

        JSONObject object = new JSONObject(resultObject);

        response.setContentType("application/json;charset=UTF-8");
        OutputStream out = response.getOutputStream();
        out.write(object.toString().getBytes("UTF-8"));
        out.flush();
        out.close();
    }
}
```
#### OnlineListener.java
``` java
@WebListener
public class OnlineListener implements HttpSessionListener{

    // 记录当前登录用户数
    private int onlineNumber = 0;

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        // 监听到创建了新的session，在线用户累加1
        onlineNumber++;
        // 设置session属性
        se.getSession().getServletContext().setAttribute("onlineNumber", onlineNumber);
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        // 监听到销毁了旧的session，在线用户减少1
        onlineNumber--;
        // 设置session属性
        se.getSession().getServletContext().setAttribute("onlineNumber", onlineNumber);
    }

}
```