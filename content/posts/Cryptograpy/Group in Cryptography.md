---
author: "vanluongkma"
title: "Group in Cryptography"
date: "2024-02-01"
tags: [
"Learning", "Crytography",
]
---

# Lý thuyết nhóm (Abstract Algebra)
## Định nghĩa
 - Trong toán học, một nhóm (group) là một tập hợp các phần tử được trang bị một phép toán hai ngôi kết hợp hai phần tử bất kỳ của tập hợp thành một phần tử thứ ba thỏa mãn bốn điều kiện gọi là tiên đề nhóm, lần lượt là tính đóng, tính kết hợp, sự tồn tại của phần tử đơn vị và tính khả nghịch. Một trong những ví dụ quen thuộc nhất về nhóm đó là tập hợp các số nguyên cùng với phép cộng; khi thực hiện cộng hai số nguyên bất kỳ luôn thu được một số nguyên khác. Hình thức trình bày trừu tượng dựa trên tiên đề nhóm, tách biệt nó khỏi bản chất cụ thể của bất kỳ nhóm đặc biệt nào và phép toán trên nhóm, cho phép nhóm bao trùm lên nhiều thực thể với nguồn gốc toán học rất khác nhau trong đại số trừu tượng và rộng hơn, và có thể giải quyết một cách linh hoạt, trong khi vẫn giữ lại khía cạnh cấu trúc căn bản của những thực thể ấy. Sự có mặt khắp nơi của nhóm trong nhiều lĩnh vực bên trong và ngoài toán học khiến chúng trở thành nguyên lý tổ chức trung tâm của toán học đương đại
 - Nhóm là một tập hợp, $G$, cùng với phép toán hai ngôi $*$ (còn gọi là luật nhóm của $G$) kết hợp hai phần tử $a$ và $b$ bất kỳ để tạo ra một phần tử khác, viết là $a * b$ hoặc $ab$.
## Tính chất
#### Tính đóng
 - $\forall a, \ b \in G, \ (a * b) \in G$
#### Tính kết hợp
 - $\forall a \ ,b \ , \ c \in G, \ (a \ * \ b) \ * \ c \ = \ a \ * \ (b \ * \ c)$
#### Phần tử đơn vị
 - Tồn tại một phần tử $e$ trong nhóm $G$ sao cho: $$\forall a \in G, \ a \ * \ e \ = \ e \ * \ a \ = \ a$$
#### Phần tử nghịch đảo
 - $\forall a \in G$ tồn tại một phần tử $a^{-1}$ (phần tử nghịch đảo của a) trong nhóm $G$ sao cho: $$a*a^{-1}=a^{-1}*a =e$$
#### Tính giao hoán
 - Nếu * có tính giao hoán $\forall a \in G$, $a * b = b * a$ thì nhóm $G$ sẽ được gọi là nhóm giao hoán hay nhóm $Abel$
## Order của một nhóm
- Nhóm hữu hạn là một nhóm có số phần tử hữu hạn
 - Xét một nhóm hữu hạn $G$
     - Bậc của nhóm là số phần tử của nhóm đó, kí hiệu $|G|$
     - Bậc của một phần tử $a \in G$ là số nguyên dương m nhỏ nhất sao cho $a^m = e$ với e là phần tử đơn vị của nhóm, kí hiệu $|a|$
 - Kí hiệu $\langle a \rangle$ là nhóm con sinh bởi $a \in G$, $\langle a \rangle = \{a^k \vert k \in \mathbb{Z}\}$. Ta có tính chất
     - $\lvert \langle a \rangle \rvert = \lvert a \rvert$ ( Bậc của nhóm con sinh bởi a bằng bậc của a)
### Định ý Lagrange
 - Nếu $H$ là nhóm con của $G$ thì $|H|$ là ước của $|G|$
 - Hệ quả:
     - $\vert G \vert \vdots \vert a \vert$, $\forall a \in G$
     - $a^{p-1} = 1 \pmod p$ với p nguyên tố và $a \ne 0$. Xét nhóm nhân $\mathbb{Z}_p^* = \{\bar{1}, \bar{2}, \dots, \overline{p-1}\}$  ta có $\vert \mathbb{Z}_p^* \vert = p - 1$. Mặt khác $\vert \mathbb{Z}_p^* \vert \vdots \vert a \vert$ nên $a^{p-1} = a^{k\vert a \vert} = 1 \pmod p$(do $a^{\vert a \vert} = 1 \pmod p$)
### Định lý Cauchy
 - Cho nhóm $G$ hữu hạn. Nếu cấp của $G$ chia hết cho $p$, và $p$ là số nguyên tố, thì tồn tại ít nhất một phần tử $a$ thuộc $G$ có cấp bằng $p$.
