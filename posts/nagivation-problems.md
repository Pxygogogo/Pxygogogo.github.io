---
title: '微信小程序页面跳转的一系列注意点'
date: 2020-02-13 19:34:54
tags: [微信小程序]
published: true
hideInList: false
feature: 
isTop: false
---
1. showModel 回调无法跳转除TabBar之外的页面
   - 回调中只能使用switchTab进行跳转

2. switchTab跳转后页面不刷新

   - 法一：switchTab成功跳转后调用success,此时可以拿到跳转后页面的page对象,从而调用页面onLoad方法重载页面

     ```javascript
     wx.switchTab({
         url: '/pages/index/index',
         success: function(e) {
             let page = getCurrentPages().pop();
             if (page == undefined || page == null) return;
             page.onLoad();
         }
     })
     ```

    - 法二：当switchTab点击过的时候，只有第一次加载数据，第二次点击的时候是不刷新数据的，这个时候只要在需要每次点击都需要刷新数据的TabBar页的js里加上onShow的方法即可

       ```javascript
        onShow:function(e){
              this.onLoad();
        },
       ```

3. TabBar页面只能使用switchTab跳转

   - 文档里有写，使用时需注意

