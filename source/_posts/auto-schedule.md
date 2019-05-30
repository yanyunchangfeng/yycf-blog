---
title: 自动排班算法的设计与实现（日历算法）
date: 2019-04-23 22:49:45
categories :
         - Angular
         - JavaScript
tags: 
    - 自动排班算法
    - 日历算法
---

你好，我是徐晓东，笔名**燕云长风**。大漠穷秋于 2019-03-16 21:22 赠此笔名。
寓意：结合李白著名的边塞诗《关山月》取【燕云长风】—— **长风几万里，吹度玉门关。**

写这篇文章之前，酝酿了很久，希望把自己之前遇到的问题及解决方案分享给大家。

那年深秋，我接到了一个开发任务——XXX市公安交通智能化管控系统的排班管理系统。

最终我选择了angular技术栈来实践，天下武功，唯快不破。

为了大家更加直观了解，我截了几张运行效果图：

![排班主页面](https://user-gold-cdn.xitu.io/2019/5/5/16a8744e6c7a37eb?w=3360&h=1878&f=jpeg&s=449789)
<p align="center" style="background-color:gray;color:#fff">排班主页面</p>
    

![选择排班人员](https://user-gold-cdn.xitu.io/2019/5/5/16a8745491856669?w=3360&h=1882&f=jpeg&s=252138)

<p align="center" style="background-color:gray;color:#fff">选择排班人员</p>

![生成的排班人员](https://user-gold-cdn.xitu.io/2019/5/5/16a87456ec805307?w=3360&h=1882&f=png&s=174922)

<p align="center" style="background-color:gray;color:#fff">生成的排班人员</p>

好了，话不多说，我们直接看核心需求（干货）

1.自定义前端日历UI组件库(日历算法)

2.实现自动排班算法(自己设计实现自动排班算法)

日历算法的实现:

前置知识：

我们知道一个月最多跨6周，即6＊7格式

本月第一天 : fistDay : new Date(year, month–1 , 1)

本月最后一天: lastDay: new Date(year, month, 0) 下月的第0天即本月最后一天

上月最后一天: lastDayofLastMonth : new Date(year, month–1 ,0) 本月第0天即上月最后一天

了解了这些知识后，下面我们开始上代码，编写日历算法:
```
/**  
 * [getMonthData 计算日历的方法]
 * @param {number} year  [年]
 * @param {number} month [月]
 * @return {DateRet[]}   [返回的日历数据]
 */

 export interface DateRet {
     year:number,
     month:number,
     date:number,
     showDate:number,
 }
 const getMonthData = (year?:number , month?:number):DateRet[] => {
    //定义ret变量用来保存最后的结果集
    let ret:any[] = [];
    if( !year || !month){
        let date = new Date();
        year = date.getFullYear();
        month = date.getMonth()+1; //月份修正
    }
    //获取当前月的第一天，用于计算上月预留天数
    let firstDayOfMonth = new Date(year, month-1, 1);
    let preMonthDay =  firstDayOfMonth.getDay();
    //获取上月的最后一天，本月第0天即为上月最后一天
    let lastDayOfLastMonth = new Date(year, month-1, 0);
    let lastDateOfLastMonth = lastDayOfLastMonth.getDate();
    //获取本月最后一天，下月第0天即为本月最后一天
    let lastDayOfMonth = new Date(year, month, 0);
    let lastDateOfMonth = lastDayOfMonth.getDate();
    
    for(let i = 0; i < 6*7; i++){
        // 获取当前排序的日期数,+1是为了修正
        let thisYear:number = year ,
            thisMonth:number = month, 
            date:number = i+1-preMonthDay,
            showDate:number = date;
        if(date <= 0){
            //如果date小于等于0，则说明是上月预留天数，月和日都要加以修正
            thisMonth -= 1;
            showDate = date + lastDateOfLastMonth;
        }else if(date > lastDateOfMonth){
            //如果date大于本月最后一天说明为下月预留天数，月和日加以修正
            thisMonth += 1;
            showDate = date - lastDateOfMonth;
        }
        // 修正年月，因为当月份为1时，上月减1为0,需要同时修正月和年
        // 当月份为12时，下月加1为13,需要同时修正年和月
        if(thisMonth === 13) {thisMonth = 1 , thisYear += 1}
        if(thisMonth === 0) {thisMonth = 12 , thisYear -= 1}
        ret.push({
            thisYear,
            thisMonth,
            date,
            showDate
        })
    }
    return ret 
 }
 export default getMonthData

 有日历算法作为基础，我们就开始设计自动排班算法了，因为自动排班算法是基于日历算法设计的
        处长每日预排班两人算法
     const test = ['test1','test2','test3','test4','test5'];
     // 待排班人员
     let days = 31;本月共计天数
     let preUsernum = 2 ;每天排班两人
     let totalNum= days * preUsernum 总人数
     let planUser = new Array(totalNum)
     let num=3;//从第几个开始排
     for(let i = 0;i < totalNum; i++){
            if (num < test.length){
                planUser[i] = test[num++]
            }else{
                num = 0;
                planUser[i] = test[num++]
           }
      }
      for(let i = 1; i <= days; i++){
         console.log (planUser[(i – 1) * 2],  planUser[i * 2 – 1])
         //依次取出 planUser数组中下标（0 1），（ 2 3 ）... 实现两人自动排班算法
      }
```

如果你需要更全面的代码，请访问 [duty-manage](https://gitee.com/yanyunchangfeng/duty-manage)

这是在线演示地址，[duty-manage](https://yanyunchangfeng.gitee.io/duty-manage)

## 我参与的系列项目

1. [NiceFish]( https://gitee.com/mumu-osc/NiceFish)：美人鱼，这是一个微型Blog系统，前端基于Angular7.0 + PrimeNG7.1.0。（GVIP 码云最有价值的开源项目 3160 ☆)
2. [NiceFish-React]( https://gitee.com/mumu-osc/NiceFish-React)：这是React版的实现，和 NiceFish Angular 版本保持风格一致。采用React Hooks 16.8.3 版本，使用TypeScript、Ant Design组件库以及Bootstrap v4.2.1 开发。  (7 ☆)
3. [OpenWMS-Frontend](https://gitee.com/mumu-osc/OpenWMS-Frontend)：OpenWMS项目前端基于 Angular 7.0 + PrimeNG 7.1.0。  (已推荐 199 ☆)
4. [nicefish-spring-cloud](https://gitee.com/mumu-osc/nicefish-spring-cloud)：这是NiceFish的服务端代码，基于SpringCloud。已经完成了一些基本的功能，如 SpringSecurity+OAuth2+JWT 实现SSO，文章、用户、评论等的分页查询等。如果你需要与这个后端代码进行对接，请检出本项目的 for-spring-cloud 分支。 (已推荐 113 ☆)

## 我的社交主页  

1. [燕云长风知乎](https://zhihu.com/people/hbxyxuxiaodong)  
2. [燕云长风知乎专栏](https://zhuanlan.zhihu.com/yanyunchangfeng)  
3. [燕云长风github](https://github.com/yanyunchangfeng)  
4. [燕云长风gitee](https://gitee.com/yanyunchangfeng)  
5. [燕云长风twitter](https://twitter.com/yanyunchangfeng)  
6. [燕云长风medium](https://medium.com/@yanyunchangfeng)   

今天的分享就到这里，祝大家顺利，工作愉快，天天开心。

长风几万里，吹度玉门关。