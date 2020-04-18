---
title: 'Mongoose得到的对象不能增加属性解决方法（两种）'
date: 2020-03-11 12:39:32
tags: [mongoose]
published: true
hideInList: false
feature: 
isTop: false
---
## 实例
- Notice模型文件如下：
```
const mongoose = require('mongoose');

const schema = mongoose.Schema({
    userId:{type:String},
    time: {type:String},
    medicines: {type:Array},
    noticePerson:{type:String},
    acrtTime:{type:String},
});

module.exports = mongoose.model('Notice',schema);
```
- 路由响应如下：

```
router.post('/notices', async (req, res) => {
            const {email} = await User.findById(req.id, {email: 1});
            const result = await Notice.create(req.body);
            result.email = email; //无法生效
            res.send(result);
        }
    });
```

- 问题描述：
希望向得到的result数据中添加一个email属性，但直接添加无法成功


---

## 原因
- 因为Mongoose是個ODM (Object Document Mapper)，类似于操作关系型数据库使用的ORM(Object Relational Mapper)，我们使用Mongoose取到的数据的结构是要依赖于我们定义的schema结构的。增加的email属性在schema中没有定义，所以我们在取到的结果中增加email属性是无效的


---

## 解决办法

- 解决办法一

==在schema中直接增加需要补充的属性==

在本例中即向Notice模型中添加一个email字段

- 解决办法二

==把查询到的结果clone一个对象，然后在新对象中补充属性==


```
router.post('/notices', async (req, res) => {
            const {email} = await User.findById(req.id, {email: 1});
            const result = await Notice.create(req.body);
            const data = {
                ...result._doc,
                email,
            };
            res.send(data);
        }
    });
```
