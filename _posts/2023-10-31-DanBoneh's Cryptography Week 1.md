---
title: DanBoneh's Cryptography Week 1
date: 2023-10-31 10:37:27 +0800
categories:
  - DanBoneh's Cryptography
tags:
  - cryptography
  - coursera
  - exp1
---

## 题目

**Many Time Pad**
  
Let us see what goes wrong when a stream cipher key is used more than once. Below are eleven hex-encoded ciphertexts that are the result of encrypting eleven plaintexts with a stream cipher, all with the same stream cipher key. Your goal is to decrypt the last ciphertext, and submit the secret message within it as solution.  
  
>**Hint:** XOR the ciphertexts together, and consider what happens when a space is XORed with a character in [a-zA-Z].
{: .prompt-tip}  
  
**ciphertext #1:**  

315c4eeaa8b5f8aaf9174145bf43e1784b8fa00dc71d885a804e5ee9fa40b16349c146fb778cdf2d3aff021dfff5b403b510d0d0455468aeb98622b137dae857553ccd8883a7bc37520e06e515d22c954eba5025b8cc57ee59418ce7dc6bc41556bdb36bbca3e8774301fbcaa3b83b220809560987815f65286764703de0f3d524400a19b159610b11ef3e


**ciphertext #2:**  

234c02ecbbfbafa3ed18510abd11fa724fcda2018a1a8342cf064bbde548b12b07df44ba7191d9606ef4081ffde5ad46a5069d9f7f543bedb9c861bf29c7e205132eda9382b0bc2c5c4b45f919cf3a9f1cb74151f6d551f4480c82b2cb24cc5b028aa76eb7b4ab24171ab3cdadb8356f

  
**ciphertext #3:**  

32510ba9a7b2bba9b8005d43a304b5714cc0bb0c8a34884dd91304b8ad40b62b07df44ba6e9d8a2368e51d04e0e7b207b70b9b8261112bacb6c866a232dfe257527dc29398f5f3251a0d47e503c66e935de81230b59b7afb5f41afa8d661cb

  
**ciphertext #4:**  

32510ba9aab2a8a4fd06414fb517b5605cc0aa0dc91a8908c2064ba8ad5ea06a029056f47a8ad3306ef5021eafe1ac01a81197847a5c68a1b78769a37bc8f4575432c198ccb4ef63590256e305cd3a9544ee4160ead45aef520489e7da7d835402bca670bda8eb775200b8dabbba246b130f040d8ec6447e2c767f3d30ed81ea2e4c1404e1315a1010e7229be6636aaa

  
**ciphertext #5:**  

3f561ba9adb4b6ebec54424ba317b564418fac0dd35f8c08d31a1fe9e24fe56808c213f17c81d9607cee021dafe1e001b21ade877a5e68bea88d61b93ac5ee0d562e8e9582f5ef375f0a4ae20ed86e935de81230b59b73fb4302cd95d770c65b40aaa065f2a5e33a5a0bb5dcaba43722130f042f8ec85b7c2070

  
**ciphertext #6:**  

32510bfbacfbb9befd54415da243e1695ecabd58c519cd4bd2061bbde24eb76a19d84aba34d8de287be84d07e7e9a30ee714979c7e1123a8bd9822a33ecaf512472e8e8f8db3f9635c1949e640c621854eba0d79eccf52ff111284b4cc61d11902aebc66f2b2e436434eacc0aba938220b084800c2ca4e693522643573b2c4ce35050b0cf774201f0fe52ac9f26d71b6cf61a711cc229f77ace7aa88a2f19983122b11be87a59c355d25f8e4

  
**ciphertext #7:**  

32510bfbacfbb9befd54415da243e1695ecabd58c519cd4bd90f1fa6ea5ba47b01c909ba7696cf606ef40c04afe1ac0aa8148dd066592ded9f8774b529c7ea125d298e8883f5e9305f4b44f915cb2bd05af51373fd9b4af511039fa2d96f83414aaaf261bda2e97b170fb5cce2a53e675c154c0d9681596934777e2275b381ce2e40582afe67650b13e72287ff2270abcf73bb028932836fbdecfecee0a3b894473c1bbeb6b4913a536ce4f9b13f1efff71ea313c8661dd9a4ce

  
**ciphertext #8:**  

