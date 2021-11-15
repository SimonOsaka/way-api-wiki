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

impl User {
  pub fn tm(&self, a: String) -> String {
    a
  }
  
  pub fn mt(a: String) -> String {
    a
  }
}
// 引用
let u = User {
  i: 12
};
u.tm("sss".to_string()); // output: sss
User::mt("bbb".to_string()); // output: bbb
```



##特性

> 不能保证完全一致，但是思想很接近

| Rust                   | Java                               |
| ---------------------- | ---------------------------------- |
| trait 名称             | interface 名称                     |
| impl trait名称 for ... | class ... implements interface名称 |
|                        |                                    |

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

