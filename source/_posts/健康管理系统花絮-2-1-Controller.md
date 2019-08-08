---
layout: post
title: 健康管理系统花絮(2.1 Controller)
date: 2019-08-07 21:16:39
categories:
- 健康管理系统
tags:
- 健康管理系统
- Spring Boot
---
> 第二课验依然是用户端后台接口开发，主要有三大功能:
> - 记录用户饮食情况。
> - 记录用户运动情况。
> - 显示用户主要生理指标与用户饮食、运动关系，以此来指导用户合理饮食和运动。
#### FoodController.java
``` java
/ 标识controller，返回json数据，URL前缀/api/v1/food
@RestController
@RequestMapping("api/v1/food")
public class FoodController {

    // 自动注入service
    @Autowired
    private FoodService foodService;

    /**
     * 查询食物
     * @param id
     * @return
     */
    @GetMapping("get")
    public ResultObject getFoodById(@RequestParam int id) {
        Food food = foodService.getFoodById(id);
        ResultObject resultObject = new ResultObject(200, "success", food);
        return resultObject;
    }

    /**
     * 新建食物
     * @param food
     * @return
     */
    @PostMapping("add")
    public ResultObject insertFood(@RequestBody Food food) {
        int modifyId = foodService.insertFood(food);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 分页查询食物
     * @param pageNum, pageSize
     * @return
     */
    @GetMapping("list")
    public ResultObject getAllFood(@RequestParam int pageNum, @RequestParam int pageSize) {
        PaginationObject paginationObj = foodService.getAllFood(pageNum, pageSize);
        ResultObject resultObject = new ResultObject(200, "success", paginationObj);

        return resultObject;
    }

    /**
     * 删除食物
     * @param id
     * @return
     */
    @DeleteMapping("delete")
    public ResultObject deleteFood(@RequestParam int id) {
        int modifyId = foodService.deleteFood(id);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 更新食物
     * @param food
     * @return
     */
    @PutMapping("edit")
    public ResultObject updateFood(@RequestBody Food food) {
        int modifyId = foodService.updateFood(food);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }
}
```
#### SportController.java
``` java
// 标识controller，返回json数据，URL前缀/api/v1/sport
@RestController
@RequestMapping("api/v1/sport")
public class SportController {

    // 自动注入service
    @Autowired
    private SportService sportService;

    /**
     * 查询运动
     * @param id
     * @return
     */
    @GetMapping("get")
    public ResultObject getSportById(@RequestParam int id) {
        Sport sport = sportService.getSportById(id);
        ResultObject resultObject = new ResultObject(200, "success", sport);
        return resultObject;
    }

    /**
     * 新建运动
     * @param sport
     * @return
     */
    @PostMapping("add")
    public ResultObject insertSport(@RequestBody Sport sport) {
        int modifyId = sportService.insertSport(sport);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 分页查询运动
     * @param pageNum, pageSize
     * @return
     */
    @GetMapping("list")
    public ResultObject getAllSport(@RequestParam int pageNum, @RequestParam int pageSize) {
        PaginationObject paginationObject = sportService.getAllSport(pageNum, pageSize);
        ResultObject resultObject = new ResultObject(200, "success", paginationObject);
        return resultObject;
    }

    /**
     * 删除运动
     * @param id
     * @return
     */
    @DeleteMapping("delete")
    public ResultObject deleteSport(@RequestParam int id) {
        int modifyId = sportService.deleteSport(id);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }

    /**
     * 更新运动
     * @param sport
     * @return
     */
    @PutMapping("edit")
    public ResultObject updateSport(@RequestBody Sport sport) {
        int modifyId = sportService.updateSport(sport);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject(200, "success", map);
        return resultObject;
    }
}
```
#### UserFoodHistoryController.java
``` java
// 标识controller，返回json数据，URL前缀/api/v1/user_food_history
@RestController
@RequestMapping("/api/v1/user_food_history")
public class UserFoodHistoryController {

    // 自动注入service
    @Autowired
    private UserFoodHistoryService userFoodHistoryService;

    /**
     * 新建用户饮食记录
     * @param userFoodHistory
     * @return
     */
    @PostMapping("add")
    public ResultObject insertUserFoodHistory(@RequestBody UserFoodHistory userFoodHistory) {
        int modifyId = userFoodHistoryService.insertUserFoodHistory(userFoodHistory);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject();
        resultObject.setResult(map);
        if (modifyId == 1) {
            resultObject.setCode(1);
            resultObject.setMsg("success");
        } else {
            resultObject.setCode(-1);
            resultObject.setMsg("fail");
        }

        return resultObject;
    }

    /**
     * 查询用户所有运动历史记录
     * @param userId
     * @return
     */
    @GetMapping("get")
    public ResultObject getAllUserFoodHistory(@RequestParam int userId) {
        List<UserEverydayFoodSituation> userEverydayFoodSituations = userFoodHistoryService.getAllUserFoodHistory(userId);
        ResultObject resultObject = new ResultObject(200, "success", userEverydayFoodSituations);
        return resultObject;
    }
}
```
#### UserRelationshipController.java
``` java
// 标识controller，返回json数据，URL前缀/api/v1/relationship
@RestController
@RequestMapping("api/v1/relationship")
public class UserRelationshipController {

    // 自动注入service
    @Autowired
    private UserRelationshipService relationshipService;

    /**
     * 查询用户饮食、运动、生理指标关系
     * @param userId
     * @return
     */
    @RequestMapping("get")
    public ResultObject getRelationship(@RequestParam int userId) {
        UserRelationship relationship = relationshipService.getRelationship(userId);
        ResultObject resultObject = new ResultObject(0, "success", relationship);
        return resultObject;
    }
}
```
#### UserSportHistoryController.java
``` java
// 标识controller，返回json数据，URL前缀/api/v1/user_sport_history
@RestController
@RequestMapping("/api/v1/user_sport_history")
public class UserSportHistoryController {

    // 自动注入service
    @Autowired
    private UserSportHistoryService userSportHistoryService;

    /**
     * 新建用户饮食记录
     * @param userSportHistory
     * @return
     */
    @PostMapping("add")
    public ResultObject insertUserSportHistory(@RequestBody UserSportHistory userSportHistory) {
        int modifyId = userSportHistoryService.insertUserSportHistory(userSportHistory);
        Map<String, Integer> map = new HashMap<>();
        map.put("modifyId", modifyId);
        ResultObject resultObject = new ResultObject();
        resultObject.setResult(map);

        // 通过modifyid判断是否执行成功
        if (modifyId == 1) {
            resultObject.setCode(1);
            resultObject.setMsg("success");
        } else {
            resultObject.setCode(-1);
            resultObject.setMsg("fail");
        }

        return resultObject;
    }

    /**
     * 查询用户饮食历史
     * @param userId
     * @return
     */
    @GetMapping("get")
    public ResultObject getAllUserSportHistory(@RequestParam int userId) {
        List<UserEverydaySportSituation> userEverydaySportSituations = userSportHistoryService.getAllUserSportHistory(userId);
        ResultObject resultObject = new ResultObject(200, "success", userEverydaySportSituations);

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