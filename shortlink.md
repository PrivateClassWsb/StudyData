## 创建短链接

1、通过 UUID 增加原始链接的随机性。

```java
originUrl += UUID.randomUUID().toString();
//UUID 由 32 个十六进制字符和 4 个 - 连接符组成，格式为：
//xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx
```

2、通过 Hash 算法将原始链接转换成一个 Hash 码。

> ```java
> int i = MurmurHash.hash32(str);
> ```

由于我们并不关心反向解密的难度，更重要的是关注哈希的运算速度和冲突概率。因此，这里选择用 Google 开发的 MurmurHash 算法。

MurmurHash 是一种非加密型哈希函数，与其他流行的哈希函数相比，MurmurHash 在处理规律性较强的键时具有更好的随机分布特性。由于它是非加密型的，相比 MD5、SHA 等加密算法，MurmurHash 的性能要高得多（实际上是 MD5 等加密算法的十倍以上）。

3、得到的10进制哈希值还是比较长，还要再次缩减。通过Base62编码。

> 得到的10进制哈希值可能为负数，为了Base62编码，所以要现将其变为正数：
>
> ```java
> long num = i < 0 ? Integer.MAX_VALUE - (long) i : i;
> ```

## 布隆过滤器

用于检索一个元素是否在一个集合中。

布隆过滤器说这个数不存在则一定不存，布隆过滤器说这个数存在可能不存在（误判）。



























