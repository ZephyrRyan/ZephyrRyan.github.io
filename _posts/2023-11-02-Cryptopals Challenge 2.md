---
title: Cryptopals Challenge 2
date: 2023-11-02 23:38:02 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目
![题目.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/%E9%A2%98%E7%9B%AE.png)

### 固定异或
写一个函数，可以接收两个等长的数据，然后输出他们的异或。
当输入这段字符串（明文）
```
1c0111001f010100061a024b53535009181c
```
在 16 进制解码后，和以下字符串（加密流）异或：
```
686974207468652062756c6c277320657965
```
应该产生（密文）：
```
746865206b696420646f6e277420706c6179
```
## 解析

### 知识点
1. XOR
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311072347697.png)
在密码学中，XOR 加密是明文和加密流进行逐 bit 的异或，得到密文。
2. 字节串
>1) 字节串由字节组成，其中每个字节通常包含 8 位二进制数据，常用于处理二进制数据.
>2) 比特流是 01 序列，8 个 bit 组成一个字节，即字节串就是**把 8 个 bit 作为一个对象处理**；字符串则是字节经过编码后的文本类型的数据，如 ASCII 和 Unicode 编码。
>3) 在 python 中，字节串以 `b` 前缀开始，例如 `b"Hello"`，其使用 ASCII 编码，与 `bytes([72, 101, 108, 108, 111])` 等价，与 b'\x 48\x 65\x 6 c\x 6 c\x 6 f'等价，他们表示的是同一给=个字节序列。
{: .prompt-info }

### 分析
> 题目给我们的是 16 进制的字符串，我们输入到 python 中的也是字符串，我们需要先利用 `bytes.fromhex（）` 将其转换成字节序列，然后进行逐比特异或。
{: .prompt-tip }


## 解决方案

### 代码

```python
from base64 import b64encode  
  
hex_string1 = "1c0111001f010100061a024b53535009181c"  
hex_string2 = "686974207468652062756c6c277320657965"  
target_string = "746865206b696420646f6e277420706c6179"  
  
# 转换成字节序列  
byte_seq1 = bytes.fromhex(hex_string1)  
byte_seq2 = bytes.fromhex(hex_string2)  
  
  
def xor_bytes(seq1, seq2):  
    # zip(seq1, seq2)将各个比特对应，然后for循环进行逐字节的异或  
    ciphertext = b''.join([bytes(b1 ^ b2 for b1, b2 in zip(seq1, seq2))])  
    return ciphertext  
  
  
# 上面的函数返回的是字节序列，我们还需要将其转化为16进制字符串  
result = xor_bytes(byte_seq1, byte_seq2).hex()  
print(result == target_string)
```
### zip() 函数
> `zip()` 函数是 Python 内置函数，将多个可迭代对象（例如列表、元组等）合并在一起，创建一个新的可迭代对象，用于同时处理几个输入序列，其中包含每个输入可迭代对象**对应位置**的元素对。简而言之，就是
> 1. **将多个序列对应位置的元素组成一个元组，生成一个新的序列。**
> 2. zip 函数不要求每个可迭代对象中元素个数一致，他以最少元素个数的为基准。
> 3. Zip 函数的返回值的类型是迭代器对象。
{: .prompt-info }
### 运行结果

```python
True
```