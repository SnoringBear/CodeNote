XORM 是一个简单而强大的 Go 语言 ORM（对象关系映射）框架，支持 MySQL、PostgreSQL、SQLite、MSSQL 等多种数据库。



## 主要特性

- 支持 Struct 和数据库表之间的灵活映射
- 支持事务、链式查询
- 支持原始 SQL 查询和 ORM 操作混合使用
- 提供缓存支持（Redis、Memcached等）
- 支持数据库读写分离
- 自动生成表结构



## 安装

```bash
go get xorm.io/xorm
```

## 基本使用

### 1. 初始化引擎

```go
import (
    "xorm.io/xorm"
    _ "github.com/go-sql-driver/mysql" // 导入MySQL驱动
)

func main() {
    engine, err := xorm.NewEngine("mysql", "root:password@/dbname?charset=utf8")
    if err != nil {
        panic(err)
    }
    defer engine.Close()
}
```



### 2. 定义模型

```go
type User struct {
    Id      int64
    Name    string `xorm:"varchar(25) notnull unique"`
    Age     int
    Created time.Time `xorm:"created"`
    Updated time.Time `xorm:"updated"`
}

// 表名
func (u *User) TableName() string {
    return "users"
}
```



### 3. CRUD 操作

**插入数据**

```go
user := User{Name: "Alice", Age: 25}
affected, err := engine.Insert(&user)
```



**查询数据**

```go
// 查询单个用户
var user User
has, err := engine.ID(1).Get(&user)

// 查询多个用户
var users []User
err := engine.Where("age > ?", 20).Find(&users)
```



**更新数据**

```go
user.Name = "Bob"
affected, err := engine.ID(user.Id).Update(&user)
```



**删除数据**

```go
affected, err := engine.ID(1).Delete(&User{})
```



### 4. 事务处理

```go
session := engine.NewSession()
defer session.Close()

if err := session.Begin(); err != nil {
    panic(err)
}

// 执行多个操作
if _, err := session.Insert(&user1); err != nil {
    session.Rollback()
    return
}

if _, err := session.Insert(&user2); err != nil {
    session.Rollback()
    return
}

return session.Commit()
```



## 高级特性



### 1. 关联查询

```go
type UserDetail struct {
    User   `xorm:"extends"`
    Detail `xorm:"extends"`
}

var details []UserDetail
err := engine.Table("user").Join("INNER", "detail", "user.id = detail.user_id").Find(&details)
```



### 2. 缓存

```go
cacher := xorm.NewLRUCacher(xorm.NewMemoryStore(), 1000)
engine.SetDefaultCacher(cacher)
```



### 3. 读写分离

```go
dataSourceNameSlice := []string{
    "root:password@/dbname?charset=utf8", // 主库
    "root:password@/dbname?charset=utf8", // 从库1
    "root:password@/dbname?charset=utf8", // 从库2
}

engineGroup, err := xorm.NewEngineGroup("mysql", dataSourceNameSlice)
```



## 代码生成工具

XORM 提供了 `xorm` 命令行工具，可以反向生成 Go 代码：

```bash
go get xorm.io/cmd/xorm

# 根据数据库表生成结构体
xorm reverse mysql "root:password@/dbname?charset=utf8" templates/goxorm
```

XORM 是一个功能全面且性能良好的 ORM 框架，适合中小型项目使用。对于更复杂的查询需求，也可以直接使用 SQL 语句。