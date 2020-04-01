# MySQL的varchar与text对比

> 原文：https://blog.51cto.com/arthur376/2121160

varchar和text是mysql字符存储争议比较多的领域,究竟大字段用那个比较好,我们来对比一下,然后自行选择.



#### 大小对比
**`VARCHAR`**:varchar在mysql中必须满足最大行宽度限制，也就是 65535(64k)字节，而varchar本身是按字符串个数来定义的,在mysql中使用uft-8字符集一个字符占用三个字节，所以单表varchar实际占用最大长度如下.

1. 使用utf-8字符编码集varchar最大长度是(65535-2)/3=21844个字符（超过255个字节会有2字节的额外占用空间开销，所以减2,如果是255以下,则减1）。
2. 使用utf-8mb4字符集，mysql中使用 utf-8mb4 字符集一个字符占用4个字节，所以 varchar 最大长度是(65535-2)/4=16383 个字符（超过255个字节会有2字节的额外占用空间开销，所以减2,如果是255以下,则减1）。

注意:如果使用utf-8mb4字符集时,有些需要存储utf-8字符的时候,还是会只占3字节,所以有时会比这个计算值能存更多个字符,因为utf-8mb4是utf-8的超集.



**`TEXT`**:最大限制也是64k个字节,但是本质是溢出存储,innodb默认只会存放前768字节在数据页中,而剩余的数据则会存储在溢出段中,虽然也受单表65535最大行宽度限制,但mysql表中每个BLOB和TEXT列实际只占其中的5至9个字节，其他部分将进行溢出存储.所以实际占用表最大行宽度为9+2字节,外加的是额外开销,跟表的实际宽度没有关系.

1. 如果使用utf-8字符集,那么单字段占用最大长度也是21844个字符.
2. 不过单表可以设置多个text字段,这就突破了单表最大行宽度65535的限制

注意:如果采用了新的行格式类型Barracuda (梭子鱼)，该文件格式拥有新的两种行格式:compressed和dynamic,两种格式对blob/text字段采用完全溢出的方式,数据页中只存放20字节,其余的都存放在溢出段中.



其他text:

text字段是分长中短类型,不像varchar只有一种,除了上面的text,还有下面三个.

`TinyText`:最大长度255个字节,实际上是个没什么意义的类型了.

`MEDIUMTEXT`:最大长度限制16M个字节。和普通text一样也支持溢出存储，所以实际占用表最大行宽度为9+3字节,外加的是额外开销
`LONGTEXT`:最大长度限制4G个字节。和普通text一样也支持溢出存储，所以实际占用表最大行宽度为9+4字节,外加的是额外开销

------

示例:

```sql
#VARCHAR单表单字段最长不能超过21844
CREATE TABLE test(
    va VARCHAR(21845)
)DEFAULT CHARSET=utf8;
[Err] 1118 - Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
#这样就可以了
CREATE TABLE test(
    va VARCHAR(21844)
)DEFAULT CHARSET=utf8;
受影响的行: 0
时间: 0.155s
#虽然每个BLOB和TEXT列 账户只占其中的5至9个字节。但是还不够
CREATE TABLE test(
    va VARCHAR(21841),
    tx text
)DEFAULT CHARSET=utf8;
[Err] 1118 - Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
#然后9+2就可以了
CREATE TABLE test(
    va VARCHAR(21840),
    tx text
)DEFAULT CHARSET=utf8;
受影响的行: 0
时间: 0.170s
```

---

**额外占用空间开销说明**:
varchar 小于255byte 1byte overhead
varchar 大于255byte 2byte overhead
tinytext 0-255 1 byte overhead
text 0-65535 byte 2 byte overhead
mediumtext 0-16M 3 byte overhead
longtext 0-4Gb 4byte overhead 

注意:

虽然text字段会把超过768字节的大部分数据溢出存放到硬盘其他空间,看上去是会更加增加磁盘压力.但从处理形态上来讲varchar大于768字节后，实质上存储和text差别不是太大了.因为超长的varchar也是会用到溢出存储,读取该行也是要去读硬盘然后加载到内存,基本认为是一样的。

另外从8000byte这个点说明一下,mysql的innodb data page默认一个数据页是16K,要存两行数据,所以对于varcahr, text如果一行数据不超过8000byte ,overflow不会存到别的page中。

------



**差异点**:
text字段，MySQL不允许有默认值。建立索引必须给出前缀索引长度.
varchar允许有默认值,对索引长度没限制,

注意:

InnoDB引擎单一字段索引的默认长度最大为767字节,myisam为1000字节.例如字符编码是utf8,那么varchar的索引最大长度是256个字符.超出限制会导致索引创建不成功,转而需要创建前缀索引.设置innodb_large_prefix=1可以增大限制,允许索引使用动态压缩,但是表的row_format必须是compressed或者dynamic.可以使索引列长度大于767bytes,但是总长度不能大于3072 bytes.
\----------------------------------------



**总结**：
　　根据存储的实现:可以考虑用varchar替代text,因为varchar存储更弹性,存储数据少的话性能更高
　　如果需要非空的默认值，就必须使用varchar
　　如果存储的数据大于64K，就必须使用到mediumtext , longtext,因为varchar已经存不下了
　　如果varchar(255+)之后,和text在存储机制是一样的,性能也相差无几
　　需要特别注意varchar(255)不只是255byte ,实质上有可能占用的更多。



感谢吴炳锡老师指导

参考文章地址:http://wubx.net/varchar-vs-text/

https://blog.csdn.net/free_ant/article/details/52936756

https://blog.csdn.net/q3dxdx/article/details/51014357 