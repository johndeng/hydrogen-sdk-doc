﻿# 知晓云 Android SDK 指南


## 接入 SDK
{% ifanrxCodeTabs %}
```gradle
// 根模块加入 jcenter 仓库
buildscript {
    repositories {
        jcenter()   
    }
}

// app 模块引入依赖
implementation "com.minapp.android:sdk:xxx"
```
{% endifanrxCodeTabs %}


## 初始化
{% ifanrxCodeTabs %}
```java
// 初始化 SDK，这里不耗费时间，可以在android.app.Application#onCreate
Baas.init(Const.clientId)
```
{% endifanrxCodeTabs %}


## 创建一条记录
{% ifanrxCodeTabs %}
```java
Table table = new Table("{tableName}");
Record record = table.createRecord();
record.putXXX(...);
record.save();          // 这样就保存至后端了
```
{% endifanrxCodeTabs %}


## 获取一条记录
{% ifanrxCodeTabs %}
```java
Record record = table.fetchRecord(...);
record.getXXX(...);             // 获取记录的各个属性
record.putXXX(...);             // 修改之
record.save();                  // 保存至后端
```
{% endifanrxCodeTabs %}

## 删除一条记录
{% ifanrxCodeTabs %}
```java
Record record = table.fetchRecord(...);
record.delete();                  
```
{% endifanrxCodeTabs %}


## 记录的基本概念
{% ifanrxCodeTabs %}
```java
// 一个 Record 拥有一些基本的属性
record.getId();             // 记录的 ID  
record.getCreatedBy();      // 创建人 ID
record.getCreatedAt();      // 创建时间
record.getUpdatedAt();      // 更新时间
record.getWritePerm();      // 写权限列表
record.getReadPerm();       // 读权限列表

// 用户在定义表时新增的列，就需要通过 getXXX() 和 putXXX 方法读写
record.putXXX(...);
```
{% endifanrxCodeTabs %}


## 列表查询
{% ifanrxCodeTabs %}
```java
// BaseQuery 代表了查询条件，一般的列表 api 都需要传一个此实例过去
BaseQuery query = new BaseQuery();  

// 通用的查询条件，具体的使用方法参考文档和代码注释
BaseQuery.OFFSET
BaseQuery.LIMIT
BaseQuery.EXPAND
BaseQuery.KEYS
BaseQuery.ORDER_BY
BaseQuery.AGGREGATION

query.put(BaseQuery.OFFSET, "0")
query.put(BaseQuery.LIMIT, "15")
query.put(BaseQuery.EXPAND, "horse_owner")
query.put(BaseQuery.ORDER_BY, Record.CREATED_AT)

// 得到一页记录，包括记录总数，此页内容
table.query(query);  
```
{% endifanrxCodeTabs %}


## where 查询
{% ifanrxCodeTabs %}
```java
Where where = new Where();
// 就像 sql 一样，方法名代表操作符，参数分别为左值和右值，它们以 and 相连
where.eq(lvalue, rvalue).gt(...).ne(...);   

Where condition1 = new Where();
condition1...
Where condition2 = new Where();
condition2...
Where where = Where.or(condition1, condition2);     // 这样以 or 合并
query.putWhere(where);      // 最后放入查询条件里
```
{% endifanrxCodeTabs %}

## 批量操作
```java
// 批量删除，这里主要是构建 where 条件（类似 delete from where ...），可以添加分页条件
BaseQuery query = new BaseQuery();
query.put(...)
Where condition = new Where();
query.putWhere(condition);
table.batchDelete(query);

// 批量保存
List<Record> products = table.query(query).getObjects();
for(Record product : products) {
    product.put(...);
}
table.batchSave(products);

// 批量删除，这里主要是构建 where 条件（类似 update where ...），可以添加分页条件
BaseQuery query = new BaseQuery();
query.put(...)
Where condition = new Where();
query.putWhere(condition);

// 这里相当于 update 的内容
Record update = new Record();
update.put(...);
table.batchUpdate(query, update);
```


## 用户相关
{% ifanrxCodeTabs %}
```java
Auth.signUpByEmail(String email, String pwd);       // 通过邮箱注册
Auth.signUpByUsername(String username, String pwd); // 通过用户名注册
Auth.emailVerify();                                 // 发送验证邮件
Auth.updateUser(UpdateUserReq request);             // 修改用户用于登录的基本信息
Auth.resetPwd(String email);                        // 重置邮箱所属用户密码
```
{% endifanrxCodeTabs %}


## 登录和登出
{% ifanrxCodeTabs %}
```java
Auth.signInByEmail(String email, String pwd);       // 邮箱登录
Auth.signInByUsername(String username, String pwd); // 用户名登录
Auth.signInAnonymous();                             // 匿名登录
Auth.logout();                                      // 登出
Auth.isSignIn();                                    // 是否有登录
```
{% endifanrxCodeTabs %}


## 用户
{% ifanrxCodeTabs %}
```java
Users.users(BaseQuery query);   // 用户列表
Users.use(String id);           // 用户明细
```
{% endifanrxCodeTabs %}


## 上传文件
{% ifanrxCodeTabs %}
```java
Storage.uploadFile(String name, byte[] data);
Storage.uploadFile(String name, String categoryId, byte[] data);
```
{% endifanrxCodeTabs %}


## 文件列表和 CURD 操作
{% ifanrxCodeTabs %}
```java
Storage.files(BaseQuery query);                 // 文件列表
Storage.file(String id);                        // 文件详情
Storage.deleteFiles(Collection<String> ids);    // 删除文件
```
{% endifanrxCodeTabs %}


## 文件分类
{% ifanrxCodeTabs %}
```java
Storage.categories(BaseQuery query);    // 文件分类列表
Storage.category(String id);            // 分类详情
```
{% endifanrxCodeTabs %}


## 内容
{% ifanrxCodeTabs %}
```java
Contents.contents(@NonNull BaseQuery query);            // 内容列表
Contents.content(String id);                            // 内容详情
Contents.contentGroups(@NonNull BaseQuery query);       // 内容库列表
Contents.contentCategories(@NonNull BaseQuery query);   // 内容分类列表
Contents.contentCategory(String id);                    // 内容分类详情
```
{% endifanrxCodeTabs %}


## 参考实践
首先根据业务定义出领域对象
{% ifanrxCodeTabs %}
```java
class Product extends Record {

    // 定义后端数据表列名
    public static final String NAME = "name";
    ...
    
    // 用领域对象包装 Record 对象方便操作
    public Product(Record data) {
        super(data);
    }
    
    // 定义 setter/getter
    public String getName() {
        return getString(NAME);
    }
    public void setName(String name) {
        put(NAME, name);
    }
    ...
}
```
{% endifanrxCodeTabs %}

然后使用 ProductRepository 作为仓库给 ViewModel 层
{% ifanrxCodeTabs %}
```java
public interface ProductRepository {

    // 根据业务定义接口，接口层屏蔽数据源的实现
    // 当使用 sdk 数据源时，实现 MinappProductRepository
    // 当无网络而使用本地数据源时，实现 LocalProductRepository
    // ...
    List<Product> queryByCompony(String componyId);
    ...
}
```
{% endifanrxCodeTabs %}


