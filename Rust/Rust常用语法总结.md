## Rust语法

### 1. Rust目录

#### 1.1 平级目录

- src
  - main.rs
  - api.rs

```rust
// main.rs
mod api; // 平级引入api.rs 

fn main() {
  api::hello();
}
```

#### 1.2 非平级目录

* src
  * main.rs
  * api
    * mod.rs
    * api.rs

```rust
// mod.rs
mod api; // 引入api.rs，注意作用域pub
```

```rust
// main.rs
mod api; // 引入api包，注意作用域pub，rust自动查找目录下的mod.rs、lib.rs等
```

#### 1.3 任意目录

```rust
use crate::api::api::hello;

fn main() {
  hello();
}
```

### 2. actix-web使用

#### 2.1 http的post\get\delete\put

```rust
// get method
// path
#[get("/advs/{id}")]
pub async fn detail(_id: web::Path<u32>) -> Result<HttpResponse, Error> {
    Ok(HttpResponse::Ok().body(format!("hello id:{}", _id)))
}
// query
#[get("/advs")]
pub async fn list(page: web::Query<Page>) -> Result<HttpResponse, Error> {
    Ok(HttpResponse::Ok().body(format!("hello page:{}", &page)))
}
```

```rust
// post method
// form
#[post("/advs")]
pub async fn add(page: web::Form<Page>) -> Result<HttpResponse, Error> {
    Ok(HttpResponse::Ok().body(format!("hello page:{}", &page)))
}
```

### 3. sqlx时间DateTime<Utc>问题解决方式

*这个问题sqlx还没有解决方案，只能从业务代码中转换。*

```rust
// 使用FixedOffset将时区变为+08:00
let created_at: NaiveDateTime = row.get("created_at");
let updated_at: NaiveDateTime = row.get("updated_at");
let tz_offset = FixedOffset::east(8 * 3600);
let created_at: DateTime<FixedOffset> = tz_offset.from_local_datetime(&created_at).unwrap();
let updated_at: DateTime<FixedOffset> = tz_offset.from_local_datetime(&updated_at).unwrap();
```

**或**

```rust
let ct: DateTime<Utc> = row.get("create_time");
ct.with_timezone(&FixedOffset::east(8*3600));
```

### 4. 如何运行没有cargo.toml的examples/xxx.rs文件

```shell
# 项目根目录下执行
# 例如：warp
cargo run --example xxx
```

