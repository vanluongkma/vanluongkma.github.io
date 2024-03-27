---
author: "vanluongkma"
title: "Diffie-Hellman"
date: "2024-02-01"
tags: [
"Cryptohack",
]
---

## STARTER
### Diffie-Hellman Starter 1
![image](https://hackmd.io/_uploads/S1PnQNW5T.png)
 - ``Given the prime p = 991, and the element g = 209, find the inverse element d such that g * d mod 991 = 1.``
 - Ta có
```
p = 991
g = 209
g*d mod 991 = 1
g*d = 1 mod 991
d = g^-1 mod 991
```
![image](https://hackmd.io/_uploads/HybdEE-c6.png)
> FLAG : 569
### Diffie-Hellman Starter 2
![image](https://hackmd.io/_uploads/BkBL7Pb96.png)
 - Challenge yêu cầu tìm phần tử sinh $g$ nhỏ nhất trên trường $F(p)$ với p = 28151
 - Ta chỉ cần brute force g sao cho g^n mod p = g (1<g<p)
 - Solution bằng python
```python3
def find_x(g, p):
  for n in range(2, p):
    if pow(g, n, p) == g:
      return False
  return True

p = 28151
for g in range(p):
  if find_x(g, p):
    print(g)
    break
```
 - sage
```sage!
GF(28151).primitive_element()
```
> FLAG : 7
### Diffie-Hellman Starter 3
![image](https://hackmd.io/_uploads/HyQS27fqp.png)
 - Challenges này ta chỉ cần tính $g^a \ mod \ p$
```sage
sage: g = 2
sage: p = 2410312426921032588552076022197566074856950548502459942654116941958108831682612228890093858261341614673227141477904012196503648957050582631942730706805009223062734745341073406696246014589361659774041027169249453200378729434170325843778659198143763193776859869524088940195577346119843545301547043747207749969763750084308926339295559968882457872412993810129130294592999947926365264059284647209730384947211681434464714438488520940127459844288859336526896320919633919
sage: a = 972107443837033796245864316200458246846904598488981605856765890478853088246897345487328491037710219222038930943365848626194109830309179393018216763327572120124760140018038673999837643377590434413866611132403979547150659053897355593394492586978400044375465657296027592948349589216415363722668361328689588996541370097559090335137676411595949335857341797148926151694299575970292809805314431447043469447485957669949989090202320234337890323293401862304986599884732815
sage: pow(g, a, p)
1806857697840726523322586721820911358489420128129248078673933653533930681676181753849411715714173604352323556558783759252661061186320274214883104886050164368129191719707402291577330485499513522368289395359523901406138025022522412429238971591272160519144672389532393673832265070057319485399793101182682177465364396277424717543434017666343807276970864475830391776403957550678362368319776566025118492062196941451265638054400177248572271342548616103967411990437357924
sage:
```
> FLAG : 1806857697840726523322586721820911358489420128129248078673933653533930681676181753849411715714173604352323556558783759252661061186320274214883104886050164368129191719707402291577330485499513522368289395359523901406138025022522412429238971591272160519144672389532393673832265070057319485399793101182682177465364396277424717543434017666343807276970864475830391776403957550678362368319776566025118492062196941451265638054400177248572271342548616103967411990437357924
### Diffie-Hellman Starter 4
![image](https://hackmd.io/_uploads/SJ0uT7z96.png)
 - Như ở trên đã viết, challenges yêu cầu tính shared secret
 - $A = g^a \ mod \ p$
 - $B = g^b \ mod \ p$ 
 - $shared-secret = A^b \ mod \ p = B^a \ mod \ p = g ^ {a,b} \ mod \ p$
 - Solution bằng python:
```python3
g = 2
p = 2410312426921032588552076022197566074856950548502459942654116941958108831682612228890093858261341614673227141477904012196503648957050582631942730706805009223062734745341073406696246014589361659774041027169249453200378729434170325843778659198143763193776859869524088940195577346119843545301547043747207749969763750084308926339295559968882457872412993810129130294592999947926365264059284647209730384947211681434464714438488520940127459844288859336526896320919633919
A = 70249943217595468278554541264975482909289174351516133994495821400710625291840101960595720462672604202133493023241393916394629829526272643847352371534839862030410331485087487331809285533195024369287293217083414424096866925845838641840923193480821332056735592483730921055532222505605661664236182285229504265881752580410194731633895345823963910901731715743835775619780738974844840425579683385344491015955892106904647602049559477279345982530488299847663103078045601
b = 12019233252903990344598522535774963020395770409445296724034378433497976840167805970589960962221948290951873387728102115996831454482299243226839490999713763440412177965861508773420532266484619126710566414914227560103715336696193210379850575047730388378348266180934946139100479831339835896583443691529372703954589071507717917136906770122077739814262298488662138085608736103418601750861698417340264213867753834679359191427098195887112064503104510489610448294420720
B = 518386956790041579928056815914221837599234551655144585133414727838977145777213383018096662516814302583841858901021822273505120728451788412967971809038854090670743265187138208169355155411883063541881209288967735684152473260687799664130956969450297407027926009182761627800181901721840557870828019840218548188487260441829333603432714023447029942863076979487889569452186257333512355724725941390498966546682790608125613166744820307691068563387354936732643569654017172


shared_secret = pow(A, b, p)
print(shared_secret) 
```
> FLAG : 1174130740413820656533832746034841985877302086316388380165984436672307692443711310285014138545204369495478725102882673427892104539120952393788961051992901649694063179853598311473820341215879965343136351436410522850717408445802043003164658348006577408558693502220285700893404674592567626297571222027902631157072143330043118418467094237965591198440803970726604537807146703763571606861448354607502654664700390453794493176794678917352634029713320615865940720837909466
### Diffie-Hellman Starter 5
![image](https://hackmd.io/_uploads/B1k5aQMc6.png)
 - [source.py](https://cryptohack.org/static/challenges/source_0e330e41ce30ead878a4589929aa31a1.py)
 - [decrypt.py](https://cryptohack.org/static/challenges/decrypt_08c0fede9185868aba4a6ae21aca0148.py)
 - Khi đọc file source.py ta thấy $flag$ bị mã hóa với $AES-CBC$. key được lấy từ shared_secret và random iv.
 - Challenges cho ta luôn file decrypt.py và encrypt_flag, iv. Việc bây giờ ta chỉ cần tính shared_secret giống như bài 4 ở trên.
 - Dưới đây là solution python của mình:
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib


def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode('ascii')
    else:
        return plaintext.decode('ascii')

g = 2
p = 2410312426921032588552076022197566074856950548502459942654116941958108831682612228890093858261341614673227141477904012196503648957050582631942730706805009223062734745341073406696246014589361659774041027169249453200378729434170325843778659198143763193776859869524088940195577346119843545301547043747207749969763750084308926339295559968882457872412993810129130294592999947926365264059284647209730384947211681434464714438488520940127459844288859336526896320919633919
A= 112218739139542908880564359534373424013016249772931962692237907571990334483528877513809272625610512061159061737608547288558662879685086684299624481742865016924065000555267977830144740364467977206555914781236397216033805882207640219686011643468275165718132888489024688846101943642459655423609111976363316080620471928236879737944217503462265615774774318986375878440978819238346077908864116156831874695817477772477121232820827728424890845769152726027520772901423784
b = 197395083814907028991785772714920885908249341925650951555219049411298436217190605190824934787336279228785809783531814507661385111220639329358048196339626065676869119737979175531770768861808581110311903548567424039264485661330995221907803300824165469977099494284722831845653985392791480264712091293580274947132480402319812110462641143884577706335859190668240694680261160210609506891842793868297672619625924001403035676872189455767944077542198064499486164431451944
B= 1241972460522075344783337556660700537760331108332735677863862813666578639518899293226399921252049655031563612905395145236854443334774555982204857895716383215705498970395379526698761468932147200650513626028263449605755661189525521343142979265044068409405667549241125597387173006460145379759986272191990675988873894208956851773331039747840312455221354589910726982819203421992729738296452820365553759182547255998984882158393688119629609067647494762616719047466973581
shared_secret = pow(A,b,p)
iv = '737561146ff8194f45290f5766ed6aba'
ciphertext= '39c99bf2f0c14678d6a5416faef954b5893c316fc3c48622ba1fd6a9fe85f3dc72a29c394cf4bc8aff6a7b21cae8e12c'

print(decrypt_flag(shared_secret, iv, ciphertext))
```
> FLAG : crypto{sh4r1ng_s3cret5_w1th_fr13nd5}
## MAN IN THE MIDDLE
### Parameter Injection
![image](https://hackmd.io/_uploads/BJrjaXG9p.png)
 - Với bài này ta sẽ gửi cho Alice B = 1 thì shared_secret lúc đó sẽ là: $$B ^ a \ mod \ p \ $$  $$1^a \ mod \ p \ $$
 - Ta có thể nhật thấy vì B = 1 nên shared_secret = 1 $\forall a$
 - Khi đó ta chỉ lấy ra iv, encrypted_flag, shared_secret = 1 và giải mã theo AES-CBC là ra flag.
 - Solution bằng python
```python3
from Crypto.Util.Padding import unpad
from Crypto.Cipher import AES
import hashlib, json
from pwn import*

def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")

f = connect("socket.cryptohack.org", 13371)
f.recvuntil("Send to Bob:")
f.sendline(json.dumps({"p":"0x01", "g":"0x02", "A":"0x03"}).encode())
f.recvuntil("Intercepted from Bob: ")
f.sendline(json.dumps({"B":"0x01"}).encode())
f.recvuntil(b"Intercepted from Alice: ")
recv = json.loads(f.readline())
iv = recv["iv"]
ciphertext = recv["encrypted_flag"]
shared_secret = 1

print(decrypt_flag(shared_secret, iv, ciphertext))
```
> FLAG : crypto{n1c3_0n3_m4ll0ry!!!!!!!!}
### Export-grade
![image](https://hackmd.io/_uploads/BkE3aQfc6.png)
 - Với bài này khi ta chọn bất kì từ DH1536 -> DH64, server sẽ gửi lại cho ta $p, \ g, \ A, \ B, \ iv, \ encrypted-flag$
 - Ta cần tìm a từ $A = g^a \ mod \ p$, từ đó sẽ tính được secret.
 - Ở đây tôi sẽ sử dụng thuật toán [Pohlig Hellman](https://en.wikipedia.org/wiki/Pohlig%E2%80%93Hellman_algorithm) để tìm a với ``a = discrete_log(p, A, g)`` hoặc với tool [Discrete logarithm calculator](https://www.alpertron.com.ar/DILOG.HTM)
 - Tiếp theo tôi sẽ chọn ![image](https://github.com/luongdv35/Group_in_Cryptography/assets/127461439/b39417c7-3908-42cc-81ca-b7ee77b65876) $DH64$( difie-hellman dùng số nguyên tố 64 bit), đủ nhỏ giúp quá trình tìm a diễn ra nhanh chóng.
 - Solution bằng python
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Util.Padding import unpad, pad
from Crypto.Cipher import AES
import hashlib, json
from pwn import remote

def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")

f = remote("socket.cryptohack.org", 13379)
f.recvline()
f.sendline(json.dumps({"supported": ["DH64"]}))
f.recvline()
f.sendline(json.dumps({"chosen": "DH64"}))
f.recvuntil(b"Intercepted from Alice: ")
recv = json.loads(f.recvline())
p = int(recv["p"], 16)
g = int(recv["g"], 16)
A = int(recv["A"], 16)

f.recvuntil(b"Intercepted from Bob: ")
recv = json.loads(f.recvline())
B = int(recv["B"],16)

f.recvuntil(b"Intercepted from Alice: ")
recv = json.loads(f.recvline())

iv = recv["iv"]
encrypted_flag= recv["encrypted_flag"]
# A = g^a mod p
a = discrete_log(p, A, g) 
print(a)
shared_secret = pow(B,a,p)
print(decrypt_flag(shared_secret, iv, encrypted_flag))
```
> FLAG : crypto{d0wn6r4d35_4r3_d4n63r0u5}
### Static Client
![image](https://hackmd.io/_uploads/ByIp6QMcp.png)
 - Bài này khi ta $nc \ socket.cryptohack.org \ 13373$
 - Server sẽ trả về
```
Intercepted from Alice: {"p": "0xffffffffffffffffc90fdaa22168c234c4c6628b80dc1cd129024e088a67cc74020bbea63b139b22514a08798e3404ddef9519b3cd3a431b302b0a6df25f14374fe1356d6d51c245e485b576625e7ec6f44c42e9a637ed6b0bff5cb6f406b7edee386bfb5a899fa5ae9f24117c4b1fe649286651ece45b3dc2007cb8a163bf0598da48361c55d39a69163fa8fd24cf5f83655d23dca3ad961c62f356208552bb9ed529077096966d670c354e4abc9804f1746c08ca237327ffffffffffffffff", "g": "0x02", "A": "0xd64f2f1f0ad198dfee4e73fba511b207fe61cb3426702363dc40d8bdffc8c0a4bf8c92b00008a2c1106bac0d5811db956c89be28a3a242a6772af92a5b9873c63edb6cf4f723696c6453fdcf705dc028cd8c8e98b86e1e81507e4d6b3b221d861ae7bdbf0f3e8a6d87740d9e8a6f5c8910f001df27fd8036332325df122e2cc2cf2048edb3c8b3e2b2e7da5ecc3640d1b8658f4f515aa2d5b95301870f0e9a686754a48f0d1b57735a5a95ec2d913bcc1538c90751b81f35184df958beb0783c"}
Intercepted from Bob: {"B": "0x8d79b69390f639501d81bdce911ec9defb0e93d421c02958c8c8dd4e245e61ae861ef9d32aa85dfec628d4046c403199297d6e17f0c9555137b5e8555eb941e8dcfd2fe5e68eecffeb66c6b0de91eb8cf2fd0c0f3f47e0c89779276fa7138e138793020c6b8f834be20a16237900c108f23f872a5f693ca3f93c3fd5a853dfd69518eb4bab9ac2a004d3a11fb21307149e8f2e1d8e1d7c85d604aa0bee335eade60f191f74ee165cd4baa067b96385aa89cbc7722e7426522381fc94ebfa8ef0"}
Intercepted from Alice: {"iv": "9213a0d5c49f46179b7a92d44cb315af", "encrypted": "6ab0125ed934535a8a49df93ee0c40262fdc097e19b1998ed15e42ec4b1683d2"}
```
 - Nhìn vào dữ liệu server trả về mình đoán đây là kiểu tấn công MITM, Alice và Bob đag sử dụng trao đổi khóa Diffie-Hellman
 - Ta biết rằng nếu gửi $(p,g,A)$ cho Bob thì Bob sẽ trả về $B=g^b \ mod \ p$.
 - Vậy với đề bài như này mình sẽ chọn 2 số $g, A$ sao cho: $$g \ = \ A$$ $$B = g^a \ mod p$$ $$B = A^b \ mod \ p$$
 - Như vậy nếu ta gửi $g = A$ thì $Bob$ sẽ gửi lại cho ta secret.
 - Ta đã có IV, encrypted, shared_secret rồi giờ chỉ cần giải mã theo AES-CBC và lấy flag.
 - Solution python:
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from pwn import *
import hashlib, json


def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")
    
f = connect("socket.cryptohack.org", 13373)

f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
p = recv["p"]
g = recv["g"]
A = recv["A"]

f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
iv = recv["iv"]
encrypted = recv["encrypted"]

print(p, g, A)
f.sendlineafter("Bob connects to you, send him some parameters: ",json.dumps({"p": p, "g": A, "A": "0x01"}))
f.recvuntil("Bob says to you: ")
recv = json.loads(f.recvline())
shared_secret = int(recv["B"], 16)

print(decrypt_flag(shared_secret, iv, encrypted))
```
> FLAG : crypto{n07_3ph3m3r4l_3n0u6h}
## GROUP THEORY
### Additive
![image](https://hackmd.io/_uploads/H1dATmMq6.png)
 - Connect at $soket.cryptohack.org \ 13380$
 - Ở challnge này thay vì dùng DHKE trong nhóm nhân ,ALice và Bob triển khai trong nhóm cộng $$A = g * a \ mod \ p$$ $$B = g * b \ mod \ p$$ $$sh = g * a * b \ mod \ p$$
 - [Diffie-Hellman on additive group](https://crypto.stackexchange.com/questions/12398/diffie-hellman-on-additive-group)
 - Ta có thể tính $$a = A * g^{-1} \ mod \ p$$ $$secret = B * a \ mod \ p$$
 - Khi có ``iv, encrypted, shared_secret`` mình chỉ cần giải mã theo AES-CBC là lấy flag.
 - Solution bằng python của mình
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Util.Padding import unpad, pad
from Crypto.Util.number import *
from Crypto.Cipher import AES
import hashlib, json
from pwn import remote

def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))

def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")

f = remote("socket.cryptohack.org", 13380)

f.recvuntil(b"Intercepted from Alice:")
recv = json.loads(f.recvline())
p = int(recv["p"], 16)
g = int(recv["g"], 16)
A = int(recv["A"], 16)

f.recvuntil(b"Intercepted from Bob: ")
recv = json.loads(f.recvline())
B = int(recv["B"],16)

f.recvuntil(b"Intercepted from Alice: ")
recv = json.loads(f.recvline())
iv = recv["iv"]
encrypted= recv["encrypted"]
a = A * pow(g, -1 , p)

shared_secret = (B * a) % p
print(decrypt_flag(shared_secret, iv, encrypted))
```
> FLAG : crypto{cycl1c_6r0up_und3r_4dd1710n?}
### Static Client 2
 ![image](https://hackmd.io/_uploads/rJO1Amf9p.png)
 - Connect at $socket.cryptohack.org \ 13378$
 - Với bài này mình thử cách tấn công của bài $$
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from pwn import *
import hashlib, json


def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")
    
f = connect("socket.cryptohack.org", 13378)

f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
p = recv["p"]
g = recv["g"]
A = recv["A"]

f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
iv = recv["iv"]
encrypted = recv["encrypted"]

print(p, g, A)
f.sendlineafter("Bob connects to you, send him some parameters: ",json.dumps({"p": p, "g": A, "A": "0x01"}))
f.recvuntil("Bob says to you: ")
recv = json.loads(f.recvline())
shared_secret = int(recv["B"], 16)

print(decrypt_flag(shared_secret, iv, encrypted))
```
![image](https://hackmd.io/_uploads/SksFGTDq6.png)

 - Đến đây thì $Bob$ nói giá trị $g$ đáng nghi ngờ
 - Chúng ta sẽ giải quyết bài này như yêu cầu đề bài cho sửa p và g dù sao cũng đang được máy chủ xác minh.
 - Thay $p$ bằng một số $p'$  sao cho $p'-1$ là  [smooth number](https://en.wikipedia.org/wiki/Smooth_number) với điều kiện
     - $p' > p$
     - $p' - 1$ là smooth number
     - Việc thay $p =p'$ sẽ giúp cho hàm discrete_log() của thuật toán Pohlig_hellman nhanh hơn.
 - khi có được $p'$ => $g ^ b = B \ mod \ p'$ và dùng discrete_log() để tìm b.
 - Ta sẽ gửi
     - g = 2
     - A = A
     - p = p'
 - Solution bằng python
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from pwn import *
import hashlib, json


def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16)
    else:
        return plaintext
    
    
def smooth_p():
    Smooth_p = 1
    i = 2
    while Smooth_p < p or not isPrime(Smooth_p+1):
        Smooth_p *= i
        i += 1
    Smooth_p += 1
    return Smooth_p
f = connect("socket.cryptohack.org", 13378)

f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
p = int(recv["p"], 16)
g = int(recv["g"], 16)
A = int(recv["A"], 16)

P_ = smooth_p()
print(P_)
f.recvuntil("Intercepted from Alice: ")
recv = json.loads(f.recvline())
iv = recv["iv"]
encrypted = recv["encrypted"]

print(p, g, A)
f.sendlineafter("Bob connects to you, send him some parameters: ",json.dumps({"p": hex(P_), "g": hex(g), "A": hex(A)}))
f.recvuntil("Bob says to you: ")
recv = json.loads(f.recvline())
B = int(recv["B"], 16) 
b = discrete_log(P_, B, 2)
shared_secret = pow(A, b, p)

print(decrypt_flag(shared_secret, iv, encrypted))
```
> FLAG : crypto{uns4f3_pr1m3_sm4ll_oRd3r}
## MISC
### Script Kiddie
 - Source
     - [script.py](https://cryptohack.org/static/challenges/script_70449139467b5442a859d310e6945690.py)
     - [output.txt](https://cryptohack.org/static/challenges/output_92cc8b7f0db768b53291efbf969ca3ca.txt)
```python3
from Crypto.Cipher import AES
import hashlib
import secrets


def header():
    print("""  _____  _  __  __ _
 |  __ \(_)/ _|/ _(_)
 | |  | |_| |_| |_ _  ___
 | |  | | |  _|  _| |/ _ \
 | |__| | | | | | | |  __/
 |_____/|_|_| |_| |_|\___|
 | |  | |    | | |
 | |__| | ___| | |_ __ ___   __ _ _ __
 |  __  |/ _ \ | | '_ ` _ \ / _` | '_ \
 | |  | |  __/ | | | | | | | (_| | | | |
 |_|  |_|\___|_|_|_| |_| |_|\__,_|_| |_|

                                        """)


def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))


def pkcs7_unpad(message, block_size=16):
    if len(message) == 0:
        raise Exception("The input data must contain at least one byte")
    if not is_pkcs7_padded(message):
        return message
    padding_len = message[-1]
    return message[:-padding_len]


def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)
    return pkcs7_unpad(plaintext).decode('ascii')


def generate_public_int(g, a, p):
    return g ^ a % p


def generate_shared_secret(A, b, p):
    return A ^ b % p


def goodbye():
    print('Goodbye!')


def main():
    header()
    print('[-] Collecting data from Alice')
    p = int(input('> p: '))
    q = (p - 1) // 2
    g = int(input('> g: '))
    A = int(input('> A: '))
    print('[+] All data collected from Alice')

    print('[+] Generating public integer for alice')
    b = secrets.randbelow(q)
    B = generate_public_int(g, b, p)
    print(f'[+] Please send the public integer to Alice: {B}')
    print('')
    input('[+] Press any key to continue')
    print('')

    print('[+] Generating shared secret')
    shared_secret = generate_shared_secret(A, b, p)

    query = input('Would you like to decrypt a message? (y/n)\n')
    if query == 'y':
        iv = input('[-] Please enter iv (hex string)\n')
        ciphertext = input('[-] Please enter ciphertext (hex string)\n')
        flag = decrypt_flag(shared_secret, iv, ciphertext)
        print(f'[+] Flag recovered: {flag}')
        goodbye()
    else:
        goodbye()


if __name__ == '__main__':
    main()

# p: 2410312426921032588552076022197566074856950548502459942654116941958108831682612228890093858261341614673227141477904012196503648957050582631942730706805009223062734745341073406696246014589361659774041027169249453200378729434170325843778659198143763193776859869524088940195577346119843545301547043747207749969763750084308926339295559968882457872412993810129130294592999947926365264059284647209730384947211681434464714438488520940127459844288859336526896320919633919
# g: 2
# A: 539556019868756019035615487062583764545019803793635712947528463889304486869497162061335997527971977050049337464152478479265992127749780103259420400564906895897077512359628760656227084039215210033374611483959802841868892445902197049235745933150328311259162433075155095844532813412268773066318780724878693701177217733659861396010057464019948199892231790191103752209797118863201066964703008895947360077614198735382678809731252084194135812256359294228383696551949882
# B: 652888676809466256406904653886313023288609075262748718135045355786028783611182379919130347165201199876762400523413029908630805888567578414109983228590188758171259420566830374793540891937904402387134765200478072915215871011267065310188328883039327167068295517693269989835771255162641401501080811953709743259493453369152994501213224841052509818015422338794357540968552645357127943400146625902468838113443484208599332251406190345653880206706388377388164982846343351
# iv: 'c044059ae57b61821a9090fbdefc63c5'
# encrypted_flag: 'f60522a95bde87a9ff00dc2c3d99177019f625f3364188c1058183004506bf96541cf241dad1c0e92535564e537322d7'
```
 - Với bài này ta chỉ cần để ý 2 hàm 
 ![image](https://github.com/luongdv35/Group_in_Cryptography/assets/127461439/94be09da-5cca-4e74-8f63-650569a933fa)
 - Chú ý kia là phép xor nên bài này trở nên dễ dàng với phép tính $$a = A \ xor \ g \ mod \ p$$ $$secret = B \ xor \ a \ mod \ p$$.
 - Solution bằng python của mình:
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Util.Padding import unpad, pad
from Crypto.Util.number import *
from Crypto.Cipher import AES
import hashlib, json
from pwn import remote

def is_pkcs7_padded(message):
    padding = message[-message[-1]:]
    return all(padding[i] == len(padding) for i in range(0, len(padding)))

def decrypt_flag(shared_secret: int, iv: str, ciphertext: str):
    # Derive AES key from shared secret
    sha1 = hashlib.sha1()
    sha1.update(str(shared_secret).encode('ascii'))
    key = sha1.digest()[:16]
    # Decrypt flag
    ciphertext = bytes.fromhex(ciphertext)
    iv = bytes.fromhex(iv)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    if is_pkcs7_padded(plaintext):
        return unpad(plaintext, 16).decode("ascii")
    else:
        return plaintext.decode("ascii")

p = 2410312426921032588552076022197566074856950548502459942654116941958108831682612228890093858261341614673227141477904012196503648957050582631942730706805009223062734745341073406696246014589361659774041027169249453200378729434170325843778659198143763193776859869524088940195577346119843545301547043747207749969763750084308926339295559968882457872412993810129130294592999947926365264059284647209730384947211681434464714438488520940127459844288859336526896320919633919
g = 2
A = 539556019868756019035615487062583764545019803793635712947528463889304486869497162061335997527971977050049337464152478479265992127749780103259420400564906895897077512359628760656227084039215210033374611483959802841868892445902197049235745933150328311259162433075155095844532813412268773066318780724878693701177217733659861396010057464019948199892231790191103752209797118863201066964703008895947360077614198735382678809731252084194135812256359294228383696551949882
B = 652888676809466256406904653886313023288609075262748718135045355786028783611182379919130347165201199876762400523413029908630805888567578414109983228590188758171259420566830374793540891937904402387134765200478072915215871011267065310188328883039327167068295517693269989835771255162641401501080811953709743259493453369152994501213224841052509818015422338794357540968552645357127943400146625902468838113443484208599332251406190345653880206706388377388164982846343351
iv = 'c044059ae57b61821a9090fbdefc63c5'
encrypted_flag = 'f60522a95bde87a9ff00dc2c3d99177019f625f3364188c1058183004506bf96541cf241dad1c0e92535564e537322d7'
a = (A ^ g) % p
shared_secret = B ^ a % p
print(decrypt_flag(shared_secret, iv, encrypted_flag))
```
> FLAG : crypto{b3_c4r3ful_w1th_y0ur_n0tati0n}
