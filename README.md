# api
---

## 全局
出错返回非正常的httpcode，错误信息为：
```
{
    errorCode:[int],
    message:[string]
}
```
api中所有url放在`${host}/api/`域名下。并包含如下cookie：
```
{
    languange:'zh'|'en',
    token:'xxx',
}
```

## 账户
### 登陆
```
@Get user/login
@Query {userId,password,remember}
@Return {
    userId: [string] 用户名,
    nickname:昵称,
    signature:用户签名,
    avatarUrl:头像地址,
    classname:班级,
    email:邮箱
}
```


### 注册
```
@Post user/register
@Body {
    userId:用户名,
    nickname:昵称,
    signature:用户签名,
    classname:班级,
    email:邮箱,
    avatar: [file] 上传图片
}
@Return {
    userId:[string] 用户名
}
```

### 注销
```
@Put account/logout
@Return {}
```

## 用户
### 获得用户排名 (ranklist)
```
@Get users
@Query {
    userId:[...],
    classname:[...],
    ...
    size,
    page
}
@Return {total:10,list:[{
    rank: [int] 排第几名,
    userId:用户名,
    nickname:昵称，
    signature，
    classname,
    static:{
        ac:[int] AC题数
        submit:[int] 提交数 
    }
}]}
```
### 获得用户详情
```
@Get users/:userId
@Return {
    nickname,
    email,
    signature,
    avatarUrl,
    classname,
    solved:[{problemId}] //ac的题目列表
}
```
## 题目
### 题目列表
```
@Get problems
@Query {
    size,
    page,
    ...其他过滤参数,eg:
    title:'A+B',
    problemId:[1,2,3]
}
@Return {
    total:1，与请求参数size对应的总页数
    list:[{
        problemId,
        title,
        tags:['Math','Dp'...]//题目标签
        difficulty，//难易度
        static:{
            ac:[int] AC题数
            submit:[int] 提交数 
        }
        date // 更新时间
    }]
}
```

### 题目详情
```
@Get problems/:problemId
@Return {
    title,
    tags:[...],
    difficulty,
    timelimit:{java,others},
    memorylimit:{java, others}
    description,
    input,
    output,
    sampleInput,
    sampleOutput,
    hint,
    source,
    static:{
        ac:[int] AC题数
        submit:[int] 提交数 
    }
}
```
###添加题目
```
@Post problems
@Body {
    title,
    difficulty,
    timelimit:{java,others},
    memorylimit:{java, others}
    description,
    input,
    output,
    sampleInput,
    sampleOutput,
    hint,
    source,
    files, //测试数据文件
    specialTest  //special judge程序
    show:true|false //是否在练习列表里面显示
    contestId:null, //将该题添加到contestId的竞赛中
    tags:[...]
}
@Return {
    problemId
}
```
### 修改题目
```
@Put problems/:problemId
@Body{
    //同添加题目
}
@Return {}
```

### 获得可选编程语言
```
@Get languages
@Return [{
    languangeId,
    name
}]
```

### 提交题目
```
@Post problems/:problemId/submit
@Body {
    languangeId:''//语言
    code
}
@Return {
    submissionId //提交Id
}
```
### Rejudge某题所有提交
```
@Put problems/:problemId/rejudge
@Return {}
```

### 删除题目
```
@Delete problems/:problemId
@Return {}
```

## 提交状态
### 提交状态列表
```
@Get submissions
@Query {size,page,problemId,userId,verdictId...一些过滤参数}
@Return [{
  userId,
  problemId,
  verdictId,//评测结果
  time,
  memory,
  languageId,//使用的编译语言
  length,
  submitTime,
  compileError:'xxxx' //编译错误详情
  accessible:[true|false] //当前用户是否可以访问该提交的源代码
}]

```
### 提交详情
```
@Get submissions/:submission
@Return {
    userId,
    problemId,
    verdictId,//评测结果
    time,
    memory,
    languageId,//使用的编译语言
    length,
    submitTime,
    code,
    compileError:'xxxx' //编译错误详情
    accessible:[true|false] //当前用户是否可以访问该提交的源代码
}
```
### rejudge 某个提交
```
@Put submissions/:submissionId/rejudge
```

## 竞赛

### 竞赛列表
```
@Get contests
@Query {size,page,title,statusId}
@Return {
    contestId,
    title,
    startTime,
    endTime,
    statusId,//0-未开始，1-正在进行，2-已完成
    attendsCount，//参赛人数
}
```
### 竞赛主页
```
@Get contests/:contestId
@Return {
    title,
    startTime,
    endTime,
    statusId,
    attendsCount，//参赛人数
    host:{ 举办人
        userId,
        nickname
    }
}
```
### 竞赛题目列表
```
@Get contests/:contetId/problems
@Return [{
    problemOrder,
    title,
    static:static:{ac,submit}
}]
```

### 竞赛排名
```
@Get contests/:contestId/standing
@Query {
    size,
    page,
    problemOrders:['A','B','C','D']//所有题目
}
@Return [
    {
        rank:[int] 排名,
        userId,
        accepts,
        penalty,//以秒为单位
        nickname,
        status:{
            problemOrder1:{//没有尝试过的题目不要放进来
                penalty,
                attempts//该ac之前总提交次数
            }
            ...
        }
    }
]
```


### 竞赛题目详情
```
@Get contests/:contestId/problems/:problemOrder
@Return {
    problemId, //返回一个problemId方法以便调用problems/:problemId/submit方法提交代码
    title,
    timelimit:{ java, others},
    memorylimit:{java,others}
    description,
    input,
    output,
    source,
    static:{ac,submit}
}
```
### 获得竞赛可选编程语言
```
@Get contests/:contestId/languages  
@Return [{
    languangeId,
    name
}]
```
### 竞赛提交状况
```
@Get contests/:contestId/submissions
@Query {size,page,...其他过滤参数}
@Return {
    total:10,
    list:[{
  userId,
  problemOrder,
  verdictId,//评测状态
  languageId,
  time,
  memory,
  accessible:[true}false],
  compiler,//使用的编译语言
  length,
  submitTime,
  compileError:'xxxx' //编译错误详情
}]
    
}
```

### 添加竞赛
```
@Post contests
@Body{
    hostId,//创建人
    title,
    startTime,
    endTime,
    password,//密码
    attendId:[{userId}]//参加的人
}
```
### 修改竞赛
```
@Put contests/:contestId
@Body {
 //同添加竞赛
}
```
### 删除竞赛
```
@Delete contests/:contestId
@Return {}
```

### 论坛列表
```
@Get topics
@Query {size,page,keyword}
@Return {
    total:30,
    list:[{
        topicId,
        title,
        author:{
            userId,
            nickname,
            avatarUrl
        }
        reply:[int]回复数
    }]
}
```
### 添加帖子
```
@Post topics
@Body {
    title,
    content,
    userId
}
@Return {topicId}
```
### 删除帖子
```
@Delete topics/:topicId
@Return {}
```

### 帖子详情
```
@Get topics/:topicId
@Query{
    size,
    page
}
@Return {
    total:10,
    list: [{
        postId:[int]帖子索引,
        title,
        content,
        replyId:0[int]要回复帖子的索引
        author:{
            userId,
            nickname,
            avatarUrl
        }
    }]
}
```

### 回复帖子
```
@Post topicId/:topic/:postId
@Body{
    userId,
    title,
    content
}
@Return {
  postId
}
```
###删除回复
```
@Delete topics/:topicId/:postId
@Return {}
```


##Todos
- 多语言支持
- 用户批量导入
