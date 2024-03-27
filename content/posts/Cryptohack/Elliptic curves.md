---
author: "vanluongkma"
title: "Elliptic curves"
date: "2024-02-23"
tags: [
"Cryptohack",
]
---

## ELLIPTIC CURVES CRYPTOHACK
### BACKGROUND
#### Background Reading
 - The flag is the name we give groups with a commutative operation.
> FLAG : crypto{abelian}
### STARTER
#### Point Negation
 - E: $Y^2 = X^3 + 497 X + 1768, p: 9739$
 - Chall yêu cầu tính tọa độ $Q(x, y)$ với $P + Q = 0$ và $P(8045, 6936)$
 - $Q = -P$
```sage!
sage: E = EllipticCurve(GF(9739),[497,1768])
sage: P = E(8045,6936)
sage: -P
(8045 : 2803 : 1)
```
> FLAG : crypto{8045,2083}
#### Point Addition
 - Bài này yêu cầu tìm $S(x,y) = P + P + Q + R$
 - Áp dụng mã giả để tính phép cộng các điểm trên đường cong lại ta có:
```sage!
Algorithm for the addition of two points: P + Q

(a) If P = O, then P + Q = Q.
(b) Otherwise, if Q = O, then P + Q = P.
(c) Otherwise, write P = (x1, y1) and Q = (x2, y2).
(d) If x1 = x2 and y1 = −y2, then P + Q = O.
(e) Otherwise:
  (e1) if P ≠ Q: λ = (y2 - y1) / (x2 - x1)
  (e2) if P = Q: λ = (3x12 + a) / 2y1
(f) x3 = λ2 − x1 − x2,     y3 = λ(x1 −x3) − y1
(g) P + Q = (x3, y3)

```
 - Solution bằng python
```python3
from Crypto.Util.number import*

def point_addition(p1,p2):
    if p1 == (0,0):
        return p2
    if p2 == (0,0):
        return p1
    x1, y1 = p1
    x2, y2 = p2
    if x1 == x2 and y1 == -y2:
        return (0,0)
    lamda = 0
    if p1 == p2:
        lamda = ((3*pow(x1,2,p)+a) * inverse(2*y1, p))
    else:
        lamda = ((y2-y1)%p *pow(x2-x1,-1,p))

    x3 = (pow(lamda, 2) - x1 - x2) % p
    y3 = (lamda*(x1 - x3) - y1) % p 
    return (x3,y3)

a = 497
b = 1768
p = 9739
P = (493, 5564)
Q = (1539, 4742)
R = (4403,5202)
#S(x,y) = P + P + Q + R
print(point_addition(P,point_addition(P,point_addition(Q,R))))
```
 - Solution bằng sage
```sage!
sage: E = EllipticCurve(GF(9739),[497,1768])
sage: P = E(493, 5564)
sage: Q = E(1539, 4742)
sage: R = E(4403,5202)
sage: P + P + Q + R
(4215 : 2162 : 1)
```
>  FLAG : crypto{4215,2162}
#### Scalar Multiplication
 - Challenge này yêu cầu ta tính $Q(x, y) = 7863P$ 
 - Áp dụng mã giả để tính phép nhân trên đường cong ta có 
```sage!
Double and Add algorithm for the scalar multiplication of point P by n

Input: P in E(Fp) and an integer n > 0
1. Set Q = P and R = O.
2. Loop while n > 0.
  3. If n ≡ 1 mod 2, set R = R + Q.
  4. Set Q = 2 Q and n = ⌊n/2⌋.
  5. If n > 0, continue with loop at Step 2.
6. Return the point R, which equals nP.
```
 - Solution bằng python