315c4eeaa8b5f8bffd11155ea506b56041c6a00c8a08854dd21a4bbde54ce56801d943ba708b8a3574f40c00fff9e00fa1439fd0654327a3bfc860b92f89ee04132ecb9298f5fd2d5e4b45e40ecc3b9d59e9417df7c95bba410e9aa2ca24c5474da2f276baa3ac325918b2daada43d6712150441c2e04f6565517f317da9d3

  
**ciphertext #9:**  

271946f9bbb2aeadec111841a81abc300ecaa01bd8069d5cc91005e9fe4aad6e04d513e96d99de2569bc5e50eeeca709b50a8a987f4264edb6896fb537d0a716132ddc938fb0f836480e06ed0fcd6e9759f40462f9cf57f4564186a2c1778f1543efa270bda5e933421cbe88a4a52222190f471e9bd15f652b653b7071aec59a2705081ffe72651d08f822c9ed6d76e48b63ab15d0208573a7eef027

  
**ciphertext #10:**  

466d06ece998b7a2fb1d464fed2ced7641ddaa3cc31c9941cf110abbf409ed39598005b3399ccfafb61d0315fca0a314be138a9f32503bedac8067f03adbf3575c3b8edc9ba7f537530541ab0f9f3cd04ff50d66f1d559ba520e89a2cb2a83

  
**target ciphertext (decrypt this one):**  

32510ba9babebbbefd001547a810e67149caee11d945cd7fc81a05e9f85aac650e9052ba6a8cd8257bf14d13e6f0a803b54fde9e77472dbff89d71b57bddef121336cb85ccb8f3315f4b52e301d16e9f52f904


For completeness, here is the python script used to generate the ciphertexts.(it doesn't matter if you can't read this)

```python
import sys

MSGS = ( ---  11 secret messages  --- )

def strxor(a, b):     # xor two strings of different lengths
    if len(a) > len(b):
       return "".join([chr(ord(x) ^ ord(y)) for (x, y) in zip(a[:len(b)], b)])
    else:
       return "".join([chr(ord(x) ^ ord(y)) for (x, y) in zip(a, b[:len(a)])])

def random(size=16):
    return open("/dev/urandom").read(size)

def encrypt(key, msg):
    c = strxor(key, msg)
    print
    print c.encode('hex')
    return c

def main():
    key = random(1024)
    ciphertexts = [encrypt(key, msg) for msg in MSGS]
```
### 多次一密

给定 11 个 16 进制的密文，这些密文使用同一个流密码加密。要求解出最后一个的明文。

## 解析

