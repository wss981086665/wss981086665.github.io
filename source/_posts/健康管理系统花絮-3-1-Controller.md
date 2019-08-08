---
layout: post
title: 健康管理系统花絮(3.1 Controller)
date: 2019-08-07 21:17:13
categories:
- 健康管理系统
tags:
- 健康管理系统
- Spring Boot
---
> 第三课主要是健康管理员端后台接口开发，主要功能有健康管理员对基本信息（姓名、性别、年龄、手机）的操作、用户和健康管理员转账的功能。 
#### AccountController.java
``` java
// 标记controller，返回json数据
@RestController
public class AccountController {

    // 自动注入service
    @Autowired
    private AccountService accountService;

    /**
     * 转账
     * @param transferDetail
     * @return
     */
    @PostMapping("transfer")
    public ResultObject add(@RequestBody TransferDetail transferDetail) {
        String status = accountService.transfer(transferDetail);
        ResultObject resultObject = new ResultObject(200, status, null);
        return resultObject;
    }
}
```
#### DoctorController.java
``` java
// 标记controller，返回json数据，URL前缀：/doctor
@RestController
@RequestMapping("doctor")
public class DoctorController {

    // 自动注入service
    @Autowired
    private DoctorService doctorService;

    /**
     * 创建健康管理员
     * @param doctor
     * @return
     */
    @PostMapping("add")
    public ResultObject add(@RequestBody Doctor doctor) {
        int modifyId = doctorService.insertDoctor(doctor);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 删除健康管理员
     * @param id
     * @return
     */
    @DeleteMapping("delete")
    public ResultObject delete(@RequestParam int id) {
        int modifyId = doctorService.deleteDoctor(id);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 更新健康管理员
     * @param doctor
     * @return
     */
    @PutMapping("modify")
    public ResultObject modify(@RequestBody Doctor doctor) {
        int modifyId = doctorService.updateDoctor(doctor);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 查询健康管理员
     * @param id
     * @return
     */
    @GetMapping("find")
    public ResultObject find(@RequestParam int id) {
        Doctor doctor = doctorService.selectDoctor(id);
        Map<String, Doctor> map = new HashMap<>();
        map.put("doctor", doctor);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }
}
```
#### UserController.java
``` java
// 标记controller，返回json数据，URL前缀：/user
@RestController
@RequestMapping("user")
public class UserController {

    // 自动注入service
    @Autowired
    private UserService userService;

    /**
     * 创建用户
     * @param user
     * @return
     */
    @PostMapping("add")
    public ResultObject add(@RequestBody User user) {
        int modifyId = userService.insertUser(user);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 删除用户
     * @param id
     * @return
     */
    @DeleteMapping("delete")
    public ResultObject delete(@RequestParam int id) {
        int modifyId = userService.deleteUser(id);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 更新用户
     * @param user
     * @return
     */
    @PutMapping("modify")
    public ResultObject modify(@RequestBody User user) {
        int modifyId = userService.updateUser(user);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 查询用户
     * @param id
     * @return
     */
    @GetMapping("find")
    public ResultObject find(@RequestParam int id) {
        User user = userService.selectUser(id);
        Map<String, User> map = new HashMap<>();
        map.put("user", user);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

}
```

**文集推荐:**
> [Java基础方法集1](https://www.jianshu.com/nb/35411761)
> [Python基础知识完整版](https://www.jianshu.com/nb/35412583)
> [Spring Boot学习笔记](https://www.jianshu.com/nb/35490047)
> [Linux指令进阶](https://www.jianshu.com/nb/35411158)
> [Java高并发编程](https://www.jianshu.com/nb/35701647)
> [SpringMVC基础知识进阶](https://www.jianshu.com/nb/36348245)
> [Mysql基础知识完整版](https://www.jianshu.com/nb/36768953)
> [健康管理系统学习花絮(学习记录)](https://www.jianshu.com/nb/36626677)
> [Node.js基础知识(随手笔记)](https://www.jianshu.com/nb/36852271)
> [MongoDB基础知识](https://www.jianshu.com/nb/36850994)
> [Dubbo学习笔记](https://www.jianshu.com/nb/36474207)
> [Vue学习笔记(随手笔记)](https://www.jianshu.com/nb/35411638)

> 声明：发表此文是出于传递更多信息之目的。若有来源标注错误或侵犯了您的合法权益，请作者持权属证明与本我们(QQ:981086665；邮箱:981086665@qq.com)联系联系，我们将及时更正、删除，谢谢。
