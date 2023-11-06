---
title: firstpost
date: 2023-11-06 20:58:39 +0800
categories: [Cryptopals, Set1]
tags: [demo]     # TAG 名称应始终小写
---


### Challenge 1 Convert hex to base64

The string:

```
49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d
```

Should produce:

```
SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t
```

So go ahead and make that happen. You'll need to use this code for the rest of the exercises.

#### Cryptopals Rule

Always operate on raw bytes, never on encoded strings. Only use hex and base64 for pretty-printing.

- Base64是一种将**二进制数据**编码成文本数据，以便在文本环境中进行传输和存储。
  - 基于64个字符表示二进制数据，因而得名“Base64”.
  - **常见用途：** Base64 编码常用于电子邮件传输（如将二进制附件编码为文本以在电子邮件中发送）、URL编码、数据传输和存储（例如在数据库中存储二进制数据），以及各种编程领域中的数据传输和处理。
  - Base64 编码的工作原理是**将原始二进制数据拆分成6位的块，然后将这些块映射到Base64字符集中的字符**。通常，Base64字符集包括A到Z、a到z、0到9以及两个额外的字符，通常是"+"和"/"。等号("=")通常用于填充以确保Base64编码的字符串长度是4的倍数。

### Solution：

#### Python代码：

```py
import base64

hex_string = "49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d"
base64_string = "SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t"

# 将hex转换成bytes
bit_string = bytes.fromhex(hex_string)
# 将转换成的bytes进行Base64编码，再用“utf-8”解码，便于比较是否转换正确
base64_string2 = base64.b64encode(bit_string).decode('utf-8')

print(base64_string == base64_string2)

```

#### 运行结果

```python
True
```

