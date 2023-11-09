---
title: Cryptopals Challenge 6
date: 2023-11-03 20:04:56 +0800
categories:
  - Cryptopals
  - Set1
tags:
  - cryptography
  - cryptopals
---

## 题目

![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311082005159.png)

### 攻击重复密钥 XOR 加密
解密一个经过重复密钥 XOR 加密后又经过 base64 编码的文件。
[点此处查看文件](https://cryptopals.com/static/challenge-data/6.txt)
## 解析

### 知识点
- 汉明距离: 在信息论"信息论"中，两个等长字符串之间的**汉明距离**是两个字符串对应位置的不同字符的个数。换句话说，它就是将一个字符串变换成另外一个字符串所需替换字符个数。在计算机中，则是一个 01 序列变成另一个 01 序列所需要替换的 01 个数。
- 如果 `a = b ^ c` 那么 `b = a ^ c`。

### 分析

> 当我们把密文按照密钥长度去划分为等长的块，那么，由于明文都是字母、空格、标点等，都在 ASCII 编码中，不同块之间的汉明距离会较小，因此，我们可以使用暴力破解的方法，遍历密钥长度，不同块之间的**平均汉明距离最小**的最有可能是密钥长度。在这里，**将汉明距离归一化是必要的，因为这样才能在不同长度密钥下比较。**
{: .prompt-tip }

> 题目其实给出了完整的解体流程，我们的**着手点是密钥长度**。思路如下：
> 1. 暴力破解，根据汉明距离猜测最可能的密钥长度；
> 2. 根据密钥长度划分为块，由于不同块的同一位置的字节与密钥中相应位置的字节进行单字节 XOR 加密，因而可以将不同块的同一位置的字节组成一个序列，采用 `challenge3` 中的解密方法，解密出密钥中的单个字节，再将所有字节连接起来，就是密钥。
> 3. 有了密钥，采用 challenge5 中的加密方法解密，即可得到明文。
{: .prompt-info }


## 解决方案

### 代码

```python
from base64 import b64decode  
import Letter_frequencies as lf  
from Challenge5_repeatingXor import repeating_xor  
  
with open('6.txt', 'r') as file:  
    data = file.read()  
  
decoded_data = b64decode(data)  
  
  
# 汉明距离：在信息论中，两个等长字符串之间的汉明距离是两个字符串对应位置的不同字符的个数。  
# 换句话说，它就是将一个字符串变换成另外一个字符串所需要替换的字符个数。  
# 在计算机里，两个字节序列的汉明距离则只需考虑0和1  
# 因此，我们只需要将两个字节序列逐个字节异或，并统计全部异或结果中”1“的个数即可  
# ”1“的个数就是汉明距离  
def hamming_distance(x: bytes, y: bytes):  
    assert len(x) == len(y)  
    distance = 0  
    for byte1, byte2 in zip(x, y):  
        xor_result = byte1 ^ byte2  
        distance += bin(xor_result).count('1')  
    return distance  
  
  
# 着手点是密钥长度。  
# 当我们把密文按照密钥长度去划分为等长的，那么，由于明文都是字母、空格、标点等，都在ASCII编码中，他们的汉明距离会较小；  
# 因此，我们可以使用暴力破解的方法，遍历密钥长度，汉明距离最小的最有可能是密钥长度  
# 在这里，将汉明距离归一化是必要的，因为这样才能在不同长度密钥下比较  
def get_keySize(ciphertext: bytes):  
    keySize = None  
    min_distance = None  
    for keyLength in range(2, 41):  
        edit_distance = 0  
        # 在这里，每个密钥长度，我们取了4组求平均的汉明距离。  
        chunks = [ciphertext[i * keyLength:(i + 1) * keyLength] for i in range(4)]  
        for i in range(0, len(chunks)):  
            for j in range(0, len(chunks)):  
                edit_distance += hamming_distance(chunks[i], chunks[j])  
        # 归一化处理  
        normalized_distance = edit_distance / keyLength  
  
        if min_distance is None or normalized_distance < min_distance:  
            min_distance = normalized_distance  
            keySize = keyLength  
    return keySize  
  
  
# 根据密钥大小分块，便于后续得出密钥  
keySize = get_keySize(decoded_data)  
cipherChunks = [decoded_data[i:i + keySize] for i in range(0, len(decoded_data), keySize)]  
# 为了保持每个块长度一致，我们可以去掉最后一块，这没有影响  
cipherChunks.pop()  
num_of_chunks = len(cipherChunks)  
# 解出密钥  
key = ""  
for i in range(keySize):  
    ciphertext = ""  
    for j in range(num_of_chunks):  
        # cipherChunks实际上是块长为keySize的数组，其中的元素是字节序列，  
        # 当我们用cipherChunks[j][i]去访问时，涉及到一个强制类型转换，，一个字节变成了int类型的ASCII值  
        ciphertext += chr(cipherChunks[j][i])  
    ciphertext = ciphertext.encode()  
    plaintext, key_letter = lf.decipher(ciphertext)  
    key += chr(key_letter)  
byte_key = key.encode()  
  
# 解密  
plaintext = repeating_xor(decoded_data, byte_key)  
print(plaintext.decode())
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

[Hamming distance - Wikipedia](https://en.wikipedia.org/wiki/Hamming_distance)

[Set 1: Pwn the world. — A noob's attempt at blogging.](https://hexterisk.github.io/blog/posts/2020/04/20/set-1/)

[Part 3: Breaking Repeating Key XOR Programmatically](https://carterbancroft.com/breaking-repeating-key-xor-programmatically/)