### 知识点
- 流加密的主要思想就是用和明文 (pi)相同长度的密钥 (ki)进行逐位异或，得到对应的密文 (ci), 即：ci=pi⊕ki. 该方法的安全性主要取决于密钥 ki 的选择：使用与消息 (明文)一样长的随机的、无重复的密钥来加密消息，并且每个密钥只加密一次消息，随后便弃之不用。
- 根据提示，我们查阅 ASCII 编码表：
	![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311110143436.png)
	可以发现：
	>- `空格 ^ 小写字母 = 大写字母`，比如 `'a' ^ ' ' = 'A'`
	>- `空格 ^ 大写字母 = 空格 ^ 空格 ^ 小写字母 = 小写字母``
	>- `字母 ^ 字母 = 非字母`
	{: .prompt-info}
- 对于异或，x=x⊕y⊕y.
	假设密文 c、消息 m、密钥 k，有 c1=m1⊕k,c2=m2⊕k……。于是根据上述异或的性质，我们可以得到 c1⊕c2=m1⊕k⊕m2⊕k=m1⊕m2 ，此时，k 被消去了，得到了等式 ***c1⊕c2=m1⊕m2***。

### 分析

> 每两个 16 进制数才代表一个字节。
{: .prompt-tip }

> 随便找任意给定的 m_i，m_j 相与或，如果发现了有意义的英文字母，那么对应位上很可能一个是空格，另一个是英文字母。
> 举例来说，有 m_1, m_2 两条信息：
  ![image.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311111118900.png)
> 我们将其用一个随机的 key 来加密，加密结果为 c_1 和 c_2，我们知道 c_1⊕c_2=m_1⊕m_2。
> ![202311111120038.png](https://note-for-zephyrryan.oss-cn-beijing.aliyuncs.com/obsidian_picture/202311111120038.png)
> **怎么确定谁是空格，谁是字母呢？**
> 我们不妨假设 m_1 是空格，标记次数记为 1，将其再与其他 9 条密文异或，累计是空格的标记次数，满足一定次数，我们就判断其为空格。
{: .prompt-info }





## 解决方案

### 代码
#### 代码逻辑：

>**挑选出每条密文中可能为空格的位置，与空格异或，得到该位置的密钥，将每条密文得出的密钥按照位置组合在一起（会有后得到的密钥替换原先的过程，但这不影响），进而求解密文。**
{: .prompt-tip }

>1. 遍历 11 条密文，分别与其他密文异或，若异或结果为字母，则标记该密文此处可能为空格；
>2. 创建一个与该密文长度相同的列表，初始化为 0，可能为空格时，列表对应位置计数+1；
>3. 当可能为空格次数大于一定值，我们判断这个位置就是空格，将其与空格异或，就得到了该位置的密钥；
>4. 遍历完 11 条密文，就获得了整个大致的密钥；
>5. 用密钥解密，得出大致的明文；
>6. 修正明文（明文具有一定的可读性，我们可以猜测），再次与密文异或，得出正确的密钥；
>7. 进而得到全部的明文
```python
ciphertexts = [  
    "315c4eeaa8b5f8aaf9174145bf43e1784b8fa00dc71d885a804e5ee9fa40b16349c146fb778cdf2d3aff021dfff5b403b510d0d0455468aeb98622b137dae857553ccd8883a7bc37520e06e515d22c954eba5025b8cc57ee59418ce7dc6bc41556bdb36bbca3e8774301fbcaa3b83b220809560987815f65286764703de0f3d524400a19b159610b11ef3e",  
    "234c02ecbbfbafa3ed18510abd11fa724fcda2018a1a8342cf064bbde548b12b07df44ba7191d9606ef4081ffde5ad46a5069d9f7f543bedb9c861bf29c7e205132eda9382b0bc2c5c4b45f919cf3a9f1cb74151f6d551f4480c82b2cb24cc5b028aa76eb7b4ab24171ab3cdadb8356f",  
    "32510ba9a7b2bba9b8005d43a304b5714cc0bb0c8a34884dd91304b8ad40b62b07df44ba6e9d8a2368e51d04e0e7b207b70b9b8261112bacb6c866a232dfe257527dc29398f5f3251a0d47e503c66e935de81230b59b7afb5f41afa8d661cb",  
    "32510ba9aab2a8a4fd06414fb517b5605cc0aa0dc91a8908c2064ba8ad5ea06a029056f47a8ad3306ef5021eafe1ac01a81197847a5c68a1b78769a37bc8f4575432c198ccb4ef63590256e305cd3a9544ee4160ead45aef520489e7da7d835402bca670bda8eb775200b8dabbba246b130f040d8ec6447e2c767f3d30ed81ea2e4c1404e1315a1010e7229be6636aaa",  
    "3f561ba9adb4b6ebec54424ba317b564418fac0dd35f8c08d31a1fe9e24fe56808c213f17c81d9607cee021dafe1e001b21ade877a5e68bea88d61b93ac5ee0d562e8e9582f5ef375f0a4ae20ed86e935de81230b59b73fb4302cd95d770c65b40aaa065f2a5e33a5a0bb5dcaba43722130f042f8ec85b7c2070",  
    "32510bfbacfbb9befd54415da243e1695ecabd58c519cd4bd2061bbde24eb76a19d84aba34d8de287be84d07e7e9a30ee714979c7e1123a8bd9822a33ecaf512472e8e8f8db3f9635c1949e640c621854eba0d79eccf52ff111284b4cc61d11902aebc66f2b2e436434eacc0aba938220b084800c2ca4e693522643573b2c4ce35050b0cf774201f0fe52ac9f26d71b6cf61a711cc229f77ace7aa88a2f19983122b11be87a59c355d25f8e4",  
    "32510bfbacfbb9befd54415da243e1695ecabd58c519cd4bd90f1fa6ea5ba47b01c909ba7696cf606ef40c04afe1ac0aa8148dd066592ded9f8774b529c7ea125d298e8883f5e9305f4b44f915cb2bd05af51373fd9b4af511039fa2d96f83414aaaf261bda2e97b170fb5cce2a53e675c154c0d9681596934777e2275b381ce2e40582afe67650b13e72287ff2270abcf73bb028932836fbdecfecee0a3b894473c1bbeb6b4913a536ce4f9b13f1efff71ea313c8661dd9a4ce",  
    "315c4eeaa8b5f8bffd11155ea506b56041c6a00c8a08854dd21a4bbde54ce56801d943ba708b8a3574f40c00fff9e00fa1439fd0654327a3bfc860b92f89ee04132ecb9298f5fd2d5e4b45e40ecc3b9d59e9417df7c95bba410e9aa2ca24c5474da2f276baa3ac325918b2daada43d6712150441c2e04f6565517f317da9d3",  
    "271946f9bbb2aeadec111841a81abc300ecaa01bd8069d5cc91005e9fe4aad6e04d513e96d99de2569bc5e50eeeca709b50a8a987f4264edb6896fb537d0a716132ddc938fb0f836480e06ed0fcd6e9759f40462f9cf57f4564186a2c1778f1543efa270bda5e933421cbe88a4a52222190f471e9bd15f652b653b7071aec59a2705081ffe72651d08f822c9ed6d76e48b63ab15d0208573a7eef027",  
    "466d06ece998b7a2fb1d464fed2ced7641ddaa3cc31c9941cf110abbf409ed39598005b3399ccfafb61d0315fca0a314be138a9f32503bedac8067f03adbf3575c3b8edc9ba7f537530541ab0f9f3cd04ff50d66f1d559ba520e89a2cb2a83",  
    "32510ba9babebbbefd001547a810e67149caee11d945cd7fc81a05e9f85aac650e9052ba6a8cd8257bf14d13e6f0a803b54fde9e77472dbff89d71b57bddef121336cb85ccb8f3315f4b52e301d16e9f52f904",  
]  
  
  
# 字节序列进行异或  
def xor_bytes(seq1: bytes, seq2: bytes) -> bytes:  
    # zip(seq1, seq2)将各个比特对应，然后for循环进行每个比特的异或  
    return bytes(b1 ^ b2 for b1, b2 in zip(seq1, seq2))  
  
  