```python3
from Crypto.Util.number import*

def point_addition(p1,p2):
    if p1 == (0,0):
        return p2
    if p2 == (0,0):
        return p1
    x1, y1 = p1
    x2, y2 = p2
    if x1 == x2 and y1 == -y2:
        return (0,0)
    lamda = 0
    if p1 == p2:
        lamda = ((3*pow(x1,2,p)+a) * inverse(2*y1, p))
    else:
        lamda = ((y2-y1)%p *pow(x2-x1,-1,p))

    x3 = (pow(lamda, 2) - x1 - x2) % p
    y3 = (lamda*(x1 - x3) - y1) % p 
    return (x3,y3)

a = 497
b = 1768
p = 9739
P = (2339, 2213)
n = 7863
Q = P
R = (0, 0)
while n > 0:
  if n % 2 == 1:
      R = point_addition(R, Q)
  Q = point_addition(Q, Q)
  n = n//2
print(R)
```
 - Solution bằng sage
```sage!
sage: E = EllipticCurve(GF(9739),[497,1768])
sage: P = E(2339, 2213)
sage: 7863*P
(9467 : 2742 : 1)
```
> FLAG: crypto{9467,2742} 
#### Curves and Logs
 - Challenge yêu cầu ta tìm tọa độ x của $S(x, y)$ sau khi băm SHA1 với:
     - $Q_A = n_AG$
     - $Q_B = n_BG$
     - shared secret : $S = n_A*Q_B = n_B * Q_A$
 - Solution bằng python
```python3
from Crypto.Util.number import *
import hashlib

def point_addition(p1,p2):
    if p1 == (0,0):
        return p2
    if p2 == (0,0):
        return p1
    x1, y1 = p1
    x2, y2 = p2
    if x1 == x2 and y1 == -y2:
        return (0,0)
    lamda = 0
    if p1 == p2:
        lamda = ((3*pow(x1,2,p)+a) * inverse(2*y1, p))
    else:
        lamda = ((y2-y1)%p *pow(x2-x1,-1,p))

    x3 = (pow(lamda, 2) - x1 - x2) % p
    y3 = (lamda*(x1 - x3) - y1) % p 
    return (x3,y3)

def scalar_mult(P, n):
    Q = P
    R = (0, 0)
    while n > 0:
        if n % 2 == 1:
            R = point_addition(R, Q)
        Q = point_addition(Q,Q)
        n = n // 2
    return R

if __name__ == '__main__':
    a = 497
    b = 1768
    p = 9739
    G = (1804,5368)
    n = 7863
    QA = (815, 3190)
    nB = 1829
    S = scalar_mult(QA, nB)
    print(S)
    flag = hashlib.sha1(str(S[0]).encode()).hexdigest()
    print("crypto{" + flag + "}")
```
 - Solution bằng sage
