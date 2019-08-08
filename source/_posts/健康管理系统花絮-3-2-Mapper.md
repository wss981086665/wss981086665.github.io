---
layout: post
title: 健康管理系统花絮(3.2 Mapper)
date: 2019-08-07 21:17:23
categories:
- 健康管理系统
tags:
- 健康管理系统
- Spring Boot
---
#### AccountMapper.java
``` java
@Mapper
public interface AccountMapper {

    /**
     * 查询账户
     * @param userId
     * @return
     */
    @Select("select balance from account where userId = #{userId}")
    int select(int userId);

    /**
     * 更新账户
     * @param account
     * @return
     */
    int update(Account account);
}
```
#### DoctorMapper.java
``` java
@Mapper
public interface DoctorMapper {

    /**
     * 查询健康管理员
     * @param id
     * @return
     */
    @Select("select id, name, gender, education, certificateId from doctor where id = #{id}")
    Doctor select(int id);

    /**
     * 新建健康管理员
     * @param doctor
     * @return
     */
    @Insert("insert into doctor(name, gender, education, certificateId) values(#{name}, #{gender}, #{education}, #{certificateId})")
    int insert(Doctor doctor);

    /**
     * 更新健康管理员
     * @param doctor
     * @return
     */
    int update(Doctor doctor);

    /**
     * 删除健康管理员
     * @param id
     * @return
     */
    @Delete("delete from doctor where id = #{id}")
    int delete(int id);
}
```
#### UserMapper.java
``` java
@Mapper
public interface UserMapper {

    /**
     * 查询用户
     * @param id
     * @return
     */
    @Select("select id, name, gender, age, doctorId from user where id = #{id}")
    User select(int id);

    /**
     * 新建用户
     * @param user
     * @return
     */
    @Insert("insert into user(name, gender,age, doctor_id) values(#{name}, #{gender}, #{age}, #{doctor.id})")
    int insert(User user);

    /**
     * 更新用户
     * @param user
     * @return
     */
    int update(User user);

    /**
     * 删除用户
     * @param id
     * @return
     */
    @Delete("delete from user where id = #{id}")
    int delete(int id);
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