key = [0] * 200  
# 我们后续需要调整这个空格数的阈值，得到最接近的答案。  
# 当阈值过高，则会导致本来确实为空格的字节被忽略，导致密钥不完整  
# 当阈值过低，则会导致本来不为空格的字节误认为是空格，导致密钥错误  
# 所有寻找一个能够正确识别最大多数空格的阈值  
space_threshold = 6  
ciphertexts_length = len(ciphertexts)  
"""
总体思路：  
我们要解出明文，那么我们就需要知道密钥；  
我们知道，a=b^c,则b=a^c，又ciphertext=key^plaintext 因而，当知道明文和密文,我们只需要将其异或，就可以得到密钥key；  
根据提示，空格与字母的异或结果仍为字母；由此，当两个字符互相异或时，若结果为字母，那么这两个字符很有可能一个是空格，一个是字母；  
我们不妨让一个密文与其他密文异或，若异或结果某个位置是字母，那么我们就在这个密文相应位置计数+1，  
与其他10条密文异或之后，我们就可以得到这个位置可能是空格的次数，这就是上面阈值的设定；  
多次一密，每个明文的对应位置的字节，密钥的字节是相同的，因而只需要选出明文中本是空格的密文，按原来的位置组合，将其与空格异或，就可以得到大致的密钥；  
当遍历完十条密文之后，我们就可以得到许多明文是空格的密文的字节，把他们组合起来，与空格异或即可；  
有了密钥只需要与密文异或，就得到了明文。  
当然，密钥是猜测的，会有瑕疵，因而解出的明文会有少量错误，我们可以人工修正明文，再次与密文异或，便得到了正确的密钥。  
由此，所有的密文我们就都可以破解了。  
""" 

