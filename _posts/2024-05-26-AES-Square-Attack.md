---
title: AES Square Attack
date: 2024-05-26 20-00-00
categories: [Cryptography]
tags: [Cryptography, Learning]
image: '/assets/image/AES/square_attack/SQUARE-11.jpg'
math: true
---

AES Square Attack là một kỹ thuật phân tích mật mã được sử dụng để tấn công các hệ mật mã khối, đặc biệt là AES (Advanced Encryption Standard). Phương pháp này là một loại tấn công vi phân, nhằm vào tính đối xứng của các hoạt động trong các vòng của thuật toán mã hóa. Khai thác vào các tính chất không đổi của các round encryption trong cipher đó

Square Attack không phải là phương pháp tấn công mạnh nhất đối với AES khi thuật toán này được thực hiện đầy đủ với tất cả các vòng mã hóa, nhưng nó đã đóng góp quan trọng trong việc hiểu rõ hơn về tính bảo mật của các thuật toán mã hóa khối.

### AES 3 Round

![image](/assets/image/AES/square_attack/SQUARE-01.jpg)

Giả xử, ta có 256 bản rõ như sau, giờ ta sẽ gọi nó là ``𝛬-set`` (``𝛬-set`` là tập hợp các bytearray phân biệt có độ dài 16 và khác nhạu tịa vị trí index được gọi là **active index**)

Đầu tiên chúng ta sẽ xor với ``first round key``
![image](/assets/image/AES/square_attack/SQUARE-03.jpg)

Sau khi tất cả các phần tử trong ``𝛬-set`` thực hiện bước này, ta vẫn sẽ thu được ``𝛬-set`` với ``active index = 0``

Chúng ta sẽ đi vào vòng đầu tiên, phép biến đổi đầu tiên là ``SubBytes``

![image](/assets/image/AES/square_attack/SQUARE-04.jpg)

Với mỗi giá trị ``i``, ``SBOX[i]`` sẽ có một giá tị duy nhất. 

Các ``non-active index`` vẫn sẽ là chính nó, ``active index`` vẫn không thay đổi. Vì vậy ta vẫn thu được ``𝛬-set``

Tiếp theo ta đến ``ShiftRow``

![image](/assets/image/AES/square_attack/SQUARE-05.jpg)

Trong bước này ta vẫn thu được ``𝛬-set`` vì các bytes chỉ đổi chỗ cho nhau

Sau đó ta nhảy đến ``MixColumns``

![image](/assets/image/AES/square_attack/SQUARE-06.jpg)

Hãy nhớ cách tính toán từ cột mới từ cột cũ

$$
\begin{bmatrix}
2a_0 + 3a_1 + 1a_2 + 1a_3\\
1a_0 + 2a_1 + 3a_2 + 1a_3\\
1a_0 + 1a_1 + 2a_2 + 3a_3\\
3a_0 + 1a_1 + 1a_2 + 2a_3
\end{bmatrix}
$$

Ta thấy với 1 byte thay đổi có thể gây ảnh hưởng đến 3 bytes còn lại trong cột đó.

Sau bước này, ta sẽ có thêm 3 active index, do mỗi byte trong kết quả thu được là tổ hợp tuyến tính giữa 4 byte, gồm 3 byte cố định và 1 byte thay đổi.

$$
\underbrace{2a_0}_\text{active} +
\underbrace{3a_1 + 1a_2 + 1a_3}_\text{constant}
$$

![image](/assets/image/AES/square_attack/SQUARE-02.jpg)

Ở cuối của first round, chúng ta có ``𝛬-set`` với ``4 active index``

Và sau khi chạy hết round 3 thì ta có được như sau

![image](/assets/image/AES/square_attack/SQUARE-08.jpg)

Khi xong round thứ 3, ta không còn thu được ``𝛬-set`` nữa. Nhưng ta có thể suy ra một tính chất như sau: Ta xor toàn bộ byte đầu của các phần tử sau khi trải qua bước ``AddRoundKey``

$$
b_1 \oplus\\
b_2 \oplus\\
\cdots\\
b_{256}
$$

Tiếp đó

$$
\begin{gather*}
2 a_{1, 0} \oplus 3 a_{1, 1} \oplus 1 a_{1, 2} \oplus 1 a_{1, 3}\\
\oplus \\
2 a_{2, 0} \oplus 3 a_{2, 1} \oplus 1 a_{2, 2} \oplus 1 a_{2, 3}\\
\oplus \\
\cdots\\
\oplus\\
2 a_{256, 0} \oplus 3 a_{256, 1} \oplus 1 a_{256, 2} \oplus 1 a_{256, 3}\\
\end{gather*}
$$

Và biến đổi ta sẽ được như sau

$$
\begin{gather*}
2 (a_{0, 0} \oplus \cdots \oplus a_{256, 0})\\
\oplus\\
3 (a_{0, 1} \oplus \cdots \oplus a_{256, 1})\\
\oplus\\
1 (a_{0, 2} \oplus \cdots \oplus a_{256, 2})\\
\oplus\\
1(a_{0, 3}\oplus \cdots \oplus a_{256, 3})
\end{gather*}
= 2 \times 0 \oplus 3 \times 0 \oplus 1 \times 0 \oplus 1 \times 0 = 0
$$

Lưu ý rằng không chỉ byte đầu, mà toàn bộ các byte còn lại cũng sẽ có tính chất này. Ta gọi tính chất này là (*) và sẽ sử dụng nó để break AES 4 round.

### AES 4 Round

### AES 5 Round

### Reference

[1] _https://www.davidwong.fr/blockbreakers/square.html_