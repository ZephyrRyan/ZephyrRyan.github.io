---
title: Vigenere-like cipher
date: 2023-10-31 12:13:22 +0800
categories:
  - Other Crypotgraphy
tags:
  - cryptography
  - exp1
---

## 题目
### Vigenere-like cipher
Write a program that allows you to “crack” ciphertexts generated using a Vigenere-like cipher, where byte-wise XOR is used instead of addition modulo 26.

Specifically, the ciphertext:
```
F96DE8C227A259C87EE1DA2AED57C93FE5DA36ED4EC87EF2C63AAE5B9A7EFFD673BE4ACF7BE8923CAB1ECE7AF2DA3DA44FCF7AE29235A24C963FF0DF3CA3599A70E5DA36BF1ECE77F8DC34BE129A6CF4D126BF5B9A7CFEDF3EB850D37CF0C63AA2509A76FF9227A55B9A6FE3D720A850D97AB1DD35ED5FCE6BF0D138A84CC931B1F121B44ECE70F6C032BD56C33FF9D320ED5CDF7AFF9226BE5BDE3FF7DD21ED56CF71F5C036A94D963FF8D473A351CE3FE5DA3CB84DDB71F5C17FED51DC3FE8D732BF4D963FF3C727ED4AC87EF5DB27A451D47EFD9230BF47CA6BFEC12ABE4ADF72E29224A84CDF3FF5D720A459D47AF59232A35A9A7AE7D33FB85FCE7AF5923AA31EDB3FF7D33ABF52C33FF0D673A551D93FFCD33DA35BC831B1F43CBF1EDF67F0DF23A15B963FE5DA36ED68D378F4DC36BF5B9A7AFFD121B44ECE76FEDC73BE5DD27AFCD773BA5FC93FE5DA3CB859D26BB1C63CED5CDF3FE2D730B84CDF3FF7DD21ED5ADF7CF0D636BE1EDB79E5D721ED57CE3FE6D320ED57D469F4DC27A85A963FF3C727ED49DF3FFFDD24ED55D470E69E73AC50DE3FE5DA3ABE1EDF67F4C030A44DDF3FF5D73EA250C96BE3D327A84D963FE5DA32B91ED36BB1D132A31ED87AB1D021A255DF71B1C436BF479A7AF0C13AA14794
```
was generated by encrypting English-language text using the following C program:
```C
#include <stdio.h>  
#define KEY_LENGTH 2 // Can be anything from 1 to 13  
   
main(){  
  unsigned char ch;  
  FILE *fpIn, *fpOut;  
  int i;  
  unsigned char key[KEY_LENGTH] = {0x00, 0x00};  
  /* of course, I did not use the all-0s key to encrypt */  
   
  fpIn = fopen("ptext.txt", "r");  
  fpOut = fopen("ctext.txt", "w");  
   
  i=0;  
  while (fscanf(fpIn, "%c", &ch) != EOF) {  
    /* avoid encrypting newline characters */    
    /* In a "real-world" implementation of the Vigenere cipher,   
       every ASCII character in the plaintext would be encrypted.  
       However, I want to avoid encrypting newlines here because   
       it makes recovering the plaintext slightly more difficult... */  
    /* ...and my goal is not to create "production-quality" code =) */  
    if (ch!='\n') {  
      fprintf(fpOut, "%02X", ch ^ key[i % KEY_LENGTH]); // ^ is logical XOR      
      i++;  
      }  
    }  
   
  fclose(fpIn);  
  fclose(fpOut);  
  return;  
}
```
(Of course, when encrypting I used a random key length and chose each byte of the key at random.) The plaintext contains upper- and lower-case letters, punctuation, and spaces, but no numbers.  
Recovered the original plaintext.
### 类维吉尼亚密码
破解类维吉尼亚密码，其使用 xor 而不是模 26 加密。
## 解析

