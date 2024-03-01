---
title: Cryptopals Challenge 10
date: 2023-11-05 15:15:34 +0800
categories:
  - Cryptopals
  - Set2
tags:
  - cryptography
  - cryptopals
---

## 题目

[Challenge 10 Set 2 - The Cryptopals Crypto Challenges](https://cryptopals.com/sets/2/challenges/10)
![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311121516305.png)

### 实现 AES-CBC
CBC 模式一种分组密码模式，允许我们加密不规则的消息，但是同样只能加密完整的块。在 CBC 模式中，下一次块加密之前，每一个密文块加到下一个明文块上。第一个明文块需要与一个初始化向量 IV 相加。
key=”YELLOW SUBMARINE”
IV=全0
解密文件

>题目中 an IV of all ASCII 0 (\x00\x00\x00 &c) 中的 "&c" 是一种缩略写法，表示等等（et cetera）。

## 解析

### 知识点
- 1976 年，IBM 发明了密码分组链接（CBC，Cipher-block chaining）模式。在 CBC 模式中，每个明文块先与前一个密文块进行异或后，再进行加密。在这种方法中，每个密文块都依赖于它前面的所有明文块。同时，为了保证每条消息的唯一性，在第一个块中需要使用初始化向量。
	- CBC 加密过程
	![](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311132259167.png)
	- CBC 解密过程
	![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311132259223.png)
	AES CBC 加解密流程中，其中 block cipher encryption 指代 AES ECB 加密，block cipher decryption 指代 AES ECB 解密。


### 分析
> 1. AES CBC 加解密中，加密运算是 AES ECB 加密，解密运算是 AES ECB 解密。
> 2. 给定的文档中带有换行符，base 64 解码时记得处理。
> 3. 我们只要把 CBC 解密流程翻译成代码就可以了，具体解释在代码注释中。
{: .prompt-tip }



## 解决方案

### 代码

```python
import base64  
from Cryptodome.Cipher import AES  
  
  
# 判断有没有使用PKCS#7填充，原理是取与最后一个字节的值的相应长度的字节，看这些字节是否相同  
def is_PKCS7_padded(text: bytes):  
    # text[-x] 表示从字符串的末尾往前数第 x 个字符。  
    # text[-x:] 表示从上述位置开始，取到字符串的末尾（包括第 x 个字符），形成一个子串。  
    padding = text[-text[-1]:]  
    for byte in padding:  
        if not byte == text[-1]:  
            return False  
    return True  
  
# 删除填充的部分  
# 判断有没有填充，没有则返回原字节序列，否则返回删除掉最后长度为最后一个字节值的字节序列  
def PKCS7_trim(text: bytes):  
    if is_PKCS7_padded(text):  
        pad_length = text[-1]  
        # text[:-x] 表示从字符串的开头取子串，直到倒数第 x 个字符之前（不包括倒数第 x 个字符）。  
        return text[:-pad_length]  
    else:  
        return text  
  
  
# ECB解密  
def AES_ECB_decrypt(ciphertext: bytes, key: bytes):  
    cipher = AES.new(key, AES.MODE_ECB)  
    return PKCS7_trim(cipher.decrypt(ciphertext))  
  
  
# CBC解密  
def AES_CBC_decrypt(ciphertext: bytes, IV: bytes, key: bytes):  
    previous = IV  
    key_length = len(key)  
    plaintext = b''  
    # 将密文按照密钥长度分块  
    for i in range(0, len(ciphertext), key_length):  
        # 解密运算其实是ECB的解密  
        cipher = AES_ECB_decrypt(ciphertext[i:i + key_length], key)  
        # 前一个密文与后一个解密运算进行异或  
        xor_list = [chr(b1 ^ b2) for b1, b2 in zip(cipher, previous)]  
        plaintext += "".join(xor_list).encode()  
        previous = ciphertext[i:i + key_length]  
    return plaintext  
  
  
def main():  
    # 文件经过处理，已经将原文件中的换行符删除  
    with open("10.txt", "r") as file:  
        b64_data = file.read()  
    key = b"YELLOW SUBMARINE"  
    ciphertext_bytes = base64.b64decode(b64_data)  
    # 删除掉填充  
    text = PKCS7_trim(AES_CBC_decrypt(ciphertext_bytes, b'\x00' * AES.block_size, key))  
    print(text.decode("utf-8"))  
  
  
if __name__ == "__main__":  
    main()
```

### 运行结果

```
I'm back and I'm ringin' the bell 
A rockin' on the mike while the fly girls yell 
In ecstasy in the back of me 
Well that's my DJ Deshay cuttin' all them Z's 
Hittin' hard and the girlies goin' crazy 
Vanilla's on the mike, man I'm not lazy. 

I'm lettin' my drug kick in 
It controls my mouth and I begin 
To just let it flow, let my concepts go 
My posse's to the side yellin', Go Vanilla Go! 

Smooth 'cause that's the way I will be 
And if you don't give a damn, then 
Why you starin' at me 
So get off 'cause I control the stage 
There's no dissin' allowed 
I'm in my own phase 
The girlies sa y they love me and that is ok 
And I can dance better than any kid n' play 

Stage 2 -- Yea the one ya' wanna listen to 
It's off my head so let the beat play through 
So I can funk it up and make it sound good 
1-2-3 Yo -- Knock on some wood 
For good luck, I like my rhymes atrocious 
Supercalafragilisticexpialidocious 
I'm an effect and that you can bet 
I can take a fly girl and make her wet. 

I'm like Samson -- Samson to Delilah 
There's no denyin', You can try to hang 
But you'll keep tryin' to get my style 
Over and over, practice makes perfect 
But not if you're a loafer. 

You'll get nowhere, no place, no time, no girls 
Soon -- Oh my God, homebody, you probably eat 
Spaghetti with a spoon! Come on and say it! 

VIP. Vanilla Ice yep, yep, I'm comin' hard like a rhino 
Intoxicating so you stagger like a wino 
So punks stop trying and girl stop cryin' 
Vanilla Ice is sellin' and you people are buyin' 
'Cause why the freaks are jockin' like Crazy Glue 
Movin' and groovin' trying to sing along 
All through the ghetto groovin' this here song 
Now you're amazed by the VIP posse. 

Steppin' so hard like a German Nazi 
Startled by the bases hittin' ground 
There's no trippin' on mine, I'm just gettin' down 
Sparkamatic, I'm hangin' tight like a fanatic 
You trapped me once and I thought that 
You might have it 
So step down and lend me your ear 
'89 in my time! You, '90 is my year. 

You're weakenin' fast, YO! and I can tell it 
Your body's gettin' hot, so, so I can smell it 
So don't be mad and don't be sad 
'Cause the lyrics belong to ICE, You can call me Dad 
You're pitchin' a fit, so step back and endure 
Let the witch doctor, Ice, do the dance to cure 
So come up close and don't be square 
You wanna battle me -- Anytime, anywhere 

You thought that I was weak, Boy, you're dead wrong 
So come on, everybody and sing this song 

Say -- Play that funky music Say, go white boy, go white boy go 
play that funky music Go white boy, go white boy, go 
Lay down and boogie and play that funky music till you die. 

Play that funky music Come on, Come on, let me hear 
Play that funky music white boy you say it, say it 
Play that funky music A little louder now 
Play that funky music, white boy Come on, Come on, Come on 
Play that funky music 

```

## 参考

[带你了解CBC加密解密-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2045288)

[AES — PyCryptodome 3.19.0 documentation](https://pycryptodome.readthedocs.io/en/latest/src/cipher/aes.html)

[CryptoPals Crypto Challenges Using Rust: Implement CBC Mode - DEV Community](https://dev.to/nvn/cryptopals-crypto-challenges-using-rust-implement-cbc-mode-d2n)

[The Cryptopals Crypto Challenges](https://securitygossip.com/blog/2018/12/24/the-cryptopals-crypto-challenges-set-2/)

[Set 2: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/24/set-2/)