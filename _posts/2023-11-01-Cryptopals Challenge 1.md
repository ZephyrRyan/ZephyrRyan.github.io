---
title: Cryptopals Challenge 1
date: 2023-11-01 20:58:39 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目

![题目.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/QQ%E6%88%AA%E5%9B%BE20231107223925.png)
### 将十六进制转换为Base64
16 进制字符串:

```
49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d
```

要转换成为:

```
SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t
```



> 在处理数据时，应该始终使用原始的字节数据，而不是已编码的字符串数据。只有在需要打印的时候，才需要 16 进制和 base64 编码。
{: .prompt-tip }


## 解析
### 知识点

- Base64是一种将**二进制数据**编码成文本数据，以便在文本环境中进行传输和存储。
  - 基于64个字符表示二进制数据，因而得名“Base64”.
  - **常见用途：** Base64 编码常用于电子邮件传输（如将二进制附件编码为文本以在电子邮件中发送）、URL编码、数据传输和存储（例如在数据库中存储二进制数据），以及各种编程领域中的数据传输和处理。
  - Base 64 编码的工作原理是**将原始二进制数据拆分成 6 位的块，然后将这些块映射到 Base 64 字符集中的字符**。通常，Base 64 字符集包括 A 到 Z、a 到 z、0 到 9 以及两个额外的字符，通常是"+"和"/"。等号 ("=")通常用于填充以确保 Base 64 编码的字符串长度是 4 的倍数。

### 分析
> 只需要将 16 进制的字符串转换成二进制数据，然后使用 base64 编码即可得到结果。

## 解决方案
### 代码

```python
import base64

hex_string = "49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d"
base64_string = "SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t"

# 将hex转换成bytes
bit_string = bytes.fromhex(hex_string)
# 将转换成的bytes进行Base64编码，再用“utf-8”解码，便于比较是否转换正确
base64_string2 = base64.b64encode(bit_string).decode('utf-8')

print(base64_string == base64_string2)

```

### 运行结果

```python
True
```

