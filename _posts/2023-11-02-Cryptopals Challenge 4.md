---
title: Cryptopals Challenge 4
date: 2023-11-02 18:00:55 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目

[Challenge 4 Set 1 - The Cryptopals Crypto Challenges](https://cryptopals.com/sets/1/challenges/4)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311081802901.png)

### 检测单字节异或
给出的文件中有一个60字符的字符串经过单字节 XOR 加密，找出他。
[点击此处下载文件](https://cryptopals.com/static/challenge-data/4.txt)
## 解析

### 分析
> 这道 challenge 是以 challenge3 为基础的，不同的是，尽管 challenge4 是一个密文，但他藏在多个文本中，因此，我们只需要利用 challenge3 中的代码，先选出每一串字符最有可能的明文，再从其中选出拟合程度最高的明文，即为正确答案。
{: .prompt-tip }


## 解决方案

### 代码

```python
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
    min_index = 99999  
    for k in range(256):  
        # 逻辑是每个字节与密文异或，取拟合程度最高的  
        candidate_plaintext = single_byte_xor(ciphertext, k)  
        fitting_index = compute_fitting_index(candidate_plaintext)  
        if fitting_index < min_index:  
            key, plaintext, min_index = k, candidate_plaintext, fitting_index  
    return plaintext, key
    
with open('4.txt', 'r') as file:  
    data = file.read()  
  
# 把每列数据读入，并转换为字节序列，存储在列表中  
byte_string = []  
for line in data.split():  
    byte_line = bytes.fromhex(line)  
    byte_string.append(byte_line)  
  
# 选出每一行数据中，最有可能的解密出的明文，存储在新列表中，作为候选文本  
candidate_plaintext_list = []  
for line in byte_string:  
    candidate_plaintext, candidate_key = lf.decipher(line)  
    candidate_plaintext_list.append(candidate_plaintext)  
  
min_index = 99999  
plaintext = None  
# 从候选文本中选出拟合程度最好的明文  
for line in candidate_plaintext_list:  
    fitting_index = lf.compute_fitting_index(line)  
    if fitting_index < min_index:  
        plaintext, min_index = line, fitting_index  
plaintext = plaintext.decode("ASCII")  
print(plaintext)
```

### 运行结果

```
Now that the party is jumping
```

## 参考
[Deciphering Single-byte XOR Ciphertext | Codementor](https://www.codementor.io/@arpitbhayani/deciphering-single-byte-xor-ciphertext-17mtwlzh30)

[Set 1: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/20/set-1/)

[Letter frequency - Wikipedia](https://en.wikipedia.org/wiki/Letter_frequency)