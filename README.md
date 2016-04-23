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
    token:'xxx'
}
```

## 账户
### 登陆
```
@Get user/login
@Query {userId,password}
@Return {
    userId: [string] 用户名,
    nickname:昵称,
    signature:用户签名,
    avatarUrl:头像地址,
    classname:班级,
    email:邮箱,
    token
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
@Query {userId}
@Return {}
```

## 用户
### 获得用户排名 (ranklist)
```
@Get users
@Query {
    filter:{
        userId:[...],
        classname:[...],
        ...
    },
    pageIndex,
    pageSize
}
@Return [{
    rank: [int] 排第几名,
    userId:用户名,
    nickname:昵称，
    signature，
    classname,
    static:{
        ac:[int] AC题数
        submit:[int] 提交数 
    }
}]
```
### 获得用户详情
```
@Get users/:userId
@Return {
    nickname,
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
    pageSize,
    pageIndex,
    filter:{...}
}
@Return [{
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
```

### 题目详情
```
@Get problems/:problemId
@Return {
    problemId,
    title,
    tags:[...],
    timelimit:{java,others},
    memorylimit:{java, others}
    description,
    input,
    output,
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
    timelimit:{java,others},
    memorylimit:{java, others}
    description,
    input,
    output,
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

### 提交题目
```
@Post problems/:problemId/submit
@Body {
    compiler:''//语言
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
@Query {pageSize,pageIndex}
@Return [{
  userId,
  nickname,
  problemId,
  verdict,//评测结果
  time,
  memory,
  compiler,//使用的编译语言
  codeLength,
  submitTime,
  compileError:'xxxx' //编译错误详情
}]

```
### rejudge 某个提交
```
@Put submissions/:submissionId/rejudge
```

## 竞赛
### 竞赛列表
```
@Get contests
@Query {pageSize,pageIndex,filter}
@Return {
    contestId,
    title,
    startTime,
    status,
    attendsCount，//参赛人数
    host:{ 举办人
        userId,
        nickname
    }
}
```
### 竞赛主页
```
@Get contests/:contestId
@Return {
    title,
    startTime,
    endTime,
    status,
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
@Return [
    {
        rank:[int] 排名,
        userId,
        solved,
        time,
        nickname,
        status:{
            problemOrder1:{
                time,
                penalty
            },
            problemOrder2:{
                time,
                penalty
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
### 竞赛提交状况
```
@Get contests/:contestId/submissions
@Query {pageSize,pageIndex,filter}
@Return [{
  userId,
  nickname,
  problemOrder,
  verdict,//评测状态
  time,
  memory,
  compiler,//使用的编译语言
  codeLength,
  submitTime,
  compileError:'xxxx' //编译错误详情
}]
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
@Get discuss
@Query {pageSize,pageIndex}
@Return [{
    title,
    author:{
        userId,
        nickname,
        avatarUrl,
        classname
    }
    reply:[int]回复数
}]
```
### 添加帖子
```
@Post discuss
@Body {
    title,
    content,
    userId
}
@Return {dicussId}
```
### 删除帖子
```
@Delete discuss/:discussId
@Return {}
```

### 帖子详情
```
@Get discuss/:discussId
@Query{pageSize,pageIndex}
@Return [{
    index:[int]帖子索引,
    title,
    content,
    reply:0[int]要回复帖子的索引
    author:{
        userId,
        nickname,
        avatarUrl,
        classname
    }
}]
```

### 回复帖子
```
@Post discuss/:discussId
@Body{
    userId,
    title,
    content,
    reply:0
}
@Return {
    index:[int]帖子索引,
    title,
    content,
    reply:[int]要回复帖子的索引
    author:{
        userId,
        nickname,
        avatarUrl,
        classname
    }
}
```
###删除回复
```
@Delete discuss/:discussId/:index
@Return {}
```


##Todos
- 多语言支持
- 用户批量导入