### Vành (Ring)
 - Xét tập hợp R với 2 phép toán $+$ và $*$, R được gọi là một vành nếu ta có các tính chất sau:
     - Cộng và nhân có tính đóng
     - Cộng và nhân có tính kết hợp: $\forall a, b, c \in R, (a + b) + c = a + (b + c), (a * b) * c = a * (b * c)$
     - Tồn tại phần tử đơn vị cho phép cộng và nhân, ta kí hiệu 0 và 1 lần lượt là phần tử đơn vị của phép cộng và nhân: $\forall a \in R, a + 0 = 0 + a = a, a * 1 = 1 * a = a$
     - Phép cộng có tính giao hoán : $a + b = b + a, \forall a, b \in R$
     - Tồn tại phần tử nghịch đảo cho phép cộng, $\forall a \in R$ , $\exists b \in R$, $a + b = 0$
     - Tính phân phối của phép nhân đối với phép cộng: $a * (b + c) = (a * b) + (a * b)$, $(b + c) * a = (b * a) + (c * a)$
 - Ví dụ: Tập hợp các ma trận vuông 2x2 trên tập số thực là một vành. Nếu R là một vành thì tập hợp các ma trận nxn với mỗi phần tử ma trận $\in R$ , kí hiệu $M_n(R)$ , cũng là một vành
### Trường (Field)
 - Xét tập hợp F với 2 phép toán $+$ và $*$. F được gọi là một trường nếu nó thỏa các tính chất sau:
     - Cộng và nhân có tính đóng
     - Cộng và nhân có tính giao hoán
     - Tồn tại phần tử đơn vị cho cộng và nhân, kí hiệu lần lượt là 0 và 1.
     - Tồn tại phần tử nghịch đảo $-a$, $\forall a \in F$ thỏa $a +(-a)=0$
     - $\forall a \ne 0$, $\exists a^{-1}$ thỏa mãn $a*a^{-1} = 1$
     - Tính phân phối của phép nhân đối với phép cộng: $a * (b+c)=(a * b)+(a * c)$
 - Trường hữu hạn là một trường có số phần tử là hữu hạn. Một trường hữu hạn thường gặp là $Z_p$ với p là số nguyên tố
# Diffie-Hellman
 - Để giải quyết các bài trong phần này, mình sẽ giải thích tóm tắt về trao đổi khóa Diffie-Hellman.
 - Các bạn có thể đọc [Diffie-Hellman](https://vi.wikipedia.org/wiki/Trao_%C4%91%E1%BB%95i_kh%C3%B3a_Diffie-Hellman) hoặc xem [ATTT-Diffie-Hellman](https://youtu.be/D08EMFzGSfA?si=CYO8rGf6Egup6g-z) để hiểu rõ và lấy cảm hứng để giải những challenges trong phần này.
![image](https://github.com/luongdv35/Group_in_Cryptography/assets/127461439/a2b90d7c-66e1-4aef-b2bc-1c96062568d4)
 - $Alice$ và $Bob$ thỏa thuận sử dụng chung một nhóm cyclic hữu hạn $G$ và một phần tử sinh $g$ của $G$. Phần tử sinh $g$ công khai với tất cả mọi người, kể cả kẻ tấn công. Giả sử nhóm $G$ là nhóm nhân.
     - Đầu tiên $Alice$ chọn ngẫu nhiên một số tự nhiên $a$ và gửi $g^a \ mod \ p$ cho $Bob$
     - Tiếp đó $Bob$ chọn ngẫu nhiên một số tự nhiên $b$ và gửi $g^b \ mod \ p$ cho $Alice$
     - Trên cơ sở đó $Alice$ tính $(g^a)^b \ mod \ p = g^{ab} \ mod \ p$
     - $Bob$ tính $(g^b)^a \ mod \ p  = g^{ab} \ mod \ p$
     - Khi đó $(g^b)^a = (g^a)^b$ vì nhóm $G$ có tính kết hợp, $Alice$ và $Bob$ tính được giá trị $g^{ab}$ và sử dụng nó cho khóa bí mật chung.
     - Các giá trị $a, b, g^{ab} = g^{ba} \ mod \ p$ được giữ bí mật, các giá trị $p, \ g, \ g^a \ mod \ p , \ g^b \ mod \ p$ được truyền công khai.
     - $Alice$ và $Bob$ tính được bí mật chung, cả hai có thể sử dụng nó làm khóa mã hóa chung chỉ có hai người biết để gửi dữ liệu trên kênh truyền thông mở.
