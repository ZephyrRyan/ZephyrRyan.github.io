---
title: Cryptopals Challenge 12
date: 2023-11-06 11:46:33 +0800
categories:
  - Cryptopals
  - Set2
tags:
  - cryptography
  - cryptopals
---

## 题目

[Challenge 12 Set 2 - The Cryptopals Crypto Challenges](https://cryptopals.com/sets/2/challenges/12)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311211150985.png)
### 逐字节 ECB 解密
解密使用AES的ECB模式加密的UNKNOWN_STRING
- 题目是用一个**未知的密钥**和 **ECB 模式**的加密函数加密一个**未知的字符串**，要求解密出这个字符串。
- 题目的步骤是：
    - 用不同长度的相同字节作为输入，推断出**密钥长度**。
    - 根据输出中是否有重复的块，判断是否使用了**ECB模式**。
    - 用一个比密钥长度少一个字节的输入，构建一个**字典**，找出未知字符串的**每个字节**。

## 解决方案

### 代码

```python
import base64  
from Cryptodome.Cipher import AES  
from Challenge9_ImplementPKCS7padding import PKCS7_pad  
from Challenge10_ImplementCBCmode import PKCS7_trim  
from Challenge11_ECBCBC_detection_oracle import generate_random_bytes, AES_ECB_encrypt  
  
  
ECB_KEY = generate_random_bytes(16)  
  
UNKNOWN_STRING = "Um9sbGluJyBpbiBteSA1LjAKV2l0aCBteSByYWctdG9wIGRvd24gc28gbXkgaGFpciBjYW4gYmxvdwpUaGUgZ2lybGllcyBvbiBzdGFuZGJ5IHdhdmluZyBqdXN0IHRvIHNheSBoaQpEaWQgeW91IHN0b3A/IE5vLCBJIGp1c3QgZHJvdmUgYnkK"  
b64_msg = UNKNOWN_STRING.encode()  
UNKNOWN_BYTES = base64.b64decode(b64_msg)  
  
  
def encryption_oracle(your_string: bytes):  
    msg = b'The unkonwn string is:\n'  
    plaintext = your_string + msg + UNKNOWN_BYTES  
  
    padded_msg = PKCS7_pad(plaintext, AES.block_size)  
    ciphertext = AES_ECB_encrypt(padded_msg, ECB_KEY)  
    return ciphertext  
  
  
def detect_block_size():  
    feed = b"A"  
    length = 0  
    while True:  
        cipher = encryption_oracle(feed)  
        feed += feed  
        if length and len(cipher) - length > 1:  
            return len(cipher) - length  
        length = len(cipher)  
  
  
# 这里的暴力破解进行了优化，把常见的字符排在前面，遍历的时候先选择常见字符，降低循环次数  
def break_AEC_ECB(block_size: int):  
    common = list(range(ord('a'), ord('z'))) + list(range(ord('A'), ord('Z'))) + [ord(' ')] + list(  
        range(ord('0'), ord('9')))  
    rare = [i for i in range(256) if i not in common]  
    possibilities = bytes(common + rare)  
  
    plaintext = b''  
    check_length = block_size  
  
    while True:  
        prepend = b'A' * (block_size - 1 - (len(plaintext) % block_size))  
        actual = encryption_oracle(prepend)[:check_length]  
  
        found = False  
        for byte in possibilities:  
            value = bytes([byte])  
            your_string = prepend + plaintext + value  
            produced = encryption_oracle(your_string)[:check_length]  
            if actual == produced:  
                plaintext += value  
                found = True  
                break  
        if not found:  
            plaintext = PKCS7_trim(plaintext)  
            print(f"Plaintext: \n{plaintext.decode('ascii')}")  
            return  
  
        if len(plaintext) % block_size == 0:  
            check_length += block_size  
  
  
def main():  
    block_size = detect_block_size()  
    print(f"Block Size is {block_size}")  
  
    break_AEC_ECB(block_size)  
  
  
if __name__ == "__main__":  
    main()
```

### 运行结果

```
Block Size is 16
Plaintext: 
The unkonwn string is:
Rollin' in my 5.0
With my rag-top down so my hair can blow
The girlies on standby waving just to say hi
Did you stop? No, I just drove by

```

## 参考
