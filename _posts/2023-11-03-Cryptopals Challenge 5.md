---
title: Cryptopals Challenge 5
date: 2023-11-03 8:22:20 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目

![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081822506.png)
### 重复密钥 XOR 加密
给定字符串
```
Burning 'em, if you ain't quick and nimble
I go crazy when I hear a cymbal
```
使用密钥 `ICE` 进行重复密钥 XOR 加密，可以得到以下结果：
```
0b3637272a2b2e63622c2e69692a23693a2a3c6324202d623d63343c2a26226324272765272
a282b2f20430a652e2c652a3124333a653e2b2027630c692b20283165286326302e27282f
```
## 解析

### 知识点
- 重复密钥 XOR 加密其实是维吉尼亚密码的一种变体，他的密钥比信息要短，密钥逐字节加密后重复使用，以覆盖整个信息。
- 维吉尼亚密码：是一种多表密码。与传统的凯撒密码（Caesar Cipher）不同，在维吉尼亚密码中，密钥是一个单词或短语，加密过程中，密钥中的字符逐个应用到明文文本的字符上，然后根据密钥字符的位置来执行字母的位移操作。
	![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081832421.png)

### 分析
> 我们可以先把密钥的长度扩展到与明文长度一直，进而逐字节异或即可。
> 1. 明文长度除以密钥长度，得到商和余数，便于扩展密钥长度
> 2. 给定的明文和密钥都是字符串，我们要先通过 `.encode()` 编码成字节串。
{: .prompt-tip }

## 解决方案

### 代码

```python
plaintext = "Burning 'em, if you ain't quick and nimble\nI go crazy when I hear a cymbal"  
key = "ICE"  
target_string = "0b3637272a2b2e63622c2e69692a23693a2a3c6324202d623d63343c2a26226324272765272a282b2f20430a652e2c652a3124333a653e2b2027630c692b20283165286326302e27282f"  
  
  
def repeating_xor(text: bytes, key: bytes) -> bytes:  
    # 明文长度除以密钥长度，得到商和余数，便于扩展密钥长度  
    quotient, remainder = divmod(len(text), len(key))  
    key_extend = bytes(key * quotient + key[:remainder])  
    # 逐个字节异或  
    return bytes([x ^ y for x, y in zip(text, key_extend)])  
  
  
# 先把字符串编码成字节串  
byte_string = plaintext.encode()  
byte_Key = key.encode()  
  
ciphertext = repeating_xor(byte_string, byte_Key)  
  
print(ciphertext.hex() == target_string)
```

### 运行结果

```
True
```

## 参考

[Set 1: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/20/set-1/)

[Cryptopals解题报告(一) | B1ank](https://blank-vax.github.io/2019/11/13/Cryptopals%E8%A7%A3%E9%A2%98%E6%8A%A5%E5%91%8A(%E4%B8%80)/)