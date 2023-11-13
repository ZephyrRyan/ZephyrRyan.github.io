---
title: Cryptopals Challenge 9
date: 2023-11-5 14:35:12 +0800
categories:
  - Cryptopals
  - Set2
tags:
  - cryptography
  - cryptopals
---

## 题目

[Challenge 9 Set 2 - The Cryptopals Crypto Challenges](https://cryptopals.com/sets/2/challenges/9)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311121435434.png)


## 解析

### 知识点
- PKCS #7 是一种密码学标准，定义了用于加密消息的格式和语法。它通常用于数字签名和加密操作，以确保数据的机密性和完整性。
	- 其填充快是在尾部填充 k - (l mod k) 个八进制数，，所有这些八进制数的值都是 k - (l mod k)，其中 l 是输入的长度。
	- 因此，如果报文长度比数据块少 x 个字节，那么就需要填充 x 个字节。
		![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311121513027.png)


### 分析
> 给出的明文比块大小少 4 个字节，因此填充 4 个 `\04` 字节即可。



## 解决方案

### 代码

```python
def PKCS7_pad(plaintext: bytes, block_size: int) -> bytes:  
    # 计算需要填充的字节  
    padding_byte = block_size - len(plaintext) % block_size  
    # 进行填充  
    plaintext += padding_byte.to_bytes(1, "big") * padding_byte  
    """  
    to_bytes(x, "big") 是整数对象的一个方法，用于将整数表示为指定字节顺序的字节数组。  
    参数解释：    第一个参数 x 表示希望将整数转换为多少字节的字节数组。  
    第二个参数 "big" 表示字节的顺序，这里是大端字节顺序（从高位到低位的顺序）。  
    """    return plaintext  
  
  
def main():  
    plaintext = "YELLOW SUBMARINE"  
    block_size = 20  
    plaintext_bytes = plaintext.encode()  
    print(PKCS7_pad(plaintext_bytes, block_size))  
  
  
if __name__ == "__main__":  
    main()
```

### 运行结果

```
b'YELLOW SUBMARINE\x04\x04\x04\x04'
```

## 参考

[Set 2: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/24/set-2/)