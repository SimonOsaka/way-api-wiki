## MySQL常用Json函数]

官方文档：[JSON Functions](http://dev.mysql.com/doc/refman/5.7/en/json-functions.html)

| Name                                                                                                                      | Description                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [JSON_APPEND()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-append)             | Append data to JSON document                                                                                              |
| [JSON_ARRAY()](http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-array)                   | Create JSON array                                                                                                         |
| [JSON_ARRAY_APPEND()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-array-append) | Append data to JSON document                                                                                              |
| [JSON_ARRAY_INSERT()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-array-insert) | Insert into JSON array                                                                                                    |
| [->](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#operator_json-column-path)                         | Return value from JSON column after evaluating path; equivalent to JSON_EXTRACT().                                        |
| [JSON_CONTAINS()](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-contains)               | Whether JSON document contains specific object at path                                                                    |
| [JSON_CONTAINS_PATH()](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-contains-path)     | Whether JSON document contains any data at path                                                                           |
| [JSON_DEPTH()](http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-depth)                  | Maximum depth of JSON document                                                                                            |
| [JSON_EXTRACT()](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-extract)                 | Return data from JSON document                                                                                            |
| [->>](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#operator_json-inline-path)                        | Return value from JSON column after evaluating path and unquoting the result; equivalent to JSON_UNQUOTE(JSON_EXTRACT()). |
| [JSON_INSERT()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-insert)             | Insert data into JSON document                                                                                            |
| [JSON_KEYS()](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-keys)                       | Array of keys from JSON document                                                                                          |
| [JSON_LENGTH()](http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-length)                | Number of elements in JSON document                                                                                       |
| [JSON_MERGE()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-merge)               | Merge JSON documents                                                                                                      |
| [JSON_OBJECT()](http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-object)                 | Create JSON object                                                                                                        |
| [JSON_QUOTE()](http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-quote)                   | Quote JSON document                                                                                                       |
| [JSON_REMOVE()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-remove)             | Remove data from JSON document                                                                                            |
| [JSON_REPLACE()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-replace)           | Replace values in JSON document                                                                                           |
| [JSON_SEARCH()](http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-search)                   | Path to value within JSON document                                                                                        |
| [JSON_SET()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-set)                   | Insert data into JSON document                                                                                            |
| [JSON_TYPE()](http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-type)                    | Type of JSON value                                                                                                        |
| [JSON_UNQUOTE()](http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-unquote)           | Unquote JSON value                                                                                                        |
| [JSON_VALID()](http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-valid)                  | Whether JSON value is valid                                                                                               |

1. 概述

MySQL里的json分为json array和json object。 $表示整个json对象，在索引数据时用**下标**(对于json array，从0开始)或**键值**(对于json object，含有特殊字符的key要用"括起来，比如$."my name")。

```sql
例如：[3, {"a": [5, 6], "b": 10}, [99, 100]]，那么：
$[0]：3
$[1]： {"a": [5, 6], "b": 10}
$[2] ：[99, 100]
$[3] ： NULL
$[1].a：[5, 6]
$[1].a[1]：6
$[1].b：10
$[2][0]：99
```

# 2. 比较规则

json中的数据可以用 =, <, <=, >, >=, <>, !=, and <=> 进行比较。但json里的数据类型可以是多样的，那么在不同类型之间进行比较时，就有优先级了，**高优先级的要大于低优先级的**（可以用JSON_TYPE()函数查看类型）。优先级从高到低如下：

```sql
BLOB
BIT
OPAQUE
DATETIME
TIME
DATE
BOOLEAN
ARRAY
OBJECT
STRING
INTEGER, DOUBLE
NULL
```

# 3. 常用函数

## 3.1 创建函数

### 3.1.1 JSON_ARRAY

JSON_ARRAY(val1,val2,val3...)

生成一个包含指定元素的json数组。

```mysql
mysql> SELECT JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME());
+---------------------------------------------+
| JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME()) |
+---------------------------------------------+
| [1, "abc", null, true, "11:30:24.000000"]   |
+---------------------------------------------+
```

　　

### 3.1.2 JSON_OBJECT

JSON_OBJECT(key1,val1,key2,val2...)

生成一个包含指定K-V对的json object。如果有key为NULL或参数个数为奇数，则抛错。

```mysql
mysql> SELECT JSON_OBJECT('id', 87, 'name', 'carrot');
+-----------------------------------------+
| JSON_OBJECT('id', 87, 'name', 'carrot') |
+-----------------------------------------+
| {"id": 87, "name": "carrot"}            |
+-----------------------------------------+
```

3.1.3 JSON_QUOTE

JSON_QUOTE(json_val)

将json_val用"号括起来。

```mysql
mysql> SELECT JSON_QUOTE('null'), JSON_QUOTE('"null"');
+--------------------+----------------------+
| JSON_QUOTE('null') | JSON_QUOTE('"null"') |
+--------------------+----------------------+
| "null"             | "\"null\""           |
+--------------------+----------------------+
mysql> SELECT JSON_QUOTE('[1, 2, 3]');
+-------------------------+
| JSON_QUOTE('[1, 2, 3]') |
+-------------------------+
| "[1, 2, 3]"             |
+-------------------------+
```

　　

### 3.1.4 CONVERT

CONVERT(json_string,JSON)

```mysql
mysql> select CONVERT('{"mail": "amy@gmail.com", "name": "Amy"}',JSON);
+----------------------------------------------------------+
| CONVERT('{"mail": "amy@gmail.com", "name": "Amy"}',JSON) |
+----------------------------------------------------------+
| {"mail": "amy@gmail.com", "name": "Amy"}                          |
+----------------------------------------------------------+
```

　　

## 3.2 查询函数

### 3.2.1 JSON_CONTAINS

JSON_CONTAINS(json_doc, val[, path])

查询json文档是否在指定path包含指定的数据，包含则返回1，否则返回0。如果有参数为NULL或path不存在，则返回NULL。

```mysql
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
mysql> SET @j2 = '1';
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                                           1 |
+-------------------------------+
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.b');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.b') |
+-------------------------------+
|                                           0 |
+-------------------------------+ 
mysql> SET @j2 = '{"d": 4}';
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                                            0 |
+-------------------------------+
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.c');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.c') |
+-------------------------------+
|                                            1 |
+-------------------------------+
```

　　

### 3.2.2 JSON_CONTAINS_PATH

JSON_CONTAINS_PATH(json_doc, one_or_all, path[, path] ...)

查询是否存在指定路径，存在则返回1，否则返回0。如果有参数为NULL，则返回NULL。

one_or_all只能取值"one"或"all"，one表示只要有一个存在即可；all表示所有的都存在才行。

```mysql
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e') |
+---------------------------------------------+
|                                                               1 |
+---------------------------------------------+
mysql> SELECT JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e') |
+---------------------------------------------+
|                                                               0 |
+---------------------------------------------+
mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.c.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.c.d') |
+----------------------------------------+
|                                                        1 |
+----------------------------------------+
mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a.d') |
+----------------------------------------+
|                                                           0 |
+----------------------------------------+
```

　

### 3.2.3 JSON_EXTRACT

JSON_EXTRACT(json_doc, path[, path] ...)

从json文档里抽取数据。如果有参数有NULL或path不存在，则返回NULL。如果抽取出多个path，则返回的数据封闭在一个json array里。

```mysql
mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]');
+--------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]') |
+--------------------------------------------+
| 20                                                              |
+--------------------------------------------+
mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]');
+----------------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]') |
+----------------------------------------------------+
| [20, 10]                                                               |
+----------------------------------------------------+
mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]');
+-----------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]') |
+-----------------------------------------------+
| [30, 40]                                                           |
+-----------------------------------------------+
```

在MySQL 5.7.9+里可以用"**->**"替代。

```mysql
mysql> SELECT c, JSON_EXTRACT(c, "$.id"), g    
         > FROM jemp   
         > WHERE JSON_EXTRACT(c, "$.id") > 1    
         > ORDER BY JSON_EXTRACT(c, "$.name");
+-------------------------------+-----------+------+
| c                                            | c->"$.id" | g      |
+-------------------------------+-----------+------+
| {"id": "3", "name": "Barney"} | "3"            |  3      |
| {"id": "4", "name": "Betty"}  | "4"            |  4      |
| {"id": "2", "name": "Wilma"}  | "2"            |  2      |
+-------------------------------+-----------+------+
3 rows in set (0.00 sec) 

mysql> SELECT c, c->"$.id", g    
         > FROM jemp   
         > WHERE c->"$.id" > 1    
         > ORDER BY c->"$.name";
+-------------------------------+-----------+------+
| c                                           | c->"$.id" | g       |
+-------------------------------+-----------+------+
| {"id": "3", "name": "Barney"} | "3"            |  3      |
| {"id": "4", "name": "Betty"}     | "4"            |  4   |
| {"id": "2", "name": "Wilma"}     | "2"            |  2      |
+-------------------------------+-----------+------+
3 rows in set (0.00 sec)
```

　　

在MySQL 5.7.13+，还可以用"**->>**"表示去掉抽取结果的"号，下面三种效果是一样的：

- JSON_UNQUOTE( JSON_EXTRACT(column, path) )
- JSON_UNQUOTE(column -> path)
- column->>path

```mysql
mysql> SELECT * FROM jemp WHERE g > 2;
+-------------------------------+------+
| c                                            | g       |
+-------------------------------+------+
| {"id": "3", "name": "Barney"} |       3 |
| {"id": "4", "name": "Betty"}     |       4 |
+-------------------------------+------+
2 rows in set (0.01 sec) 

mysql> SELECT c->'$.name' AS name    
        ->   FROM jemp WHERE g > 2;
+----------+
| name        |
+----------+
| "Barney" |
| "Betty"  |
+----------+
2 rows in set (0.00 sec) 

mysql> SELECT JSON_UNQUOTE(c->'$.name') AS name  
        ->   FROM jemp WHERE g > 2;
+--------+
| name   |
+--------+
| Barney |
| Betty  |
+--------+
2 rows in set (0.00 sec) 

mysql> SELECT c->>'$.name' AS name  
        ->   FROM jemp WHERE g > 2;
+--------+
| name   |
+--------+
| Barney |
| Betty  |
+--------+
2 rows in set (0.00 sec)
```

　

### 3.2.4 JSON_KEYS

JSON_KEYS(json_doc[, path])

获取json文档在指定路径下的所有键值，返回一个json array。如果有参数为NULL或path不存在，则返回NULL。

```mysql
mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}');
+---------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}') |
+---------------------------------------+
| ["a", "b"]                                          |
+---------------------------------------+

mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b');
+----------------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b') |
+----------------------------------------------+
| ["c"]                                                             |
+----------------------------------------------+
```

　　

### 3.2.5 JSON_SEARCH

JSON_SEARCH(json_doc, one_or_all, search_str[, escape_char[, path] ...])

查询包含指定字符串的paths，并作为一个json array返回。如果有参数为NUL或path不存在，则返回NULL。

one_or_all："one"表示查询到一个即返回；"all"表示查询所有。

search_str：要查询的字符串。 可以用LIKE里的'%'或‘_’匹配。

path：在指定path下查。

```mysql
mysql> SET @j = '["abc", [{"k": "10"}, "def"], {"x":"abc"}, {"y":"bcd"}]'; 
mysql> SELECT JSON_SEARCH(@j, 'one', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'one', 'abc') |
+-------------------------------+
| "$[0]"                                     |
+-------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'abc') |
+-------------------------------+
| ["$[0]", "$[2].x"]                   |
+-------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', 'ghi');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'ghi') |
+-------------------------------+
| NULL                                         |
+-------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10');
+------------------------------+
| JSON_SEARCH(@j, 'all', '10') |
+------------------------------+
| "$[1][0].k"                             |
+------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$');
+-----------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$') |
+-----------------------------------------+
| "$[1][0].k"                                            |
+-----------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[*]');
+--------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[*]') |
+--------------------------------------------+
| "$[1][0].k"                                                 |
+--------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$**.k');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$**.k') |
+---------------------------------------------+
| "$[1][0].k"                                                  |
+---------------------------------------------+ 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[*][0].k');
+-------------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[*][0].k') |
+-------------------------------------------------+
| "$[1][0].k"                                                        |
+-------------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[1]');
+--------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[1]') |
+--------------------------------------------+
| "$[1][0].k"                                                |
+--------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[1][0]');
+-----------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[1][0]') |
+-----------------------------------------------+
| "$[1][0].k"                                                   |
+-----------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', 'abc', NULL, '$[2]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', 'abc', NULL, '$[2]') |
+---------------------------------------------+
| "$[2].x"                                                    |
+---------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%a%');
+-------------------------------+
| JSON_SEARCH(@j, 'all', '%a%') |
+-------------------------------+
| ["$[0]", "$[2].x"]                   |
+-------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%b%');
+-------------------------------+
| JSON_SEARCH(@j, 'all', '%b%') |
+-------------------------------+
| ["$[0]", "$[2].x", "$[3].y"]  |
+-------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[0]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[0]') |
+---------------------------------------------+
| "$[0]"                                                           |
+---------------------------------------------+ 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[2]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[2]') |
+---------------------------------------------+
| "$[2].x"                                                       |
+---------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[1]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[1]') |
+---------------------------------------------+
| NULL                                                             |
+---------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', '', '$[1]');
+-------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', '', '$[1]') |
+-------------------------------------------+
| NULL                                                        |
+-------------------------------------------+ 

mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', '', '$[3]');
+-------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', '', '$[3]') |
+-------------------------------------------+
| "$[3].y"                                                     |
+-------------------------------------------+
```

　　

## 3.3 修改函数

### 3.3.1 JSON_APPEND/JSON_ARRAY_APPEND

JSON_ARRAY_APPEND(json_doc, path, val[, path, val] ...)

在指定path的json array尾部追加val。如果指定path是一个json object，则将其封装成一个json array再追加。如果有参数为NULL，则返回NULL。

```mysql
mysql> SET @j = '["a", ["b", "c"], "d"]';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1]', 1);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1]', 1) |
+----------------------------------+
| ["a", ["b", "c", 1], "d"]            |
+----------------------------------+

mysql> SELECT JSON_ARRAY_APPEND(@j, '$[0]', 2);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[0]', 2) |
+----------------------------------+
| [["a", 2], ["b", "c"], "d"]        |
+----------------------------------+

mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1][0]', 3);
+-------------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1][0]', 3) |
+-------------------------------------+
| ["a", [["b", 3], "c"], "d"]             |
+-------------------------------------+ 

mysql> SET @j = '{"a": 1, "b": [2, 3], "c": 4}';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$.b', 'x');
+------------------------------------+
| JSON_ARRAY_APPEND(@j, '$.b', 'x')  |
+------------------------------------+
| {"a": 1, "b": [2, 3, "x"], "c": 4} |
+------------------------------------+

mysql> SELECT JSON_ARRAY_APPEND(@j, '$.c', 'y');
+--------------------------------------+
| JSON_ARRAY_APPEND(@j, '$.c', 'y')       |
+--------------------------------------+
| {"a": 1, "b": [2, 3], "c": [4, "y"]} |
+--------------------------------------+ 

mysql> SET @j = '{"a": 1}';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$', 'z');
+---------------------------------+
| JSON_ARRAY_APPEND(@j, '$', 'z') |
+---------------------------------+
| [{"a": 1}, "z"]                          |
+---------------------------------+
```

　　

### 3.3.2 JSON_ARRAY_INSERT

JSON_ARRAY_INSERT(json_doc, path, val[, path, val] ...)

 在path指定的json array元素插入val，原位置及以右的元素顺次右移。如果path指定的数据非json array元素，则略过此val；如果指定的元素下标超过json array的长度，则插入尾部。

```mysql
mysql> SET @j = '["a", {"b": [1, 2]}, [3, 4]]';
mysql> SELECT JSON_ARRAY_INSERT(@j, '$[1]', 'x');
+------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[1]', 'x') |
+------------------------------------+
| ["a", "x", {"b": [1, 2]}, [3, 4]]  |
+------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[100]', 'x');
+--------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[100]', 'x') |
+--------------------------------------+
| ["a", {"b": [1, 2]}, [3, 4], "x"]       |
+--------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[1].b[0]', 'x');
+-----------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[1].b[0]', 'x') |
+-----------------------------------------+
| ["a", {"b": ["x", 1, 2]}, [3, 4]]            |
+-----------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[2][1]', 'y');
+---------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[2][1]', 'y') |
+---------------------------------------+
| ["a", {"b": [1, 2]}, [3, "y", 4]]       |
+---------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[0]', 'x', '$[2][1]', 'y');
+----------------------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[0]', 'x', '$[2][1]', 'y') |
+----------------------------------------------------+
| ["x", "a", {"b": [1, 2]}, [3, 4]]                          |
+----------------------------------------------------+
```

　　

### 3.3.3 JSON_INSERT/JSON_REPLACE/JSON_SET

JSON_INSERT(json_doc, path, val[, path, val] ...)

在指定path下插入数据，如果path已存在，则忽略此val（**不存在才插入**）。

```mysql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]');
+----------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]') |
+----------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": "[true, false]"}            |
+----------------------------------------------------+
```

JSON_REPLACE(json_doc, path, val[, path, val] ...)

替换指定路径的数据，如果某个路径不存在则略过（**存在才替换**）。如果有参数为NULL，则返回NULL。

```mysql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]');
+-----------------------------------------------------+
| JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]') |
+-----------------------------------------------------+
| {"a": 10, "b": [2, 3]}                                               |
+-----------------------------------------------------+
```

JSON_SET(json_doc, path, val[, path, val] ...)

设置指定路径的数据（**不管是否存在**）。如果有参数为NULL，则返回NULL。

```mysql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_SET(@j, '$.a', 10, '$.c', '[true, false]');
+-------------------------------------------------+
| JSON_SET(@j, '$.a', 10, '$.c', '[true, false]') |
+-------------------------------------------------+
| {"a": 10, "b": [2, 3], "c": "[true, false]"}      |
+-------------------------------------------------+

mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]');
+----------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]') |
+----------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": "[true, false]"}             |
+----------------------------------------------------+

mysql> SELECT JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]');
+-----------------------------------------------------+
| JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]') |
+-----------------------------------------------------+
| {"a": 10, "b": [2, 3]}                                               |
+-----------------------------------------------------+
```

　　

### 3.3.4 JSON_MERGE

JSON_MERGE(json_doc, json_doc[, json_doc] ...)

merge多个json文档。规则如下：

- 如果都是json array，则结果自动merge为一个json array；
- 如果都是json object，则结果自动merge为一个json object；
- 如果有多种类型，则将非json array的元素封装成json array再按照规则一进行mege。

```mysql
mysql> SELECT JSON_MERGE('[1, 2]', '[true, false]');
+---------------------------------------+
| JSON_MERGE('[1, 2]', '[true, false]') |
+---------------------------------------+
| [1, 2, true, false]                              |
+---------------------------------------+

mysql> SELECT JSON_MERGE('{"name": "x"}', '{"id": 47}');
+-------------------------------------------+
| JSON_MERGE('{"name": "x"}', '{"id": 47}') |
+-------------------------------------------+
| {"id": 47, "name": "x"}                              |
+-------------------------------------------+

mysql> SELECT JSON_MERGE('1', 'true');
+-------------------------+
| JSON_MERGE('1', 'true') |
+-------------------------+
| [1, true]                        |
+-------------------------+

mysql> SELECT JSON_MERGE('[1, 2]', '{"id": 47}');
+------------------------------------+
| JSON_MERGE('[1, 2]', '{"id": 47}') |
+------------------------------------+
| [1, 2, {"id": 47}]                          |
+------------------------------------+
```

　　

### 3.3.5 JSON_REMOVE

JSON_REMOVE(json_doc, path[, path] ...)
移除指定路径的数据，如果某个路径不存在则略过此路径。如果有参数为NULL，则返回NULL。

```mysql
mysql> SET @j = '["a", ["b", "c"], "d"]';
mysql> SELECT JSON_REMOVE(@j, '$[1]');
+-------------------------+
| JSON_REMOVE(@j, '$[1]') |
+-------------------------+
| ["a", "d"]                       |
+-------------------------+
```

　　

### 3.3.6 JSON_UNQUOTE

JSON_UNQUOTE(val)

去掉val的引号。如果val为NULL，则返回NULL。

```mysql
mysql> SET @j = '"abc"';
mysql> SELECT @j, JSON_UNQUOTE(@j);
+-------+------------------+
| @j  | JSON_UNQUOTE(@j)      |
+-------+------------------+
| "abc" | abc                    |
+-------+------------------+

mysql> SET @j = '[1, 2, 3]';
mysql> SELECT @j, JSON_UNQUOTE(@j);
+-----------+------------------+
| @j    | JSON_UNQUOTE(@j)          |
+-----------+------------------+
| [1, 2, 3] | [1, 2, 3]             |
+-----------+------------------+
```

## 3.4 JSON特性查询

### 3.4.1 JSON_DEEPTH

 JSON_DEPTH(json_doc)

获取json文档的深度。如果参数为NULL，则返回NULL。

空的json array、json object或标量的深度为1。

```mysql
mysql> SELECT JSON_DEPTH('{}'), JSON_DEPTH('[]'), JSON_DEPTH('true');
+------------------+------------------+--------------------+
| JSON_DEPTH('{}') | JSON_DEPTH('[]') | JSON_DEPTH('true') |
+------------------+------------------+--------------------+
|                         1 |                        1 |                          1 |
+------------------+------------------+--------------------+

mysql> SELECT JSON_DEPTH('[10, 20]'), JSON_DEPTH('[[], {}]');
+------------------------+------------------------+
| JSON_DEPTH('[10, 20]') | JSON_DEPTH('[[], {}]') |
+------------------------+------------------------+
|                                2 |                                2 |
+------------------------+------------------------+

mysql> SELECT JSON_DEPTH('[10, {"a": 20}]');
+-------------------------------+
| JSON_DEPTH('[10, {"a": 20}]') |
+-------------------------------+
|                                            3 |
+-------------------------------+
```

### 3.4.2 JSON_LENGTH

JSON_LENGTH(json_doc[, path])

获取指定路径下的长度。如果参数为NULL，则返回NULL。　

长度的计算规则：

- 标量的长度为1；
- json array的长度为元素的个数；
- json object的长度为key的个数。

```mysql
mysql> SELECT JSON_LENGTH('[1, 2, {"a": 3}]');
+---------------------------------+
| JSON_LENGTH('[1, 2, {"a": 3}]') |
+---------------------------------+
|                                                3 |
+---------------------------------+

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}');
+-----------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}') |
+-----------------------------------------+
|                                                         2 |
+-----------------------------------------+

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b');
+------------------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b') |
+------------------------------------------------+
|                                                                    1 |
+------------------------------------------------+
```

　　

### 3.4.3 JSON_TYPE

JSON_TYPE(json_val)

获取json文档的具体类型。如果参数为NULL，则返回NULL。

### 3.4.4 JSON_VALID

JSON_VALID(val)

判断val是否为有效的json格式，是为1，不是为0。如果参数为NUL，则返回NULL。

```mysql
mysql> SELECT JSON_VALID('{"a": 1}');
+------------------------+
| JSON_VALID('{"a": 1}') |
+------------------------+
|                                   1 |
+------------------------+

mysql> SELECT JSON_VALID('hello'), JSON_VALID('"hello"');
+---------------------+-----------------------+
| JSON_VALID('hello') | JSON_VALID('"hello"') |
+---------------------+-----------------------+
|                              0 |                               1 |
+---------------------+-----------------------+
```

　　

原文：[MySQL常用Json函数](https://www.cnblogs.com/waterystone/p/5626098.html)
