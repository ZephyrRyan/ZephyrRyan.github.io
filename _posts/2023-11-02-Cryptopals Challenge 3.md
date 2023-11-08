---
title: Cryptopals Challenge 3
date: 2023-11-08 09:53:20 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目

![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081037705.png)

### 单字节 XOR 加密
被单字节 XOR 加密后的 16 进制字符串：
```
1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736
```
要求找到被用作加密的字节，解密这条信息。

> 可以使用 "ETAOIN SHRDLU" 笑话
{: .prompt-tip }

## 解析

### 知识点

#### 单字节 XOR 加密
>**单字节 XOR 加密**，就是加密密钥的大小为一个字节，明文中每个与加密密钥 XOR，即可得到密文。
>例如，我们使用 key=69 对明文 `abcd` 进行加密，则 a, b, c, d 分别于 69 异或，得到 `$'&!` 密文。

![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081049242.png)

#### 单字节 XOR 解密
>对于**解密**，XOR 有个特性，如果 `a = b ^ c` 那么 `b = a ^ c`，因而我们将密文再做一次单字节 XOR 加密即可。

#### 字母频率
>字母频率是指单个字母在正常语言中平均出现的次数，对字母频率的分析是语言识别的一种初级方法，对于本题，我们可以查看解密出的信息是否符合字母频率，进而判断是否解密成功。
```
occurance_english = { 'a': 8.2389258, 'b': 1.5051398, 'c': 2.8065007, 'd': 4.2904556, 'e': 12.813865, 'f': 2.2476217, 'g': 2.0327458, 'h': 6.1476691, 'i': 6.1476691, 'j': 0.1543474, 'k': 0.7787989, 'l': 4.0604477, 'm': 2.4271893, 'n': 6.8084376, 'o': 7.5731132, 'p': 1.9459884, 'q': 0.0958366, 'r': 6.0397268, 's': 6.3827211, 't': 9.1357551, 'u': 2.7822893, 'v': 0.9866131, 'w': 2.3807842, 'x': 0.1513210, 'y': 1.9913847, 'z': 0.0746517 }
```
>所谓"ETAOIN SHRDLU" 笑话，这段字符串实际上是按照 twitter 上大量文字中的使用频率的高低产生的。
### 分析
> 我们已知加密后的字符串，且已知加密密钥是一个字节，也就是[0-255]之间的某个字节，所以，我们可以使用暴力破解，将[0-255]之间的每个字节同字符串进行单字节 XOR 加密，得到的**可读性**的语言就是明文。
{: .prompt-tip }

> 对于**可读性**的判断，我们可以使用字母频率来判断，这样更加“规范”。因此，我们可以把使用[0-255]每个字节异或后的结果同字母频率表相比较，**拟合程度最好的**的即为正确答案。
> 对于**拟合程度**的判断，我们可以定义一个 Fitting Quotient，它是文本中字母频率同英语中字母频率绝对差值的平均值。
> ![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081114941.png)
{: .prompt-tip }


## 解决方案

### 代码

```python
import base64  
from collections import Counter  
  
character_frequencies = {  
    # 字母频率表数据来源：维基百科  
    'a': 8.167, 'b': 1.492, 'c': 2.782, 'd': 4.253,  
    'e': 12.702, 'f': 2.228, 'g': 2.015, 'h': 6.094,  
    'i': 6.966, 'j': 0.153, 'k': 0.772, 'l': 4.025,  
    'm': 2.406, 'n': 6.749, 'o': 7.507, 'p': 1.929,  
    'q': 0.095, 'r': 5.987, 's': 6.327, 't': 9.056,  
    'u': 2.758, 'v': 0.978, 'w': 2.360, 'x': 0.150,  
    'y': 1.974, 'z': 0.074  
}  
  
dist = list(character_frequencies.values())  
  
  
def compute_fitting_index(string: bytes) -> float:  
    # Counter对象  
    counter = Counter(string)  
    dist_text = [  
        # ord 获取字符的ASCII值  
        (counter[ord(letter)] * 100) / len(string)  
        for letter in character_frequencies  
    ]  
    return sum([abs(n - m) for n, m in zip(dist, dist_text)]) / len(dist_text)  
  
  
# 逐字节异或，python的异或有整数之间的按位异或，有布尔值的异或，有逐字节的异或，没有bit级别的异或  
def single_byte_xor(string: bytes, key: int) -> bytes:  
    return bytes([b ^ key for b in string])  
  
  
def decipher(ciphertext: bytes):  
    key = None  
    plaintext = None  
    min_index = 10 ^ 9  
    for k in range(256):  
        # 逻辑是每个字节与密文异或，取拟合程度最高的  
        candidate_plaintext = single_byte_xor(ciphertext, k)  
        fitting_index = compute_fitting_index(candidate_plaintext)  
  
        if fitting_index < min_index:  
            key, plaintext, min_index = k, candidate_plaintext, fitting_index  
    plaintext = plaintext.decode("ASCII")  
    return plaintext, key  
  
  
def main():  
    cipherString = "1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736"  
    # 先把16进制字符串转换成字节序列  
    ciphertext = bytes.fromhex(cipherString)  
    plaintext, key = decipher(ciphertext)  
    print(plaintext)  
    print(key)  
  
  
main()
```

>1. Counter () 类
>	1. 位于 `collections` 模块中，需要导入 `from collections import Counter` ；
>	2. 用于快速、方便地计数可迭代对象中元素的出现次数。
>	3. 使用计数对象的键（元素）来访问其对应的计数值。例如： `count_of_2 = counter[2] # 获取元素 2 的计数`。
>	4. 获取一个不存在的元素的计数，`Counter` 不会引发 `KeyError` 错误，而是返回默认值0。
>2. ord () 的作用是获取字符对应的 ASCII 数值。
{: .prompt-tip }

### 运行结果

```python
Cooking MC's like a pound of bacon
88
```

## 参考
[Deciphering Single-byte XOR Ciphertext | Codementor](https://www.codementor.io/@arpitbhayani/deciphering-single-byte-xor-ciphertext-17mtwlzh30)
[Fetching Title #ca7d ]( https://blank-vax.github.io/2019/11/13/Cryptopals%E8%A7%A3%E9%A2%98%E6%8A%A5%E5%91%8A (%E4%B8%80)/)
[Set 1: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/20/set-1/)
[Letter frequency - Wikipedia](https://en.wikipedia.org/wiki/Letter_frequency)