for i in range(ciphertexts_length):  
    ciphertext1 = bytes.fromhex(ciphertexts[i])  
    ciphertext1_length = len(ciphertext1)  
    candidate_space = [0] * ciphertext1_length  
  
    for j in range(ciphertexts_length):  
        if j == i:  
            continue  
        ciphertext2 = bytes.fromhex(ciphertexts[j])  
        xor_text = xor_bytes(ciphertext1, ciphertext2)  
        '''  
        不能用index获取位置，因为index获取的是第一个的位置！！！  
        for letter in xor_text:            if letter.isalpha():                print(xor_text.index(letter))  
                candidate_space[xor_text.index(letter)] += 1        '''        len_xor_text = len(xor_text)  
        for k in range(len_xor_text):  
            if xor_text[k: k + 1].isalpha() or xor_text[k: k + 1] == chr(0):  
                candidate_space[k] += 1  
  
    for s in range(ciphertext1_length):  
        if candidate_space[s] >= space_threshold:  
            key[s] = ciphertext1[s] ^ ord(' ')  
  
print("根据猜测的密钥求解的明文：", xor_bytes(key, bytes.fromhex(ciphertexts[10])).decode())  
# 输出  
"""
Thm secuet message is: Whtn usi|g wsstream cipher, never use the key more than once  
由于是猜测的，密钥可能并不完全正确，我们可以自主修正一下：  
The secret message is: When using a stream cipher, never use the key more than once  
"""

# 我们根据修正了的第十一条明文，与第十一条密文再次异或，便得到了正确的密钥，由此，我们可以列出其他密文的明文。  
plaintext = "The secret message is: When using a stream cipher, never use the key more than once"  
correct_key = xor_bytes(plaintext.encode(), bytes.fromhex(ciphertexts[10]))  
print("根据修正后的明文得到的正确密钥：", correct_key.hex())  
# 这是16进制的正确的密钥：  
# 66396e89c9dbd8cc9874352acd6395102eafce78aa7fed28a07f6bc98d29c50b69b0339a19f8aa401a9c6d708f80c066c763fef0123148cdd8e802d05ba98777335daefcecd59c433a6b268b60bf4ef03c9a61  
for i in range(len(ciphertexts)):  
    print(f"明文 {i + 1}:", xor_bytes(correct_key, bytes.fromhex(ciphertexts[i])).decode())
```

### 运行结果

```
根据猜测的密钥求解的明文： Thm secuet message is: Whtn usi|g wsstream cipher, never use the key more than once
根据修正后的明文得到的正确密钥： 66396e89c9dbd8cc9874352acd6395102eafce78aa7fed28a07f6bc98d29c50b69b0339a19f8aa401a9c6d708f80c066c763fef0123148cdd8e802d05ba98777335daefcecd59c433a6b268b60bf4ef03c9a61
明文 1: We can factor the number 15 with quantum computers. We can also factor the number 1
明文 2: Euler would probably enjoy that now his theorem becomes a corner stone of crypto - 
明文 3: The nice thing about Keeyloq is now we cryptographers can drive a lot of fancy cars
明文 4: The ciphertext produced by a weak encryption algorithm looks as good as ciphertext 
明文 5: You don't want to buy a set of car keys from a guy who specializes in stealing cars
明文 6: There are two types of cryptography - that which will keep secrets safe from your l
明文 7: There are two types of cyptography: one that allows the Government to use brute for
明文 8: We can see the point where the chip is unhappy if a wrong bit is sent and consumes 
明文 9: A (private-key)  encryption scheme states 3 algorithms, namely a procedure for gene
明文 10:  The Concise OxfordDictionary (2006) deﬁnes crypto as the art of  writing o r sol
明文 11: The secret message is: When using a stream cipher, never use the key more than once

```

## 参考

[Stanford - Cryptography I - Week 1-1 Programming Assignment - CodeAntenna](https://codeantenna.com/a/WPQyTmRqtM)

[Stanford's Introduction to Cryptography. Week 1 - Programming Assignment · GitHub](https://gist.github.com/vadimii/4061298)

[实验一 Many Time Pad - PeteLau - 博客园](https://www.cnblogs.com/jingqinglin/p/12843068.html)

[密码学实验一：Many Time Pad（多次使用流密码）-CSDN博客](https://blog.csdn.net/qq_28786023/article/details/109016103)

[现代密码学解题报告(一) | B1ank](https://blank-vax.github.io/2019/11/13/%E7%8E%B0%E4%BB%A3%E5%AF%86%E7%A0%81%E5%AD%A6%E8%A7%A3%E9%A2%98%E6%8A%A5%E5%91%8A%EF%BC%88%E4%B8%80%EF%BC%89/)