```sage
sage: import hashlib
sage: E = EllipticCurve(GF(9739),[497,1768])
sage: QA = E(815,3190)
sage: nB = 1829
sage: S = QA*nB
sage: print("crypto{" + hashlib.sha1(str(S[0]).encode()).hexdigest()+ "}")
crypto{80e5212754a824d3a4aed185ace4f9cac0f908bf}
```
> FLAG : crypto{80e5212754a824d3a4aed185ace4f9cac0f908bf}
#### Efficient Exchange
 - Challenge này cho ta q_x = 4726 và $n_B = 6534$ và dùng file [decrypt.py](https://cryptohack.org/static/challenges/decrypt_08c0fede9185868aba4a6ae21aca0148.py) để decrypt ra flag.
 - Bây giờ chúng ta thay x vào phương trình $Y^2 = X^3 + 497 X + 1768$ để tìm lại $Y^2$
 - Khi có $Y^2$ ta có thể tìm $Y$ dựa vào điều kiện đã biết $p ≡ 3 mod 4$ và [``link``](https://crypto.stackexchange.com/questions/20993/significance-of-3mod4-in-squares-and-square-roots-mod-n)
 - Việc còn lại là tính shared secret từ $Q_A*n_B$ giống bài trước và decrypt theo file đã cho.
 - Solution bằng python
```python3
from Crypto.Util.number import *
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib

def point_addition(p1,p2):
    if p1 == (0,0):
        return p2
    if p2 == (0,0):
        return p1
    x1, y1 = p1
    x2, y2 = p2
    if x1 == x2 and y1 == -y2:
        return (0,0)
    lamda = 0
    if p1 == p2:
        lamda = ((3*pow(x1,2,p)+a) * inverse(2*y1, p))
    else:
        lamda = ((y2-y1)%p *pow(x2-x1,-1,p))

    x3 = (pow(lamda, 2) - x1 - x2) % p
    y3 = (lamda*(x1 - x3) - y1) % p 
    return (x3,y3)

def scalar_mult(P, n):
    Q = P
    R = (0, 0)
    while n > 0:
        if n % 2 == 1:
            R = point_addition(R, Q)
        Q = point_addition(Q,Q)
        n = n // 2
    return R

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

if __name__ == '__main__':
    a = 497
    b = 1768
    p = 9739
    G = (1804,5368)
    q_x = 4726
    n_B = 6534
    q_y2 = q_x**3 + a*q_x + b
    q_y = pow(q_y2, (p + 1) // 4, p)
    QA = (q_x, q_y)
    S = scalar_mult(QA, n_B)
    print(S)
    shared_secret = S[0]
    iv = 'cd9da9f1c60925922377ea952afc212c'
    ciphertext = 'febcbe3a3414a730b125931dccf912d2239f3e969c4334d95ed0ec86f6449ad8'
    print(decrypt_flag(shared_secret, iv, ciphertext))
```
> FLAG :  crypto{3ff1c1ent_k3y_3xch4ng3}
### PARAMETER CHOICE
#### Smooth Criminal
![image](https://hackmd.io/_uploads/SyJU8Mbna.png)
 - [Source.py](https://cryptohack.org/static/challenges/source_ba064d03b53a5fd7321dd0007b72906b.py)
 - [output.txt](https://cryptohack.org/static/challenges/output_6cf0cf5ca7ab93bb829e557dd77e08ff.txt)
 - Challenge này chúng ta chỉ cần đi tìm lại n là có thể giải quyết bài toán.
 - Ta có public = G * n. Ở đây phép nhân vô hướng xuất hiện. Chúng ta sẽ quay lại bài toán logarit rời rạc để giải quyết bài toán này với những phương pháp tôi đã nói ở bài viết trên.
 - Khi có được n thì shared secret sẽ được tính bằng phép nhân vô hướng $B * n$.
 - Cuối cùng chúng ta sử dụng lại hàm decrypt của bài trước và lấy flag.
 - Solution bằng python
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib
from sage.all import *


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

p = 310717010502520989590157367261876774703
a = 2
b = 3
F = GF(p)
E = EllipticCurve(F, [a, b])

# Generator
g_x = 179210853392303317793440285562762725654
g_y = 105268671499942631758568591033409611165
G = E(g_x, g_y)

# Bob's public key
b_x = 272640099140026426377756188075937988094
b_y = 51062462309521034358726608268084433317
B = E(b_x, b_y)

# Send this to Bob!
public = E(280810182131414898730378982766101210916, 291506490768054478159835604632710368904)

n = discrete_log(public, G, operation='+')
print(f"{n = }")

iv = '07e2628b590095a5e332d397b8a59aa7'
encrypted_flag = '8220b7c47b36777a737f5ef9caa2814cf20c1c1ef496ec21a9b4833da24a008d0870d3ac3a6ad80065c138a2ed6136af'
shared_secret = (B * n)[0]
print(decrypt_flag(shared_secret, iv, encrypted_flag))
```
> FLAG : crypto{n07_4ll_curv3s_4r3_s4f3_curv3s}
#### Exceptional Curves
 - Challenge này cũng mã hóa tương tự bài trước nhưng lại chống lại thuật toán Pohlih-Hellman
 ![image](https://hackmd.io/_uploads/ryycmQM2p.png)
 - Sau một hồi tìm kiếm các cách tấn công ECC tôi tìm thấy [ECC Attack](https://github.com/merlinepedra/crypto-attacks/tree/master/attacks/ecc) và sử dụng **Smart Attack**
 - Các bạn có thể đọc qua [Smart attack](https://ariana1729.github.io/2021/05/31/SmartAttack.html)
 - Bên cạnh đó tôi dùng 1 đoạn source để check phương thức tấn công cho challenge này
```python3!
from sage.all import*

def check_pohlig_hellman(curve, generator=None):
    """
    The Pohlig-Hellman algorithm allows for quick (EC)DLP solving if the order of the curve is smooth,
    i.e its order is a product of multiple (small) primes.
    The best general purpose algorithm for finding a discrete logarithm is the Baby-step giant-step
    algorithm, with a running time of O(sqrt(n)).
    If the order of the curve (over a finite field) is smooth, we can however solve the (EC)DLP
    algorithm by solving the (EC)DLP for all the prime powers that make up the order, then using the
    Chinese remainder theorem to compute the (EC)DLP solution to our original order.
    If no generator is provided, we assume a cofactor of 1, and thus a generator subgroup order equal to the curve order.
    """


    if generator:
        order = generator.order()
    else:
        order = curve.order()
    factorization = factor(order)

    # baby-step giant-step complexity is O(sqrt(n))
    naive_complexity = order.nth_root(2, True)[0] + 1

    # for an order N = (p_0)^(e_0) * (p_1)^(e_1) * ... * (p_k)^(e^k) with p prime and e natural the complexity is:
    # O( \sum_{i=0}^k e_i ( log_2(n) + sqrt((p_i)) ) )

    logn = log(order, 2)

    pohlig_hellman_complexity  = sum( y * (logn + x.nth_root(2, True)[0]) for x, y in factorization)

    return (pohlig_hellman_complexity, naive_complexity)


def check_smart(curve):
    """
    The Smart attack allows for solving the ECDLP in linear time, given that the trace of Frobenius of the curve equals one,
    i.e the order of the curve is equal to the order of the finite field over which the elliptic curve is defined.
    If this is the case, we can create a group isomorphism from our curve E over Fp to the finite field Fp (which preserves addition).
    Solving the discrete 'log' problem in a finite field is easy, when our group is an additive one.
    Finding `m` such that mQ = P given P, Q is hard in an elliptic curve defined over a finite field, but easy over a finite field an sich:
    m = P * modinv(Q)
    """

    return curve.trace_of_frobenius() == 1

def check_mov(curve):
    """
    The MOV attack (Menezes, Okamoto, Vanstone) allows for solving the ECDLP in subexponential time, given that the trace of frobenius of the curve equals zero,
    i.e the curve is supersingular.
    If this is the case we can reduce the problem to the discrete logarithm problem in the finite field over which the curve is defined. Subexponential attacks are known.
    This differs from the smart attack in the sense that we have to solve the actual multiplicative discrete log problem Q^m = P,
    instead of the additive discrete log problem mQ = P
    """

    return curve.trace_of_frobenius() == 0

def check(curve, generator=None):

    def print_red(p):
        print("\u001b[41m" + str(p) + "\u001b[0m")

    ph = check_pohlig_hellman(curve, generator)

    if ph[0] < ph[1]:
        quotient = round( float(ph[1]/ph[0]), 2) - 1

        logs = [round(float(log(x, 2)), 2) for x in ph]

        print_red(f"Pohlig-Hellman can make ECDLP solving {quotient} times faster!")
        print(f"O(2^{logs[1]}) -> O(2^{logs[0]})")

    else:
        print("Pohlig-Hellman cannot improve ECDLP solving speed.")

    smart = check_smart(curve)

    if smart:
        print_red("Smart's attack can solve ECDLP in linear time!")
    else:
        print("Smart's attack does not apply.")

    mov = check_mov(curve)

    if mov:
        print_red("MOV attack can solve ECDLP in subexponential time!")
    else:
        print("MOV attack does not apply.")



p = 0xa15c4fb663a578d8b2496d3151a946119ee42695e18e13e90600192b1d0abdbb6f787f90c8d102ff88e284dd4526f5f6b6c980bf88f1d0490714b67e8a2a2b77
a = 0x5e009506fcc7eff573bc960d88638fe25e76a9b6c7caeea072a27dcd1fa46abb15b7b6210cf90caba982893ee2779669bac06e267013486b22ff3e24abae2d42
b = 0x2ce7d1ca4493b0977f088f6d30d9241f8048fdea112cc385b793bce953998caae680864a7d3aa437ea3ffd1441ca3fb352b0b710bb3f053e980e503be9a7fece

E = EllipticCurve(GF(p), [a, b])
check(E)
```
![image](https://hackmd.io/_uploads/HJzEeeLnp.png)
 - Smart's Attack là phương pháp tối ưu nhất cho bài này
 - Solution bằng python
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib
from sage.all import *

# Lifts a point to the p-adic numbers.
def _lift(E, P, gf):
    x, y = map(ZZ, P.xy())
    for point_ in E.lift_x(x, all=True):
        _, y_ = map(gf, point_.xy())
        if y == y_:
            return point_


def attack(G, P):
    """
    Solves the discrete logarithm problem using Smart's attack.
    More information: Smart N. P., "The discrete logarithm problem on elliptic curves of trace one"
    :param G: the base point
    :param P: the point multiplication result
    :return: l such that l * G == P
    """
    E = G.curve()
    gf = E.base_ring()
    p = gf.order()
    assert E.trace_of_frobenius() == 1, f"Curve should have trace of Frobenius = 1."

    E = EllipticCurve(Qp(p), [int(a) + p * ZZ.random_element(1, p) for a in E.a_invariants()])
    G = p * _lift(E, G, gf)
    P = p * _lift(E, P, gf)
    Gx, Gy = G.xy()
    Px, Py = P.xy()
    return int(gf((Px / Py) / (Gx / Gy)))

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

# Curve params
p = 0xa15c4fb663a578d8b2496d3151a946119ee42695e18e13e90600192b1d0abdbb6f787f90c8d102ff88e284dd4526f5f6b6c980bf88f1d0490714b67e8a2a2b77
a = 0x5e009506fcc7eff573bc960d88638fe25e76a9b6c7caeea072a27dcd1fa46abb15b7b6210cf90caba982893ee2779669bac06e267013486b22ff3e24abae2d42
b = 0x2ce7d1ca4493b0977f088f6d30d9241f8048fdea112cc385b793bce953998caae680864a7d3aa437ea3ffd1441ca3fb352b0b710bb3f053e980e503be9a7fece

# Define curve
E = EllipticCurve(GF(p), [a, b])

# Generator
G_x, G_y = (3034712809375537908102988750113382444008758539448972750581525810900634243392172703684905257490982543775233630011707375189041302436945106395617312498769005, 4986645098582616415690074082237817624424333339074969364527548107042876175480894132576399611027847402879885574130125050842710052291870268101817275410204850)
G = E(G_x, G_y)

b_x = 0x7f0489e4efe6905f039476db54f9b6eac654c780342169155344abc5ac90167adc6b8dabacec643cbe420abffe9760cbc3e8a2b508d24779461c19b20e242a38
b_y = 0xdd04134e747354e5b9618d8cb3f60e03a74a709d4956641b234daa8a65d43df34e18d00a59c070801178d198e8905ef670118c15b0906d3a00a662d3a2736bf
B = E(b_x, b_y)

# Public Key
a_x, a_y = (4748198372895404866752111766626421927481971519483471383813044005699388317650395315193922226704604937454742608233124831870493636003725200307683939875286865, 2421873309002279841021791369884483308051497215798017509805302041102468310636822060707350789776065212606890489706597369526562336256272258544226688832663757)
A = E(a_x, a_y)

n = attack(G, A)
print(n)

iv = '719700b2470525781cc844db1febd994'
encrypted_flag = '335470f413c225b705db2e930b9d460d3947b3836059fb890b044e46cbb343f0'
shared_secret = (B * n)[0]
print(decrypt_flag(shared_secret, iv, encrypted_flag))
```
> FLAG : crypto{H3ns3l_lift3d_my_fl4g!}
#### Micro Transmissions
![image](https://hackmd.io/_uploads/SJQpVHGh6.png)
 - [Source.sage](https://cryptohack.org/static/challenges/source_78fc27e69b84e3cf75538d4bff66c4a8.sage)
 - [output.txt](https://cryptohack.org/static/challenges/output_aabbd844dd2b52cbab4978e8f0b3b95c.txt)
 - Với bài này tôi sử dụng hàm python như bài **Smooth Criminal** nhưng không ra kết quả.
```sage!
sage: from sympy.ntheory.residue_ntheory import discrete_log
....: from Crypto.Cipher import AES
....: from Crypto.Util.Padding import pad, unpad
....: import hashlib
....: from sage.all import *
....:
....: def gen_shared_secret(P, n):
....:     S = n * P
....:     return S.xy()[0]
....:
....: p = 99061670249353652702595159229088680425828208953931838069069584252923270946291
....: a = 1
....: b = 4
....: E = EllipticCurve(GF(p), [a,b])
....:
....: ax = 87360200456784002948566700858113190957688355783112995047798140117594305287669
....: bx = 6082896373499126624029343293750138460137531774473450341235217699497602895121
....:
....: A = E.lift_x(ax)
....: B = E.lift_x(bx)
....: G = E(43190960452218023575787899214023014938926631792651638044680168600989609069200, 20971936269255296908588589778
....: 128791635639992476076894152303569022736123671173)
....:
....: n_a = discrete_log(A, G, operation='+')
....: print(n_a)

Killed
```
 - Tôi sử dụng thuật toán Pohlig-Hellman để tìm lại n 
 - Các bạn có thể đọc qua solution bài [ECC2 - picoctf 2017](https://github.com/hgarrereyn/Th3g3ntl3man-CTF-Writeups/blob/master/2017/picoCTF_2017/problems/cryptography/ECC2/ECC2.md) để hiểu rõ hơn
 - Solution chạy trên sagemath
```python3
from sympy.ntheory.residue_ntheory import discrete_log
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib
from sage.all import *


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
    
p = 99061670249353652702595159229088680425828208953931838069069584252923270946291
a = 1
b = 4
E = EllipticCurve(GF(p), [a,b])
G = E(43190960452218023575787899214023014938926631792651638044680168600989609069200, 20971936269255296908588589778128791635639992476076894152303569022736123671173)
A = E.lift_x(87360200456784002948566700858113190957688355783112995047798140117594305287669)
B = E.lift_x(6082896373499126624029343293750138460137531774473450341235217699497602895121)

factors, exponents = zip(*factor(E.order()))
primes = [factors[i] ^ exponents[i] for i in range(len(factors))][:-2]
dlogs = []
for fac in primes:
    t = int(G.order()) // int(fac)
    dlog = discrete_log(A*t,G*t,operation="+")
    dlogs += [dlog]
    print("factor: "+str(fac)+", Discrete Log: "+str(dlog)) 
l = crt(dlogs,primes)
print(l)

iv = 'ceb34a8c174d77136455971f08641cc5'
encrypted_flag ='b503bf04df71cfbd3f464aec2083e9b79c825803a4d4a43697889ad29eb75453'
shared_secret = (B * l)[0]
print(decrypt_flag(shared_secret, iv, encrypted_flag))
```
> FLAG : crypto{d0nt_l3t_n_b3_t00_sm4ll}
