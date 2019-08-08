---
layout: post
title: 健康管理系统花絮(2.2 Mapper)
date: 2019-08-07 21:17:03
categories:
- 健康管理系统
tags:
- 健康管理系统
- Spring Boot
---
#### FoodMapper.java
``` java
@Mapper
public interface FoodMapper {

    /**
     * 创建食物
     * @param food
     * @return
     */
    @Insert("insert into food(name, description, food_energy) values(#{name}, #{description}, #{foodEnergy})")
    int insert(Food food);

    /**
     * 查询食物
     * @param id
     * @return
     */
    @Select("select id, name, description,foodEnergy as foodEnergy from food where id = #{id}")
    Food getById(int id);

    /**
     * 查询所有食物
     * @param
     * @return
     */
    @Select("select id, name, description,foodEnergy as foodEnergy from food")
    List<Food> getAll();

    /**
     * 更新食物
     * @param food
     * @return
     */
    int update(Food food);

    /**
     * 删除食物
     * @param id
     * @return
     */
    @Delete("delete from food where id = #{id};")
    int delete(int id);
}
```
#### SportMapper.java
``` java
@Mapper
public interface SportMapper {

    /**
     * 创建运动
     * @param sport
     * @return
     */
    int insert(Sport sport);

    /**
     * 查询运动
     * @param id
     * @return
     */
    @Select("select id, name, description,consumeEnergy as consumeEnergy from sport where id = #{id}")
    Sport getById(int id);

    /**
     * 查询所有运动
     * @param
     * @return
     */
    @Select("select * from sport")
    List<Sport> getAll();

    /**
     * 更新运动
     * @param sport
     * @return
     */
    int update(Sport sport);

    /**
     * 删除运动
     * @param id
     * @return
     */
    @Delete("delete from sport where id = #{id}")
    int delete(int id);
}
```
#### UserFoodHistoryMapper.java
``` java
@Mapper
public interface UserFoodHistoryMapper {

    // 记录用户饮食
    @Insert("insert into user_food_history (userId, foodId, foodQuantity, collectDate) " +
            "values (#{userId}, #{food.id}, #{foodQuantity}, #{collectDate})")
    public int insert(UserFoodHistory userFoodHistory);

    // 获取指定用户所用饮食记录
    @Select("select d,user_id,foodId,foodQuantity,collectDate from user_food_history where userId = #{userId}")
    public List<UserFoodHistory> getAll(int userId);

    // 获取指定用户每日的摄入能量
    @Select("select sum(foodQuantity * foodEnergy / 500) as sumFoodEnergy, collectDate " +
            "from user_food_history u join food f on u.foodId = f.id " +
            "where userId = #{userId} group by collectDate")
    public List<EnergyDate> getSumFoodEnergy(int userId);

}
```
#### UserIndexMapper.java
``` java
@Mapper
public interface UserIndexMapper {

    // 获取指定用户所有生理指标
    @Select("select id,indexType as indexType,indexContent as indexContent,collectDate as collectDate " +
            "from user_index where userId = #{userId}")
    List<UserIndex> getById(int userId);

}
```
#### UserSportHistoryMapper.java
``` java
@Mapper
public interface UserSportHistoryMapper {

    // 记录用户运动
    @Insert("insert into user_sport_history userId, sportId, sportTime, collectDate) " +
            "values (#{userId}, #{sport.id}, #{sportTime}, #{collectDate})")
    public int insert(UserSportHistory userSportHistory);

    // 获取指定用户所有运动记录
    @Select("select id,userId,sportId,sportTime,collectDate " +
            "from user_sport_history where userId = #{userId}")
    public List<UserSportHistory> getAll(int userId);

    //  获取指定用户每日的运动消耗能量
    @Select("select sum(sportTime * consumeEenergy) as sumConsumeEnergy, collectDate " +
            "from user_sport_history u join sport s on u.sportId = s.id " +
            "where userId = #{userId} group by collectDate")
    public List<EnergyDate> getSumConsumeEnergy(int userId);

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