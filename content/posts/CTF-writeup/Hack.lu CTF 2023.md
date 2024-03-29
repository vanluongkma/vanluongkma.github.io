---
author: "vanluongkma"
title: "Hack.lu CTF 2023"
date: "2023-10-17"
tags: ["CTF-Writeup"]
---

## Lucky Numbers
![image](https://github-production-user-asset-6210df.s3.amazonaws.com/127461439/315314705-f024527b-f93d-4b22-bac1-b7298154a3fa.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240327%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240327T075026Z&X-Amz-Expires=300&X-Amz-Signature=2083d649925b8b004fefa158eb817ab402f2c2605d8625a0cfc1ef85190b20e1&X-Amz-SignedHeaders=host&actor_id=127461439&key_id=0&repo_id=635265812)

https://flu.xxx/challenges/15

```python3
#!/usr/bin/env python
#hacklu23 Baby Crypyo Challenge
import math
import random
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
import base64
import os                                                   
    
def add(e): return e+(length-len(e)%length)*chr(length-len(e)%length)
def remove(e): return e[0:-ord(e[-1:])]
length=16 

def main():  
    flag= os.environ["FLAG"]
    print("Starting Challenge")
 
    key=get_random_bytes(32)
    message=add(flag)
    iv = get_random_bytes(length)
    cipher = AES.new(key,AES.MODE_CBC,iv) 
    cipher_bytes = base64.b64encode(iv+cipher.encrypt(message.encode("utf8")))
    print(cipher_bytes.decode())

    for l in range(0,5):
        A=[]
        print("You know the moment when you have this special number that gives you luck? Great cause I forgot mine")
        data2=input()
        print("I also had a second lucky number, but for some reason I don't remember it either :(")
        data3=input()
        v=data2.strip()
        w=data3.strip()
        if not v.isnumeric() or not w.isnumeric():
            print("You sure both of these are numbers?")
            continue
        s=int(data2)
        t=int(data3)
        if s<random.randrange(10000,20000):
            print("I have the feeling the first number might be too small")
            continue
        if s>random.randrange(150000000000,200000000000):
            print("I have the feeling the first number might be too big")
            continue
        if t>42:
            print("I have the feeling the second number might be too big")
            continue

        n=2**t-1
        sent=False
        for i in range(2,int(n**0.5)+1):
             if (n%i) == 0:
                print("The second number didn't bring me any luck...")
                sent = True
                break
        if sent:
            continue
        u=t-1
        number=(2**u)*(2**(t)-1)
        sqrt_num=math.isqrt(s)
        for i in range(1,sqrt_num+1):
            if s%i==0:
                A.append(i)
                if i != s//i and s//i != s:
                    A.append(s//i)      
        total=sum(A)
        if total==s==number:
            decoded=base64.b64decode(cipher_bytes)
            cipher=AES.new(key,AES.MODE_CBC,iv)
            decoded_bytes=remove(cipher.decrypt(decoded[length:]))
            print("You found them, well done! Here have something for your efforts: ")
            print(decoded_bytes.decode())
            exit()
        else:
            print("Hm sadge, those don't seem to be my lucky numbers...😞")
    
    print("Math is such a cool concept, let's see if you can use it a little more...")
    exit()
  
if __name__ == "__main__":
    main()
```
### Solution


Tôi chú ý đến ``data2`` và ``data3``

```python3
        print("You know the moment when you have this special number that gives you luck? Great cause I forgot mine")
        data2=input()
        print("I also had a second lucky number, but for some reason I don't remember it either :(")
        data3=input()
```

Để qua được 3 điều kiện if thì

```
t <= 42
20000 <= s <= 150000000000
```

Tiếp đó dể ``sent = True`` thì ``isPrime(n=2**t-1) = True``

Và qua vòng for cuối thì ``number=(2**u)*(2**(t)-1)`` Là số hoàn hảo (u = t-1) nghĩa là t cũng phải là số nguyên tố

```python3
from pwn import *
from math import *


f = connect("flu.xxx", 10010)
def nt(n):
    if n < 2:
        return False
    for i in range(2, isqrt(n) + 1):
        if n % i == 0:
            return False
    return True

for i in range(42):
	if nt(2**i - 1) & nt(i):
		t = i
		s = (2**(t-1))*(2**(t)-1)
		if 20000 <= s <= 150000000000:
			break

f.sendlineafter(b'You know the moment when you have this special number that gives you luck? Great cause I forgot mine\n', str(s).encode())
f.sendlineafter(b"I also had a second lucky number, but for some reason I don't remember it either :(\n", str(t).encode())

f.recvline()
```

> FLAG : flag{luck_0n_fr1d4y_th3_13th?}
