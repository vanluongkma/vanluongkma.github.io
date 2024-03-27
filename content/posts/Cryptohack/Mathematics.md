---
author: "vanluongkma"
title: "Mathematics"
date: "2023-05-04"
tags: [
"Cryptohack",
]
---

## Mathematics
### MODULAR MATH
### LATTICES
### BRAINTEASERS PART 1
### BRAINTEASERS PART 2
#### Roll your Own
```python3
from Crypto.Util.number import getPrime
import random
from utils import listener

FLAG = 'crypto{???????????????????????????????????}'

class Challenge():
    def __init__(self):
        self.no_prompt = True
        self.q = getPrime(512)
        self.x = random.randint(2, self.q)

        self.g = None
        self.n = None
        self.h = None

        self.current_step = "SHARE_PRIME"

    def check_params(self, data):
        self.g = int(data['g'], 16)
        self.n = int(data['n'], 16)
        if self.g < 2:
            return False
        elif self.n < 2:
            return False
        elif pow(self.g,self.q,self.n) != 1:
            return False
        return True

    def check_secret(self, data):
        x_user = int(data['x'], 16)
        if self.x == x_user:
            return True
        return False

    def challenge(self, your_input):
        if self.current_step == "SHARE_PRIME":
            self.before_send = "Prime generated: "
            self.before_input = "Send integers (g,n) such that pow(g,q,n) = 1: "
            self.current_step = "CHECK_PARAMS"
            return hex(self.q)

        if self.current_step == "CHECK_PARAMS":
            check_msg = self.check_params(your_input)
            if check_msg:
                self.x = random.randint(0, self.q)
                self.h = pow(self.g, self.x, self.n)
            else:
                self.exit = True
                return {"error": "Please ensure pow(g,q,n) = 1"}

            self.before_send = "Generated my public key: "
            self.before_input = "What is my private key: "
            self.current_step = "CHECK_SECRET"

            return hex(self.h)

        if self.current_step == "CHECK_SECRET":
            self.exit = True
            if self.check_secret(your_input):
                return {"flag": FLAG}
            else:
                return {"error": "Protocol broke somewhere"}

        else:
            self.exit = True
            return {"error": "Protocol broke somewhere"}


listener.start_server(port=13403)
```
- Challenge này được hiểu như sau:
	- Đầu tiên server sẽ trả về **hex(q)**
	- Tiếp đó cần gửi 2 giá trị **(g,n)** sao cho $g^q  \ mod \ n = 1$
	- Sau đó server sẽ trả lại cho user **hex(h)** với $h = g^x  \ mod \ n$
	- Và cuối cùng để get **FLAG** thì $x_{user} = x$
 - Bước đầu ta phải bypass qua

```python3
    def check_params(self, data):
        self.g = int(data['g'], 16)
        self.n = int(data['n'], 16)
        if self.g < 2:
            return False
        elif self.n < 2:
            return False
        elif pow(self.g,self.q,self.n) != 1:
            return False
        return True
```
 -  Chúng ta sử dụng DLP attack cụ thể ở đây là [**Paillier cryptosystem**](https://en.wikipedia.org/wiki/Paillier_cryptosystem)
 
 Paillier cryptosystem exploits the fact that certain discrete logarithms can be computed easily.

 For example, by binomial theorem, $$(1+n)^x = \sum_{k=0}^{x} \binom{x}{2} n^k = 1 + nx + \binom{x}{2} n^2 + higher \ powers \ of \ n$$ $$(1+n)^x \equiv 1 + nx \  \ mod \ \ n^2$$
 
 Nếu: $$y = (1 + n)^x  \ mod \ n^2$$
 Thì $$x \equiv \frac{y-1}{n} \ mod \ n$$	
  - Đầu tiên user cần chọn g, n sao cho $g^q = 1  \ \% \ n$ , nếu $g = q + 1$ và $$n = q^2$$
   $\implies$ $(1 + q)^q = 1 + q^2 = 1  \ mod \ n^2$
  - Example

```sage
sage: 6**5 % (5**2)
1
sage: 6**5 % (5**3)
26
sage: 6**5 % (5**1)
1
sage: 6**5 % (5**2)
1
sage: 6**5 % (5**4)
276
sage: 6**5 % (5**5)
1526
sage: 6**5 % (5**6)
7776
sage:
```
 - Như trên đã đề cập, $$h = g^x  \ mod \ n$$ $$\iff h = (1 + q)^x  \ mod \ n^2$$ $$ x = \frac{y - 1}{n}  \ mod\ n$$
 - Solution bằng python

```python3 
 from pwn import *
import json 

f = remote('socket.cryptohack.org', 13403)
q = f.recvline().decode().strip().split(": ")[1]
q = int(q[1:-1], 0)
f.recvuntil(b"1: ")

g = q + 1 
n = q ** 2

f.sendline(json.dumps({"g": hex(g), "n": hex(n)}))
p = f.recvline().decode().strip().split(": ")[1]
p = int(p[1:-1], 0)

x = (p - 1) // q 

f.sendline(json.dumps({"x": hex(x)}))
f.recvline()
f.close()
```
> FLAG : crypto{Grabbing_Flags_with_Pascal_Paillier}
### PRIMES