### 知识点
- 维吉尼亚密码：是使用一系列[凯撒密码]( https://zh.wikipedia.org/wiki/%E5%87%AF%E6%92%92%E5%AF%86%E7%A0%81 "凯撒密码")组成密码字母表的加密算法，属于[多表密码]( https://zh.wikipedia.org/w/index.php?title=%E5%A4%9A%E8%A1%A8%E5%AF%86%E7%A0%81&action=edit&redlink=1 "多表密码（页面不存在）")的一种简单形式。下图是维吉尼亚密码加解密表格
	![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311120027154.png)
	![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311120029434.png)
- 维吉尼亚密码的破译：对包括维吉尼亚密码在内的所有多表密码的破译都是以字母频率为基础的，但直接的频率分析却并不适用。**破译维吉尼亚密码的关键在于它的密钥是循环重复的。**
- 对于密钥长度的求解：密文写成矩阵形式，其中列数与假定的密钥长度一致，将每一列的重合指数单独计算，并求得平均重合指数。对于所有可能的密钥长度，平均重合指数最高的最有可能是真正的密钥长度。


### 分析
> 题目中的信息总是关键的，我们看提供的 C 代码，可以知道，密钥长度在 1~13 的范围内，而且其明文只包含大小写字母、标点、空格，这是我们暴力破解，得出密钥的关键，这其实还是一个**密钥重复使用问题**。
{: .prompt-tip }

> 思路：先求解密钥长度，再将密文进行分割组合，使用相同密钥字符的组合在一起，从而破解出该密钥字符，进而得出整个密钥。
{: .prompt-info }

> 操作一定是在字节级别的，有字符串先转换成字节序列。
{: .prompt-warning }



## 解决方案

### 代码

**求解密钥长度**
```python
ciphertext = "F96DE8C227A259C87EE1DA2AED57C93FE5DA36ED4EC87EF2C63AAE5B9A7EFFD673BE4ACF7BE8923CAB1ECE7AF2DA3DA44FCF7AE29235A24C963FF0DF3CA3599A70E5DA36BF1ECE77F8DC34BE129A6CF4D126BF5B9A7CFEDF3EB850D37CF0C63AA2509A76FF9227A55B9A6FE3D720A850D97AB1DD35ED5FCE6BF0D138A84CC931B1F121B44ECE70F6C032BD56C33FF9D320ED5CDF7AFF9226BE5BDE3FF7DD21ED56CF71F5C036A94D963FF8D473A351CE3FE5DA3CB84DDB71F5C17FED51DC3FE8D732BF4D963FF3C727ED4AC87EF5DB27A451D47EFD9230BF47CA6BFEC12ABE4ADF72E29224A84CDF3FF5D720A459D47AF59232A35A9A7AE7D33FB85FCE7AF5923AA31EDB3FF7D33ABF52C33FF0D673A551D93FFCD33DA35BC831B1F43CBF1EDF67F0DF23A15B963FE5DA36ED68D378F4DC36BF5B9A7AFFD121B44ECE76FEDC73BE5DD27AFCD773BA5FC93FE5DA3CB859D26BB1C63CED5CDF3FE2D730B84CDF3FF7DD21ED5ADF7CF0D636BE1EDB79E5D721ED57CE3FE6D320ED57D469F4DC27A85A963FF3C727ED49DF3FFFDD24ED55D470E69E73AC50DE3FE5DA3ABE1EDF67F4C030A44DDF3FF5D73EA250C96BE3D327A84D963FE5DA32B91ED36BB1D132A31ED87AB1D021A255DF71B1C436BF479A7AF0C13AA14794"  
ciphertext_bytes = bytes.fromhex(ciphertext)  
ciphertext_bytes_length = len(ciphertext_bytes)  
  
# 根据所提供的C程序可知，密钥最长为13  
max_key_length = 13  
possible_key_length = [0] * max_key_length  
ciphertext_length = len(ciphertext_bytes)  
  
  
# 比较两个字节序列的相同位置的字节相同的指数，  
def compare_bytes(a: bytes, b: bytes):  
    fitting_index = 0  
    for le1, le2 in zip(a, b):  
        if le1 == le2:  
            fitting_index += 1  
    return fitting_index / min(len(a), len(b))  
  
  
# 获取密钥可能的长度，返回值是字典类型，记录每个长度对应的可能  
def guss_key_length(cipher_bytes: bytes):  
    fitting_index_dict = {}  
    for key_len in range(1, max_key_length + 1):  
        ciphertext_chunks = []  
        for i in range(0, ciphertext_length, key_len):  
            ciphertext_chunks.append(cipher_bytes[i:i + key_len])  
        chunks_length = len(ciphertext_chunks)  
        fitting_index = 0  
        for i in range(chunks_length):  
            fitting_sum = 0  
            for j in range(i + 1, chunks_length):  
                fitting_sum += compare_bytes(ciphertext_chunks[i], ciphertext_chunks[j])  
            fitting_index += (fitting_sum / (chunks_length - i))  
        fitting_index_dict[key_len] = fitting_index  
    return fitting_index_dict  
  
  
if __name__ == '__main__':  
    fitting_index =  guss_key_length(ciphertext_bytes)  
    for key, value in fitting_index.items():  
        print(key, ':', value.__round__(4))
```

**密钥长度结果**
```
1 : 5.4054
2 : 2.6859
3 : 1.5482
4 : 1.1983
5 : 0.9524
6 : 0.7179
7 : 4.7134
8 : 0.5238
9 : 0.4511
10 : 0.4307
11 : 0.4277
12 : 0.2911
13 : 0.248
```
>可以看到，密钥可能的长度为 1，其次为 7，然后是 2；
>当我们使用密钥长度为 1 的密钥时，发现并没有符合的密钥，于是使用密钥长度为 7

```python
import getKeyLength  
  
# 当我们使用密钥长度为 1 的密钥时，发现并没有符合的密钥
key_length = 7  

# 将密文按照密钥长度进行分割组合  
def split_bytes_by_modulo(input_bytes, key_len):  
    result_bytes = [b""] * key_len  # 创建一个包含7个空字节串的列表  
    for index, byte in enumerate(input_bytes):  
        # 根据位置将字节分组  
        group_index = index % key_len  
        result_bytes[group_index] += bytes([byte])  
    return result_bytes  
  
  
# 判断异或结果是否为字母或标点或空格  
def judge(x):  
    return 65 <= x <= 90 or 97 <= x <= 122 or 32 <= x <= 33 or x == 39 or 44 <= x <= 46  
  
  
# 获取密钥，基本原理是看异或结果是否完全是字母或标点或空格，不是则不对  
def get_key(key_len: int, ciphertext_byte_chunks):  
    key = []  
    for i in range(key_len):  
        length = len(ciphertext_byte_chunks[i])  
        for k in range(256):  
            for l in range(length):  
                if not judge(ciphertext_byte_chunks[i][l] ^ k):  
                    break  
                if l == length - 1:  
                    key.append(k)  
  
    return key  
  
  
# 来自Cryptopals set1 challenge6，用于解出明文  
def repeating_xor(text: bytes, key) -> bytes:  
    # 明文长度除以密钥长度，得到商和余数，便于扩展密钥长度  
    quotient, remainder = divmod(len(text), len(key))  
    key_extend = bytes(key * quotient + key[:remainder])  
    # 逐个字节异或  
    return bytes([x ^ y for x, y in zip(text, key_extend)])  
  

ciphertext_byte_chunk = split_bytes_by_modulo(getKeyLength.ciphertext_bytes, key_length)  
final_key = get_key(key_length, ciphertext_byte_chunk)  
plaintext = repeating_xor(getKeyLength.ciphertext_bytes, final_key)  
print(plaintext.decode())  
print(final_key)
```

运行结果：
```
Cryptography is the practice and study of techniques for, among other things, secure communication in the presence of attackers. Cryptography has been used for hundreds, if not thousands, of years, but traditional cryptosystems were designed and evaluated in a fairly ad hoc manner. For example, the Vigenere encryption scheme was thought to be secure for decades after it was invented, but we now know, and this exercise demonstrates, that it can be broken very easily.
[186, 31, 145, 178, 83, 205, 62]

```
## 参考

[维吉尼亚密码 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E7%BB%B4%E5%90%89%E5%B0%BC%E4%BA%9A%E5%AF%86%E7%A0%81#)

[现代密码学作业](https://www.cnblogs.com/hierath/articles/17809970.html)

[现代密码学解题报告(一) | B1ank](https://blank-vax.github.io/2019/11/13/%E7%8E%B0%E4%BB%A3%E5%AF%86%E7%A0%81%E5%AD%A6%E8%A7%A3%E9%A2%98%E6%8A%A5%E5%91%8A%EF%BC%88%E4%B8%80%EF%BC%89/)