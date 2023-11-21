---
title: Cryptopals Challenge 11
date: 2023-11-05 14:27:47 +0800
categories:
  - Cryptopals
  - Set2
tags:
  - cryptography
  - cryptopals
---

## 题目

[Challenge 11 Set 2 - The Cryptopals Crypto Challenges](https://cryptopals.com/sets/2/challenges/11)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311151428363.png)

### 甄别 ECB 和 CBC 加密
>1. 写一个生成随机 16 个字节长度的密钥
>2. 写一个 encryption_oracle()函数，能够随机的采用 ECB 或者 CBC 进行加密。
>3. 要求：再输入的文本前后都添加 5-10 个随机的字节。
>4. 最后实现甄别 encryption_oracle()使用了 ECB 还是 CBC 加密。

## 解析

> 1. 写一个生成随机 16 个字节长度的密钥：
> 	os.Urandom(n)用于生成指定长度 n 的随机字节串。这个函数通常用于生成密码学上安全的随机数，例如用作加密密钥或初始化向量。
> 2. 写一个 encryption_oracle ()函数：
> 	random.Randint (0,1)，选择用哪种加密方法
> 3. 前后添加随机字节，仍然可以使用 os.Urandom (n)
> 4. 如何甄别是否使用了 ECB 呢？查看密文中是否存在重复的文本块即可。因为其他加密模式密文块碰撞概率为 0. 具体分析可查看[Cryptopals - Detect AES in ECB mode - Braincoke | Security Blog](https://braincoke.fr/write-up/cryptopals/cryptopals-detect-aes-in-ecb-mode/)
{: .prompt-tip }

> **Oracle**
> 1. "Oracle" 常用于描述一种理论上的理想化工具或黑盒子，它可以接受特定类型的查询并提供相应的响应，但不透露内部的具体细节。
> 2. 常使用 "Oracle" 来描述攻击模型，其中攻击者能够与一个理想化的 Oracle 进行交互，以获取关于密码系统的有用信息。
{: .prompt-info }



## 解决方案

### 代码

```python
import os  
import random  
from Challenge9_ImplementPKCS7padding import PKCS7_pad  
from Cryptodome.Cipher import AES  
  
  
# ECB加密  
def AES_ECB_encrypt(plaintext: bytes, key: bytes):  
    cipher = AES.new(key, AES.MODE_ECB)  
    ciphertext = cipher.encrypt(PKCS7_pad(plaintext, AES.block_size))  
    return ciphertext  
  
  
# CBC加密  
def AES_CBC_encrypt(plaintext: bytes, IV: bytes, key: bytes):  
    cipher = AES.new(key, AES.MODE_CBC, IV)  
    ciphertext = cipher.encrypt(plaintext)  
    return ciphertext  
  
  
# 将字节分块  
def bytes_to_chunks(string: bytes, chunk_size: int):  
    chunks = [string[i:i + chunk_size] for i in range(0, len(string), chunk_size)]  
    return chunks  
  
  
# 产生随机的指定长度的字节序列  
def generate_random_bytes(key_length: int):  
    # os.urandom(n)用于生成指定长度 n 的随机字节串。这个函数通常用于生成密码学上安全的随机数，例如用作加密密钥或初始化向量。  
    return os.urandom(key_length)  
  
  
def msg_pad(msg: bytes):  
    # 左侧填充5-10个字节  
    padded_msg = os.urandom(random.randint(5, 10))  
    padded_msg += msg  
    # 右侧填充5-10个字节  
    padded_msg += os.urandom(random.randint(5, 10))  
  
    # 填充字节后，需要再进行PKCS7填充  
    if len(padded_msg) % AES.block_size:  
        return PKCS7_pad(padded_msg, AES.block_size)  
    else:  
        return padded_msg  
  
  
def encryption_oracle(msg: bytes):  
    # 是否采用CBC模式  
    CBC_mode = random.randint(0, 1)  
    key = generate_random_bytes(16)  
    padded_msg = msg_pad(msg)  
    if CBC_mode:  
        print("采用CBC加密")  
        IV = generate_random_bytes(AES.block_size)  
        ciphertext = AES_CBC_encrypt(padded_msg, IV, key)  
    else:  
        print("采用ECB加密")  
        ciphertext = AES_ECB_encrypt(padded_msg, key)  
    return ciphertext  
  
  
# 甄别方法，看加密后的密文块是否有重复，有重复是ECB模式，否则是CBC模式  
def detect_AES(ciphertext: bytes, chunk_size: int):  
    chunks = bytes_to_chunks(ciphertext, chunk_size)  
    # set 是一种无序、可变的集合数据类型，用于存储唯一的元素。  
    unique_set = set(chunks)  
    if len(unique_set) < len(chunks):  
        return "检测为：ECB模式"  
    else:  
        return "检测为：CBC模式"  
  
  
def main():  
    msg = "Yellow SubmarineTwo One Nine TwoYellow Submarine"*2 
    msg_bytes = msg.encode()  
    # 重复多次，便于直观查看甄别效果
    for _ in range(10):  
        print(detect_AES(encryption_oracle(msg_bytes), AES.block_size))  
        print("--------------")  
  
  
if __name__ == "__main__":  
    main()
```

> 值得注意的是，msg 要足够重复足够长，否则密文中不一定有重复的块，进而导致 ECB 被识别成了 CBC。
{: .prompt-warning }

### 运行结果

```
采用ECB加密
检测为：ECB模式
--------------
采用CBC加密
检测为：CBC模式
--------------
采用CBC加密
检测为：CBC模式
--------------
采用ECB加密
检测为：ECB模式
--------------
采用CBC加密
检测为：CBC模式
--------------
采用CBC加密
检测为：CBC模式
--------------
采用CBC加密
检测为：CBC模式
--------------
采用ECB加密
检测为：ECB模式
--------------
采用ECB加密
检测为：ECB模式
--------------
采用ECB加密
检测为：ECB模式
--------------
```

## 参考

[writeup.md](https://github.com/Rajil1213/Cryptopals-Set-2/blob/master/writeup/writeup.md)

[Set 2: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/24/set-2/)

[Cryptopals Guided Tour - 11 - An ECB/CBC Detection Oracle - YouTube](https://www.youtube.com/watch?v=i6BhbO_g4KI&t=335s&ab_channel=NCCGroupGlobal)

[Cryptopals - Detect AES in ECB mode - Braincoke | Security Blog](https://braincoke.fr/write-up/cryptopals/cryptopals-detect-aes-in-ecb-mode/)

