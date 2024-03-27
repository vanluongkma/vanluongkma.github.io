---
author: "vanluongkma"
title: "Symmetric Ciphers"
date: "2024-01-24"
tags: [
"Cryptohack",
]
---

### 1. Keyed Permutations
![image](https://hackmd.io/_uploads/HkUtPS4F6.png)
 - ``What is the mathetical term for a one-to-one correspondence?``
 ![image](https://hackmd.io/_uploads/BJgVY3NYa.png)
> FLAG : crypto{bijection}
### 2. Resisting Bruteforce
![image](https://hackmd.io/_uploads/rkJedrEF6.png)
 - ``What is the name for the best single-key attack against AES?``
 ![image](https://hackmd.io/_uploads/rJ-Vc2Vt6.png)
> FLAG : crypto{biclique}
### 3. Structure of AES
![image](https://hackmd.io/_uploads/r1iW_SEYp.png)
 - [matrix.py](https://cryptohack.org/static/challenges/matrix_e1b463dddbee6d17959618cf370ff1a5.py)
 - Bài này hàm ``bytes2matrix`` để chuyển đổi khối văn bản gốc ban đầu của chúng ta thành ma trận trạng thái. 
 - Yêu cầu ta viết hàm ``Matrix2bytes``để biến ma trận đó trở lại thành byte và gửi văn bản gốc kết quả làm cờ.

```python3
def bytes2matrix(text):
    """ Converts a 16-byte array into a 4x4 matrix.  """
    return [list(text[i:i+4]) for i in range(0, len(text), 4)]

def matrix2bytes(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    plaintext = ''
    for i in range(len(matrix)):
        for j in range(4):
            plaintext += chr(matrix[i][j])
    return plaintext

matrix = [
    [99, 114, 121, 112],
    [116, 111, 123, 105],
    [110, 109, 97, 116],
    [114, 105, 120, 125],
]

print(matrix2bytes(matrix))
```
> FLAG : crypto{inmatrix}
### 4. Round Keys
![image](https://hackmd.io/_uploads/H1xjM_SEFa.png)
 - Nhìn vào sơ đồ ta có : từng phần tử trong ma trận X sẽ được XOR với phần tử ở vị trí tương ứng trong ma trận round_key.
 - Bài này tôi sẽ xor từng phần tử của 2 matrix với nhau rồi chuyển sang byte.
```python3
state = [
    [206, 243, 61, 34],
    [171, 11, 93, 31],
    [16, 200, 91, 108],
    [150, 3, 194, 51],
]

round_key = [
    [173, 129, 68, 82],
    [223, 100, 38, 109],
    [32, 189, 53, 8],
    [253, 48, 187, 78],
]


def add_round_key(state, round_key):
    for i in range(4):
        for j in range(4):
            state[i][j] ^= round_key[i][j]
    return state

def matrix2bytes(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    plaintext = ''
    for i in range(len(matrix)):
        for j in range(4):
            plaintext += chr(matrix[i][j])
    return plaintext


print(add_round_key(state, round_key))
print(matrix2bytes(state))
```
> FLAG : crypto{r0undk3y}
### 5. Confusion through Substitution
![image](https://hackmd.io/_uploads/ByeNuB4K6.png)
    - Nhìn vào mô hình ta thấy từng phần tử của ma trận X được thay thế bằng giá trị tra cứu trong bảng  ``S-BOX``
        - Bài này tôi chỉ cần triển khai hàm ``sub_byte()``, gửi ma trận state qua hộp ``inv_S_BOX`` rồi chuyển đổi nó thành byte để lấy flag.
```python3
s_box = (
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16,
)

inv_s_box = (
    0x52, 0x09, 0x6A, 0xD5, 0x30, 0x36, 0xA5, 0x38, 0xBF, 0x40, 0xA3, 0x9E, 0x81, 0xF3, 0xD7, 0xFB,
    0x7C, 0xE3, 0x39, 0x82, 0x9B, 0x2F, 0xFF, 0x87, 0x34, 0x8E, 0x43, 0x44, 0xC4, 0xDE, 0xE9, 0xCB,
    0x54, 0x7B, 0x94, 0x32, 0xA6, 0xC2, 0x23, 0x3D, 0xEE, 0x4C, 0x95, 0x0B, 0x42, 0xFA, 0xC3, 0x4E,
    0x08, 0x2E, 0xA1, 0x66, 0x28, 0xD9, 0x24, 0xB2, 0x76, 0x5B, 0xA2, 0x49, 0x6D, 0x8B, 0xD1, 0x25,
    0x72, 0xF8, 0xF6, 0x64, 0x86, 0x68, 0x98, 0x16, 0xD4, 0xA4, 0x5C, 0xCC, 0x5D, 0x65, 0xB6, 0x92,
    0x6C, 0x70, 0x48, 0x50, 0xFD, 0xED, 0xB9, 0xDA, 0x5E, 0x15, 0x46, 0x57, 0xA7, 0x8D, 0x9D, 0x84,
    0x90, 0xD8, 0xAB, 0x00, 0x8C, 0xBC, 0xD3, 0x0A, 0xF7, 0xE4, 0x58, 0x05, 0xB8, 0xB3, 0x45, 0x06,
    0xD0, 0x2C, 0x1E, 0x8F, 0xCA, 0x3F, 0x0F, 0x02, 0xC1, 0xAF, 0xBD, 0x03, 0x01, 0x13, 0x8A, 0x6B,
    0x3A, 0x91, 0x11, 0x41, 0x4F, 0x67, 0xDC, 0xEA, 0x97, 0xF2, 0xCF, 0xCE, 0xF0, 0xB4, 0xE6, 0x73,
    0x96, 0xAC, 0x74, 0x22, 0xE7, 0xAD, 0x35, 0x85, 0xE2, 0xF9, 0x37, 0xE8, 0x1C, 0x75, 0xDF, 0x6E,
    0x47, 0xF1, 0x1A, 0x71, 0x1D, 0x29, 0xC5, 0x89, 0x6F, 0xB7, 0x62, 0x0E, 0xAA, 0x18, 0xBE, 0x1B,
    0xFC, 0x56, 0x3E, 0x4B, 0xC6, 0xD2, 0x79, 0x20, 0x9A, 0xDB, 0xC0, 0xFE, 0x78, 0xCD, 0x5A, 0xF4,
    0x1F, 0xDD, 0xA8, 0x33, 0x88, 0x07, 0xC7, 0x31, 0xB1, 0x12, 0x10, 0x59, 0x27, 0x80, 0xEC, 0x5F,
    0x60, 0x51, 0x7F, 0xA9, 0x19, 0xB5, 0x4A, 0x0D, 0x2D, 0xE5, 0x7A, 0x9F, 0x93, 0xC9, 0x9C, 0xEF,
    0xA0, 0xE0, 0x3B, 0x4D, 0xAE, 0x2A, 0xF5, 0xB0, 0xC8, 0xEB, 0xBB, 0x3C, 0x83, 0x53, 0x99, 0x61,
    0x17, 0x2B, 0x04, 0x7E, 0xBA, 0x77, 0xD6, 0x26, 0xE1, 0x69, 0x14, 0x63, 0x55, 0x21, 0x0C, 0x7D,
)

state = [
    [251, 64, 182, 81],
    [146, 168, 33, 80],
    [199, 159, 195, 24],
    [64, 80, 182, 255],
]


def matrix2bytes(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    plaintext = ''
    for i in range(len(matrix)):
        for j in range(4):
            plaintext += chr(matrix[i][j])
    return plaintext 

def sub_bytes(state, sbox=s_box):
    for i in range(4):
        for j in range(4):
            state[i][j] = sbox[state[i][j]]
    return state

print(sub_bytes(state, sbox=inv_s_box))
print(matrix2bytes(state))
```
> FLAG : crypto{l1n34rly}
### 6. Diffusion through Permutation
![image](https://hackmd.io/_uploads/S1OUdr4Yp.png)
 - Nhìn vào mô hình ta thấy ``ShiftRows`` hoạt động:
     - Hàng thứ nhất không dịch chuyển.
     - Hàng thứ 2 dịch trái 1 byte
     - Hàng thứ 3 dịch trái 2 byte
     - Hàng cuối cùng dịch trái 3 byte.
     - Ta sẽ áp dụng xuống hàm ``inv_shift_rowas`` phía dưới.
 - Mixcolumn() : các cột trong matrix được coi như là đa thức trong trường hữu hạn GF(2^8) và nhân với đa thức: $$ C(x) = 3X^3 + X^2 + X + 2 $$
 - Vì thế nó được coi như phép nhân matrix trong trường hợp này.
 - ``run inv_mix_columns on it, then inv_shift_rows, convert to bytes and you will have your flag.``
```python3
def shift_rows(s):
    s[0][1], s[1][1], s[2][1], s[3][1] = s[1][1], s[2][1], s[3][1], s[0][1]
    s[0][2], s[1][2], s[2][2], s[3][2] = s[2][2], s[3][2], s[0][2], s[1][2]
    s[0][3], s[1][3], s[2][3], s[3][3] = s[3][3], s[0][3], s[1][3], s[2][3]


def inv_shift_rows(s):
    s[1][1], s[2][1], s[3][1], s[0][1]= s[0][1], s[1][1], s[2][1], s[3][1]
    s[2][2], s[3][2], s[0][2], s[1][2]= s[0][2], s[1][2], s[2][2], s[3][2]
    s[3][3], s[0][3], s[1][3], s[2][3]= s[0][3], s[1][3], s[2][3], s[3][3]


# learned from http://cs.ucsb.edu/~koc/cs178/projects/JT/aes.c
xtime = lambda a: (((a << 1) ^ 0x1B) & 0xFF) if (a & 0x80) else (a << 1)


def mix_single_column(a):
    # see Sec 4.1.2 in The Design of Rijndael
    t = a[0] ^ a[1] ^ a[2] ^ a[3]
    u = a[0]
    a[0] ^= t ^ xtime(a[0] ^ a[1])
    a[1] ^= t ^ xtime(a[1] ^ a[2])
    a[2] ^= t ^ xtime(a[2] ^ a[3])
    a[3] ^= t ^ xtime(a[3] ^ u)


def mix_columns(s):
    for i in range(4):
        mix_single_column(s[i])


def inv_mix_columns(s):
    # see Sec 4.1.3 in The Design of Rijndael
    for i in range(4):
        u = xtime(xtime(s[i][0] ^ s[i][2]))
        v = xtime(xtime(s[i][1] ^ s[i][3]))
        s[i][0] ^= u
        s[i][1] ^= v
        s[i][2] ^= u
        s[i][3] ^= v

    mix_columns(s)


state = [
    [108, 106, 71, 86],
    [96, 62, 38, 72],
    [42, 184, 92, 209],
    [94, 79, 8, 54],
]


def matrix2byte(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    plaintext = ''
    for i in range(4):
        for j in range(4):
            plaintext += chr(matrix[i][j])
    return plaintext

inv_mix_columns(state)
inv_shift_rows(state)

print(matrix2byte(state))
```
> FLAG : crypto{d1ffUs3R}
### 7. Bringing It All Together
![image](https://hackmd.io/_uploads/SktDOrVYT.png)
 - Ở challenge này là tổng hợp của các challenge bên trên ta tận dụng các hàm đã có ở trên để solve.Trình tự giải mã diễn ra như hình bên dưới
![image](https://hackmd.io/_uploads/SJxij1BYa.png)
 - Đầu tiên chuyên ``ciphertext`` thành ma trận ``state`` 
 - Sau đó ``add_round_key`` lần 1 với round_key cuối cùng là ``round_key[10]`` vì đây là ``AES - 128 bit``.
 - Tiếp theo là 9 vòng lặp gồm các bước ``inv_shift_row``, ``inv_sub_bytes``, ``add_round_key`` và ``inv_mix_columns``.
 - Cuối cùng chạy vòng cuối gồm ``invShiftRows``, ``invSubBytes``, ``AddRoundKey``
```python3
N_ROUNDS = 10

key        = b'\xc3,\\\xa6\xb5\x80^\x0c\xdb\x8d\xa5z*\xb6\xfe\\'
ciphertext = b'\xd1O\x14j\xa4+O\xb6\xa1\xc4\x08B)\x8f\x12\xdd'



s_box = (
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16,
)

inv_s_box = (
    0x52, 0x09, 0x6A, 0xD5, 0x30, 0x36, 0xA5, 0x38, 0xBF, 0x40, 0xA3, 0x9E, 0x81, 0xF3, 0xD7, 0xFB,
    0x7C, 0xE3, 0x39, 0x82, 0x9B, 0x2F, 0xFF, 0x87, 0x34, 0x8E, 0x43, 0x44, 0xC4, 0xDE, 0xE9, 0xCB,
    0x54, 0x7B, 0x94, 0x32, 0xA6, 0xC2, 0x23, 0x3D, 0xEE, 0x4C, 0x95, 0x0B, 0x42, 0xFA, 0xC3, 0x4E,
    0x08, 0x2E, 0xA1, 0x66, 0x28, 0xD9, 0x24, 0xB2, 0x76, 0x5B, 0xA2, 0x49, 0x6D, 0x8B, 0xD1, 0x25,
    0x72, 0xF8, 0xF6, 0x64, 0x86, 0x68, 0x98, 0x16, 0xD4, 0xA4, 0x5C, 0xCC, 0x5D, 0x65, 0xB6, 0x92,
    0x6C, 0x70, 0x48, 0x50, 0xFD, 0xED, 0xB9, 0xDA, 0x5E, 0x15, 0x46, 0x57, 0xA7, 0x8D, 0x9D, 0x84,
    0x90, 0xD8, 0xAB, 0x00, 0x8C, 0xBC, 0xD3, 0x0A, 0xF7, 0xE4, 0x58, 0x05, 0xB8, 0xB3, 0x45, 0x06,
    0xD0, 0x2C, 0x1E, 0x8F, 0xCA, 0x3F, 0x0F, 0x02, 0xC1, 0xAF, 0xBD, 0x03, 0x01, 0x13, 0x8A, 0x6B,
    0x3A, 0x91, 0x11, 0x41, 0x4F, 0x67, 0xDC, 0xEA, 0x97, 0xF2, 0xCF, 0xCE, 0xF0, 0xB4, 0xE6, 0x73,
    0x96, 0xAC, 0x74, 0x22, 0xE7, 0xAD, 0x35, 0x85, 0xE2, 0xF9, 0x37, 0xE8, 0x1C, 0x75, 0xDF, 0x6E,
    0x47, 0xF1, 0x1A, 0x71, 0x1D, 0x29, 0xC5, 0x89, 0x6F, 0xB7, 0x62, 0x0E, 0xAA, 0x18, 0xBE, 0x1B,
    0xFC, 0x56, 0x3E, 0x4B, 0xC6, 0xD2, 0x79, 0x20, 0x9A, 0xDB, 0xC0, 0xFE, 0x78, 0xCD, 0x5A, 0xF4,
    0x1F, 0xDD, 0xA8, 0x33, 0x88, 0x07, 0xC7, 0x31, 0xB1, 0x12, 0x10, 0x59, 0x27, 0x80, 0xEC, 0x5F,
    0x60, 0x51, 0x7F, 0xA9, 0x19, 0xB5, 0x4A, 0x0D, 0x2D, 0xE5, 0x7A, 0x9F, 0x93, 0xC9, 0x9C, 0xEF,
    0xA0, 0xE0, 0x3B, 0x4D, 0xAE, 0x2A, 0xF5, 0xB0, 0xC8, 0xEB, 0xBB, 0x3C, 0x83, 0x53, 0x99, 0x61,
    0x17, 0x2B, 0x04, 0x7E, 0xBA, 0x77, 0xD6, 0x26, 0xE1, 0x69, 0x14, 0x63, 0x55, 0x21, 0x0C, 0x7D,
)


def bytes2matrix(text):
    """ Converts a 16-byte array into a 4x4 matrix.  """
    return [list(text[i:i+4]) for i in range(0, len(text), 4)]

def matrix2bytes(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    plaintext = ''
    for i in range(len(matrix)):
        for j in range(4):
            plaintext += chr(matrix[i][j])
    return plaintext

def add_round_key(state, round_key):
    for i in range(4):
        for j in range(4):
            state[i][j] ^= round_key[i][j]
    return state

def sub_bytes(state, sbox=s_box):
    for i in range(4):
        for j in range(4):
            state[i][j] = sbox[state[i][j]]
    return state

# learned from http://cs.ucsb.edu/~koc/cs178/projects/JT/aes.c
xtime = lambda a: (((a << 1) ^ 0x1B) & 0xFF) if (a & 0x80) else (a << 1)


def mix_single_column(a):
    # see Sec 4.1.2 in The Design of Rijndael
    t = a[0] ^ a[1] ^ a[2] ^ a[3]
    u = a[0]
    a[0] ^= t ^ xtime(a[0] ^ a[1])
    a[1] ^= t ^ xtime(a[1] ^ a[2])
    a[2] ^= t ^ xtime(a[2] ^ a[3])
    a[3] ^= t ^ xtime(a[3] ^ u)


def mix_columns(s):
    for i in range(4):
        mix_single_column(s[i])


def inv_mix_columns(s):
    # see Sec 4.1.3 in The Design of Rijndael
    for i in range(4):
        u = xtime(xtime(s[i][0] ^ s[i][2]))
        v = xtime(xtime(s[i][1] ^ s[i][3]))
        s[i][0] ^= u
        s[i][1] ^= v
        s[i][2] ^= u
        s[i][3] ^= v

    mix_columns(s)

def shift_rows(s):
    s[0][1], s[1][1], s[2][1], s[3][1] = s[1][1], s[2][1], s[3][1], s[0][1]
    s[0][2], s[1][2], s[2][2], s[3][2] = s[2][2], s[3][2], s[0][2], s[1][2]
    s[0][3], s[1][3], s[2][3], s[3][3] = s[3][3], s[0][3], s[1][3], s[2][3]


def inv_shift_rows(s):
    s[1][1], s[2][1], s[3][1], s[0][1]= s[0][1], s[1][1], s[2][1], s[3][1]
    s[2][2], s[3][2], s[0][2], s[1][2]= s[0][2], s[1][2], s[2][2], s[3][2]
    s[3][3], s[0][3], s[1][3], s[2][3]= s[0][3], s[1][3], s[2][3], s[3][3]

def expand_key(master_key):
    """
    Expands and returns a list of key matrices for the given master_key.
    """

    # Round constants https://en.wikipedia.org/wiki/AES_key_schedule#Round_constants
    r_con = (
        0x00, 0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40,
        0x80, 0x1B, 0x36, 0x6C, 0xD8, 0xAB, 0x4D, 0x9A,
        0x2F, 0x5E, 0xBC, 0x63, 0xC6, 0x97, 0x35, 0x6A,
        0xD4, 0xB3, 0x7D, 0xFA, 0xEF, 0xC5, 0x91, 0x39,
    )

    # Initialize round keys with raw key material.
    key_columns = bytes2matrix(master_key)
    iteration_size = len(master_key) // 4

    # Each iteration has exactly as many columns as the key material.
    i = 1
    while len(key_columns) < (N_ROUNDS + 1) * 4:
        # Copy previous word.
        word = list(key_columns[-1])

        # Perform schedule_core once every "row".
        if len(key_columns) % iteration_size == 0:
            # Circular shift.
            word.append(word.pop(0))
            # Map to S-BOX.
            word = [s_box[b] for b in word]
            # XOR with first byte of R-CON, since the others bytes of R-CON are 0.
            word[0] ^= r_con[i]
            i += 1
        elif len(master_key) == 32 and len(key_columns) % iteration_size == 4:
            # Run word through S-box in the fourth iteration when using a
            # 256-bit key.
            word = [s_box[b] for b in word]

        # XOR with equivalent word from previous iteration.
        word = bytes(i^j for i, j in zip(word, key_columns[-iteration_size]))
        key_columns.append(word)

    # Group key words in 4x4 byte matrices.
    return [key_columns[4*i : 4*(i+1)] for i in range(len(key_columns) // 4)]


def decrypt(key, ciphertext):
    round_keys = expand_key(key) # Remember to start from the last round key and work backwards through them when decrypting
    # Convert ciphertext to state matrix
    state = bytes2matrix(ciphertext)
    # Initial add round key step
    state = add_round_key(state,round_keys[10])
    for i in range(N_ROUNDS - 1, 0, -1):
        pass # Do round
        inv_shift_rows(state)
        state = sub_bytes(state,inv_s_box)
        state = add_round_key(state, round_keys[i])
        inv_mix_columns(state)

    # Run final round (skips the InvMixColumns step)
    inv_shift_rows(state)
    state = sub_bytes(state, inv_s_box)
    state = add_round_key(state, round_keys[0])
    # Convert state matrix to plaintext
    plaintext = matrix2bytes(state)
    return plaintext


print(decrypt(key, ciphertext))
```
 - Một cách khác mình sử dụng thư viện AES trong python mode AES-ECB để giải mã và lấy flag.
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*

key        = b'\xc3,\\\xa6\xb5\x80^\x0c\xdb\x8d\xa5z*\xb6\xfe\\'
ciphertext = b'\xd1O\x14j\xa4+O\xb6\xa1\xc4\x08B)\x8f\x12\xdd'


def decrypt_ecb(ciphertext):
    cipher = AES.new(key, AES.MODE_ECB)
    plaintext = cipher.decrypt(ciphertext)
    return plaintext


print(decrypt_ecb(ciphertext))
```
> FLAG : crypto{MYAES128}
### 8. Modes of Operation Starter
![image](https://hackmd.io/_uploads/r1Fddr4tp.png)
 - Source: [block_cipher_starter](https://aes.cryptohack.org/block_cipher_starter/)
 - Với challenges này đã có 2 hàm là decrypt() và encrypt().
 - Mã hóa và giải mã theo mode AES-ECB
 - Ta chỉ việc lấy ciphertext rồi gửi lại vào server thì sẽ lấy được flag.
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
import requests


def encrypt_flag():
	url = "https://aes.cryptohack.org/block_cipher_starter/encrypt_flag/"
	r = requests.get(url)
	js = r.json()
	return bytes.fromhex(js["ciphertext"])

def decrypt_flag(ciphertext):
	url = "https://aes.cryptohack.org/block_cipher_starter/decrypt/"
	url += ciphertext.hex()
	url += "/"
	r = requests.get(url)
	js = r.json()
	return bytes.fromhex(js["plaintext"])

ciphertext = encrypt_flag()
print(decrypt_flag(ciphertext))
```
> FLAG : crypto{bl0ck_c1ph3r5_4r3_f457_!}
### 9. Passwords as Keys
![image](https://hackmd.io/_uploads/BkBKuBNK6.png)
 - Source: [passwords_as_key](https://aes.cryptohack.org/passwords_as_keys/)
 - Với bài này ta thấy key được lấy bất kì trong list [words](https://gist.githubusercontent.com/wchargin/8927565/raw/d9783627c731268fb2935a731a618aa8e95cf465/words)
 - Việc còn lại ta chỉ cần giải mã theo mode AES-ECB và brute force key cho đến khi xuât hiện format flag.
```python3!
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
import hashlib, requests

# /usr/share/dict/words from
# https://gist.githubusercontent.com/wchargin/8927565/raw/d9783627c731268fb2935a731a618aa8e95cf465/words
with open("/home/patriot/words.txt") as f:  # đường dẫn đến file words
    words = [w.strip() for w in f.readlines()]
print(len(words))

def encrypt_flag():
	url = "https://aes.cryptohack.org/passwords_as_keys/encrypt_flag/"
	r = requests.get(url)
	js = r.json()
	return bytes.fromhex(js["ciphertext"])

ciphertext = encrypt_flag()
print(ciphertext)

for i in range(len(words)):
    KEY = hashlib.md5(words[i].encode()).digest()
    cipher = AES.new(KEY, AES.MODE_ECB)
    decrypted = cipher.decrypt(ciphertext)
    print(decrypted)
    if b'crypto{' in decrypted:
        print(decrypted)
        break
```
> FLAG : crypto{k3y5__r__n07__p455w0rdz?}
### 10. ECB CBC WTF
![image](https://hackmd.io/_uploads/BJ1q_SVt6.png)
 - Source: [ecbcbcwtf](https://aes.cryptohack.org/ecbcbcwtf/)
 - Với bài này ta có:
![image](https://hackmd.io/_uploads/r1RejZBKa.png)
ciphertext trả về sẽ được cộng thêm 16 bytes của iv lên đầu.
 - Ở đây 
![image](https://hackmd.io/_uploads/HJDT2ZrY6.png)

 - Source của bài này mã hóa bằng mode AES-CBC và được giải mã bằng mode AES-ECB
![image](https://hackmd.io/_uploads/r1mjbzBtT.png)
 
![image](https://hackmd.io/_uploads/S1XDeMBta.png)
![image](https://hackmd.io/_uploads/SJ9bmGHK6.png)

 - Nhìn vào sơ đồ trên ta có:
     - Nếu tôi gửi ciphertext và đầu ra sẽ giải mã theo mode AES-ECB khi đó đầu ra tôi cần xor với IV sẽ được bản mã hoàn chỉnh đầu tiên.
     - Với khối tiếp mã hóa bằng AES-CBC 16 bytes của Ciphertext sẽ được xor với plaintext. Khi đó làm giống lần giải mã đầu tiên tôi sẽ lấy 16 bytes của ciphertext xor với plaintext sẽ được bản bã hoàn chỉnh tiếp. 
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
from pwn import xor
import requests


def encrypt_flag():
	url = "https://aes.cryptohack.org/ecbcbcwtf/encrypt_flag/"
	r = requests.get(url)
	js = r.json()
	return (js["ciphertext"])

def decrypt(ciphertext):
	url = "https://aes.cryptohack.org/ecbcbcwtf//decrypt/"
	url += ciphertext + "/"
	r = requests.get(url)
	js = r.json()
	return (js["plaintext"])

cipher = encrypt_flag()
plaintext = decrypt(cipher)

iv1 = bytes.fromhex(cipher[:32])
ciphertext = cipher[32:]


iv2 = bytes.fromhex(ciphertext[:32])

plaintext1 =  bytes.fromhex(plaintext[32:64])
plaintext2 = bytes.fromhex(plaintext[64:])

print(xor(plaintext1, iv1) + xor(plaintext2, iv2))
```
> FLAG : crypto{3cb_5uck5_4v01d_17_!!!!!}
### 11. ECB Oracle
![image](https://hackmd.io/_uploads/B1tcdrEYp.png)
 - Source : [ecb_oracle](https://aes.cryptohack.org/ecb_oracle/)
![image](https://hackmd.io/_uploads/SJfijtHYp.png)
 - Nhìn vào hàm mã hóa, khi ta nhập vào thì server sẽ trả về ``AES-ECB(pad(input | flag))``
 - Dựa vào điểm yếu của mode ECB: những block plaintext giống nhau sẽ cho ra những block ciphertext giống nhau 
 - Tôi cần tạo 1 đoạn 24 b'1' + 7 b'crypto{' + 1 byte kí tự  brute force flag 
`` b"111111111111111111111111crypto{" \ + \ b"x" ``
 - Vì những block plaintext giống nhau sẽ cho ra những block ciphertext giống nhau.
 - Nên với lần gửi đầu tiên 24 b'1' thì encrypt() sẽ mã hóa và cộng sau đó là b'crypto{' + 1 byte kí tự
 - Tiếp đó tôi sẽ cộng thêm chuỗi b'crypto{' + 1 byte kí tự(chr(32-127)) và so sánh với lần payload ban đầu cho khi chúng bằng nhau. Khi đó ta sẽ thu được b'111111111111111111111111crypto{p'.
 - Các vòng lặp tiếp theo ta sẽ làm tương tự như vậy nhưng sẽ giảm bớt 1 b'1' để cho kí tự tiếp theo của flag được thêm vào.
 - Brute force cho đến khi thu được flag hoàn chỉnh.
```python3
import requests
from Crypto.Cipher import AES

flag = b'crypto{'

def encrypt(plaintext):
    url = 'https://aes.cryptohack.org/ecb_oracle/encrypt/' + plaintext.hex()
    r = requests.get(url).json()
    return bytes.fromhex(r['ciphertext'])


b = ''.join([chr(i) for i in range(32, 127)])

while True:
    plaintext = b'1' * (31 - len(flag))
    target = encrypt(plaintext)[16:32]
    plaintext += flag
    
    found = False
    
    for j in b:
        plaintext_temp = plaintext[:31] + j.encode()
        bullet = encrypt(plaintext_temp)[16:32]
        if bullet == target:
            flag += j.encode()
            print(flag)
            found = True
            break
    
    if not found:
        break
```
> FLAG : crypto{p3n6u1n5_h473_3cb}
### 12. Flipping Cookie
![image](https://hackmd.io/_uploads/rkDsdrNtp.png)
 - Source: [flipping_cookie](https://aes.cryptohack.org/flipping_cookie/)
 - Với bài này khi get cookie 
![image](https://hackmd.io/_uploads/Sy-6azSF6.png) 
thì server sẽ trả về IV + ciphertext.
 - Từ đó 16 byte đầu của cookie sẽ là IV.
 - Bài này yêu cầu khi decrypt đoạn mã thì ``admin=True`` thì sẽ return flag.
![image](https://hackmd.io/_uploads/ry-qsEHt6.png)
 - Ở khối đầu tiên theo mô hình giải mã ta sẽ có $$plaintext = IV ⊕ D(ciphertext) $$
 - Hay $$ b'admin=False' = IV ⊕ D(ciphertext) $$
 - Muốn thay đổi ``b'admin=False'`` thành ``b'admin=True'`` ta cần làm như sau.
    $$ D(ciphertext) = IV \ ⊕ \ b'admin=False' $$
    $$ D(ciphertext) = X \ ⊕ \ b'admin=True' $$
    $$ => X \ ⊕ \ b'admin=True' = IV \ ⊕ \ b'admin=False' $$
    $$ => X = IV_{new} =  IV \ ⊕ \ b'admin=False' \ ⊕ \ b'admin=True' $$
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
from pwn import xor
import requests

def get_cookie():
	url = "https://aes.cryptohack.org/flipping_cookie/get_cookie/"
	r = requests.get(url)
	js = r.json()
	return bytes.fromhex(js["cookie"])

def check_admin(cookie, iv):
    url = "http://aes.cryptohack.org/flipping_cookie/check_admin/"
    url += cookie.hex() + "/" + iv.hex() + "/"
    r = requests.get(url)
    js = r.json()
    print(js)

cookie = get_cookie()
print(f"{cookie = }")

admin_false = (b"admin=False;expiry=")[:16]
admin_true = pad(b"admin=True;", 16)

IV = cookie[:16]
k1 = cookie[16:32]

IV_new = xor(xor(admin_false, admin_true), IV)
print(f"{IV_new.hex() = }")
print("cookie = " , k1.hex())

check_admin(k1, IV_new)
```
### 13. Lazy CBC
![image](https://hackmd.io/_uploads/r1W2dSNKp.png)
![image](https://hackmd.io/_uploads/B1KizUrtp.png)
 - Bài này nếu ta nhập key vào bằng KEY của server thì sẽ return flag.
 - Ta để ý hàm ``encrypt()`` và ``receive()`` có $$cipher = AES.new(KEY, AES.MODE-CBC, KEY)$$
=> key = IV
 - Vậy Giờ ta gửi 32 b'\x00' vào server
 - Khi đó theo mô hình giải mã AES-CBC ta có:
 $$  Plaintext_0  = D(Ciphertext_0) \ ⊕ \ IV $$
 $$ Plaintext_1 = D(Ciphertext_1) \ ⊕ \ Ciphertext_0 $$
 $$ Plaintext_2 = D(Ciphertext_2) \ ⊕ \ Ciphertext_1 $$
  - Giả dụ ta xor:
  $$ Plaintext_0 \ ⊕ \ Plaintext_1 \ = \ D(Ciphertext_0) \ ⊕ \ IV \ ⊕ \ D(Ciphertext_1) \ ⊕ \ Ciphertext_0 $$
  - Khi đó với 32 byte "\x00" ta gửi vào ở trước thì ta sẽ có
    $$ Plaintext_0 \ ⊕ \ Plaintext_1 \ = \ D(0) \ ⊕ \ IV \ ⊕ \ D(0) \ ⊕ \ 0 = IV = key$$
  - Giờ ta chỉ cần gửi key vào server và lụm flag.
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
from pwn import xor
import requests

def encrypt(plaintext):
	url = "https://aes.cryptohack.org/lazy_cbc/encrypt/"
	url += plaintext.hex()
	r = requests.get(url)
	js = r.json()
	return (js["ciphertext"])

def get_flag(key):
    url = "https://aes.cryptohack.org/lazy_cbc/get_flag/"
    url += key.hex() + "/"
    r = requests.get(url)
    js = r.json()
    print(bytes.fromhex(js["plaintext"]))
    
def receice(ciphertext):
    url = "https://aes.cryptohack.org/lazy_cbc/receive/"
    url += ciphertext.hex() + "/"
    r = requests.get(url)
    js = r.json()
    return bytes.fromhex(js["error"][len("Invalid plaintext: "):])

ciphertext = b"\x00" * 32
plaintext = receice(ciphertext)
print(f"{plaintext = }")
get_flag(xor(plaintext[:16], plaintext[16:]))
```
> FLAG : crypto{50m3_p30pl3_d0n7_7h1nk_IV_15_1mp0r74n7_?}
### 14. Triple DES
![image](https://hackmd.io/_uploads/SJ53uHNKa.png)
 - Source : [triple_des](https://aes.cryptohack.org/triple_des/)
 - ``This challenge demonstrates a strange weakness of DES which a secure block cipher should not have.``
 - Với challenge này mình sẽ tìm kiếm điểm yếu của DES đó là các khóa yếu của DES đã liệt kê ở phần lý thuyết.
 - Ở đây tôi dùng: ||0000000000000000FEFEFEFEFEFEFEFE||
     - Ta sẽ gửi key vào hàm ``encrypt_flag(key)`` sau đó gửi ciphertext và key đó vào hàm ``encrypt(key, plaintext)``. Server sẽ trả về cho ta flag.
```python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import*
from pwn import xor
import requests

def encrypt_flag(key):
	url = "https://aes.cryptohack.org/triple_des/encrypt_flag/"
	url += key
	r = requests.get(url)
	js = r.json()
	return (js["ciphertext"])

def encrypt(key, plaintext):
    url = "https://aes.cryptohack.org/triple_des/encrypt/"
    url += key + "/" + plaintext + "/"
    r = requests.get(url)
    js = r.json()
    print(bytes.fromhex(js["ciphertext"]))

key = "0000000000000000FEFEFEFEFEFEFEFE"
ciphertext = encrypt_flag(key)
flag = encrypt(key, ciphertext)
```
> FLAG : crypto{n0t_4ll_k3ys_4r3_g00d_k3ys}
### 15. Paper Plane
 ![image](https://hackmd.io/_uploads/rJrLT2OKT.png)
 - Các bạn có thể kéo lên phần``padding oracle attack`` ở phần Symmetric Cryptography để đọc lời giải của bài này.
> FLAG : crypto{h3ll0_t3l3gr4m}
### 16. Forbidden Fruit
![image](https://hackmd.io/_uploads/SkRikpRFp.png)
 - Các bạn có thể kéo lên phần ``Key-Recovery Attacks on GCM with Repeated Nonces`` ở phần Symmetric Cryptography để đọc lời giải của bài này.
> FLAG : crypto{https://github.com/attr-encrypted/encryptor/pull/22}
