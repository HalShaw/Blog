---

title: Bcrypt、PBKDF2、Scrypt三种密码哈希函数的区别及优缺点
date: 2019-12-04 23:14
tags: Bcrypt PBKDF2 Scrypt 
categories: 哈希函数
---

# 一、前言

常用加密密码的三个主要哈希函数有bcrypt、PBKDF2和scrypt，但是他们之间有什么优缺点和区别呢？以及如何结合盐值使用，下面就来探讨一下。

# 二、Bcrypt

Bcrypt由Niels Provos 和 David Mazieres制定，是Belgain Fundamnetal Research in Cryptology and Informa-tion Security的简称。是基于Blowfish密码法的密钥导出函数。在保护密钥的过程中会加盐，而且具有适应能力。随着时间的推移，可以增加迭代次数，不断放慢速度，抵抗暴力攻击。

Bcrypt是首选的哈希函数，是专门为密码设计的密钥导出函数。

其优势有：
- 基于Blowfish密码学，使用RAM中的查询表，在算法执行的过程中持续替换。这种表易于CPU处理，由于需要顺讯访问内存，而且是并行处理的，所以不适用与GPU，所以攻击者无法使用GUP硬件提升破解速度。
- 是专为密码设计的哈希算法，故意放慢速度（这对密码哈希是好事）。

<!--more-->

在网站或应用中动手实现这个算法：

- 先从`npm `中安装`bcrypt ` 包：

```
npm install bcrypt --save
```

- 然后把`bcrypt `引入`node `应用：

```
var bcrypt = require('bcrypt');
```

- `bcrypt `包含了生成盐值的方法，因此不再需要使用`crypto `库，也就不需要再同时引入`bcrypt `和`crypto `了。下面是一个`bcrypt `算法的简单实现:

```
function bcrypt_encrypt(username,password){
  bcrypt.gensalt(10, function(err, salt) {
    bcrypt.hash(password. salt, function(err,key){
        // 把用户名、密码哈希值和盐值存入数据库
    });
  });
}
```

上面函数接受函数名和密码作为参数，可以假设它们在用户创建或更新账号时输入。上面代码采用异步方式生成盐值，这也是推荐使用的方法，还可以同步分别生成：

```
var salt = bcrypt.gemSaltSync(10);
var hash = bcrypt.hashSync(password, salt);
```

盐值由`bcrypt.gensalt() `这个方法生成，其参数是循环次数（或称因子，默认为10）和一个回调，后者返回可能出现的错误和得到的盐值。
哈希值由`bcrypt.hash() `方法生成，这个方法的参数为要计算哈希值的密码和刚生成 的盐值，回调返回生成的哈希值。

# 三、PBKDF2

PBKDF2全称为Password-Based Key Derivation Function 2，由RSA Laboratoies制定。它在输入（密码）上应用一个伪随机函数（例如哈希、暗码或HMAC），此外还会加盐，这个过程会重复多次，最后得到一个密钥。

主要优势：
- 经受住了时间的考验，而且过去这些年是大量研究的课题
- 1Password、LastPass等密码管理系统采用的算法
- Node crypto 库原生支持的标准方法

`PBKDF2 ` 是Node crypto自带的哈希算法，使用起来十分简单，首先，在Node应用中引入crypto库：

```
var crypto = require('crypto');

function pbkdf2_encrypt(username, password){
  crypto.randomBytes(32, function(ex, salt){
    crypto.pbkdf2(password, salt, 4096, 512, 'SHA-256', function(err, key) {
    if (err) throw err;
    // 把用户名、密码哈希值和盐值存入数据库
  });
  });
}
```

首先调用`crypto.randomBytes() `方法生成随机盐值，这个方法的参数是生成的数据的字节数（这里设为32字节），返回值是盐值。
然后调用`crypto.pbkdf2()`方法，传入下面的参数：
- 用户密码
- 盐值
- 迭代次数，即应用哈希函数的次数（这里设为4096）
- 密钥长度（这里设为512）
- 摘要函数（这里设为SHA-256）。支持的各个摘要函数可以通过`crypto.getHashes()`查看

这个函数返回的哈希值是十六进制格式的字符串，可以使用如下代买查看原始字符串：

```
console.log("key: " + key.toString('hex'));
```
采用同步方式的代码如下：

```
const salt = crypto.randomBytes(32);
var result = crypto.pbkdf2Sync(password, salt, 4096, 512, 'SHA-256');
```

# 四、Scrypt

对于`PBKDF2 `和`bcrypt `哪一个更好的争论从未停歇，但是`scrypt `采用的哈希算法与它们有很大 的差异。

`scrypt `的优势：
- 做了特殊设计，是硬件和内存密集型算法，攻击者需要实施大型攻击
- 是加密数字货币莱特币和狗狗货币背后采用的算法

与`PBKDF2 `和`bcrypt `相比，`scrypt `的主要优势是，若想要破解耗费异常多的硬件和内存，而破解`PBKDF2 `和`bcrypt `哈希的数据可以在少量硬件资源上并行实施几千次攻击，因为它们在设计时就没有要求大量资源。
- 首先安装`scrypt `模块

```
npm install scrypt --save
```

```
var scrypt = require('scrypt');
    crypto = require('crypto');

function scrypt_encrypt(username, password){
  crypto.randomBytes(32, function(ex, salt){
    scrypt.hash(password, {"N":16384, "r":8, "p":1}, 64, salt,
      function(err, key) {
        // 把用户名、密码哈希值和盐值存入数据库
      }
    }
  })
}
```
上面函数使用`crypto `库中的`crypto.randomBytes() `方法生成盐值，传入输出的字节数，然后输出盐值。
然后，根据盐值生成哈希值，调用`scrypt.hash() `方法，其接受下面几个参数：

- 需要计算哈希值的用户密码
- 一个对象，包含控制scrypt哈希行为的采纳数：
  - N：scrypt 最多使用多久时间（秒数）计算密钥（偶数）
  - r：计算密钥是最多使用多少字节RAM（整数）。默认是0
  - p：计算密钥是所用RAM占可用值的比例（0-1，换算成百分比）。默认值为0.5
- 得到的哈希值的长度
- 刚生成的盐值

同步代码：

```
const salt = crypto.randomBytes(256);
var result = scrypt.hashSync(key, {"N":16384, "r":8, "p":1}, 64, salt);
```
