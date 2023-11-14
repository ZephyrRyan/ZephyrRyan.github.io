---
title: MT3C Level 2
date: 2023-11-04 11:30:41 +0800
categories:
  - MT3C
  - Level
tags:
  - cryptography
  - mt3c
  - exp1
---

## 题目

[cracking-sha1-hashed-passwords](https://mysterytwister.org/challenges/level-2/cracking-sha1-hashed-passwords)
### Cracking Sha1-Hashed passwords
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311121400931.png)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311121400992.png)

已知 password 经过 sha1-hash 后的值和键盘输入痕迹，破解密码

## 解析

### 知识点
-  `SHA-1`（Secure Hash Algorithm 1）是一种密码散列函数，美国国家安全局设计，并由美国国家标准技术研究所（NIST）发布为联邦资料处理标准（FIPS）。SHA-1可以生成一个被称为消息摘要的160位（20字节）散列值，散列值通常的呈现形式为40个十六进制数。

### 分析
> 根据键盘上的指纹痕迹，我们可以判断密码所包含的字符`['q', 'Q', 'w', 'W', 'n', 'N', 'i', 'I', '+', '*', '5', '%', '8', '(', '0', '=']`，以及最小的密码长度为 `8`，注意到小键盘上只有 2468 有痕迹，很有可能代表上下左右。
> 穷举即可，从所有字符串中选出密码长度个（可重复）字符，组成密码，进行 sha1 运算，结果与给定的密码 sha1-value 相比较，相同即可得到答案。
{: .prompt-tip }


>  `product` 是 Python 标准库中 `itertools` 模块中的一个函数。它用于计算多个可迭代对象的笛卡尔积。简而言之，`product` 返回所有可能的组合，其中每个组合由输入的可迭代对象中的一个元素组成。
> 	`Itertools.Product (*iterables, repeat=1)`
> 	1. `*iterables`：这是一个可变参数，你可以传递一个或多个可迭代对象作为参数。这些可迭代对象的元素将被用来生成组合。
> 	2. `repeat`：这是一个可选参数，默认值为 `1`。它表示每个输入可迭代对象要重复使用的次数。如果设置为大于 1 的值，生成的组合中的每个元素都会在输出中重复多次。
{: .prompt-info }


## 解决方案

### 代码

```python
# -*- coding: utf-8 -*-  
import hashlib  
import itertools  
  
  
def is_equal(c):  
    return hashlib.sha1(c.encode('utf8')).hexdigest() == '67ae1a64661ac8b4494666f58c4822408dd0a3e4'  
  
  
def main():  
    k = '(Q=Win5qw80IN*%'  
    for i in range(8, 20):  
        keys = itertools.permutations(k, i)  
        for key in keys:  
            c = ''.join(key)  
            if is_equal(c):  
                print("The password is :", c)  
                return  
  
  
if __name__ == '__main__':  
    main()
```

### 运行结果

```
The password is : (Q=win*5
```

## 参考
