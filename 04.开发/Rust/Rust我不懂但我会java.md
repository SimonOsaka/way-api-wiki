## 基本类型

| Rust   | Java   |
| ------ | ------ |
| i16    | short  |
| i32    | int    |
| i64    | long   |
| String | String |
|        |        |

## 结构体

| Rust                                                         | Java                                                         |          |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| struct 名称 {<br>    i: i32<br>}                             | class 名称 {<br>    int i;<br>}                              | 类声明   |
| struct 名称(xxx);                                            | 名称(xxx) {<br>}                                             | 构造函数 |
| impl 名称 {<br>}                                             | {<br>}                                                       | 类内部   |
| impl 名称{<br>    pub fn tm(&self, a: String) -> String {<br>        a<br>    }<br>} | {<br>    public String tm(String a) {<br>        return a;<br>    }<br>} | 实例方法 |
| impl 名称{<br/>    pub fn tm(a: String) -> String {<br/>        a<br/>    }<br/>} | {<br/>    public static String tm(String a) {<br/>        return a;<br/>    }<br/>} | 类方法   |
|                                                              |                                                              |          |

完整例子

```rust
// 声明
struct User {
  i: i32
}
// 或者
struct User(i32);

impl User {
  pub fn tm(&self, a: String) -> String {
    a
  }
  
  pub fn mt(a: String) -> String {
    a
  }
}
// 实例
let u = User {
  i: 12
};
//或者
let u = User(12);

u.tm("sss".to_string()); // output: sss
User::mt("bbb".to_string()); // output: bbb
```



##特性

> 不能保证完全一致，但是思想很接近

| Rust                                                  | Java                                         |                  |
| ----------------------------------------------------- | -------------------------------------------- | ---------------- |
| trait 名称1                                           | interface 名称1                              | 接口声明         |
| trait 名称2: 名称1                                    | interface 名称2 extends 名称1                | 接口继承         |
| impl trait名称 for ...                                | class ... implements interface名称           | 接口实现         |
| pub fn 方法名(&self) -> 返回值;                       | public 返回值 方法名();                      | 接口声明方法     |
| pub fn 方法名(&self) -> 返回值 {<br>    逻辑实现<br>} | default 返回值 方法名 {<br>    逻辑实现<br>} | 接口声明默认方法 |

完整例子

```rust
struct Car {
  brand: String
}

trait Device {
  pub fn run(&self, name: String) -> String;
  pub fn get_out(&self) -> String {
    "i am out of the car"
  }
}

impl Device for Car {
  pub fn run(&self, name: String) -> String {
    format!("{} drives {}", name, &self.brand)
  }
}

let c = Car{
  brand: "ghost".to_string()
};

c.run("sb"); // output: sb drives ghost
c.get_out(); // output: i am out of the car
```

## 注释

| Rust | Java         |          |
| ---- | ------------ | ------------ |
| `//` | `//`         | 代码注释     |
| `///` | `/**`<br> `*`<br> `*/` | 文档方法注释（通过源代码对比docs.rs） |
|      |              |              |

实际例子

```rust
let a = 1; // 这是注释

/// 函数注释
/// docs.rs显示
fn a() {}
```

