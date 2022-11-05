# 原理
```python
# /usr/bin/python
# encoding: utf-8

base64_flag = 'mZOemISXmpOTkKCHkp6Rgv=='
base64_table = ['=','A', 'B', 'C', 'D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z',
                'a', 'b', 'c', 'd','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z',
                '0', '1', '2', '3','4','5','6','7','8','9',
                '+', '/'][::-1]

def encode_b64(s):
    l = len(s)
    i = 0
    result = ''
    while i < l:
        # 将字符转换为二进制编码，然后对齐
        s1 = s[i]
        b1 = bin(ord(s1))[2:]
        cb1 = b1.rjust(8, '0')

        i += 1
        if i >= l:
            cb2 = '00000000'
        else:
            s2 = s[i]
            b2 = bin(ord(s2))[2:]
            cb2 = b2.rjust(8, '0')

        i += 1
        if i >= l:
            cb3 = '00000000'
        else:
            s3 = s[i]
            b3 = bin(ord(s3))[2:]
            cb3 = b3.rjust(8, '0')

        # 将三字节转换为四字节
        cb = cb1 + cb2 + cb3

        rb1 = cb[:6]
        rb2 = cb[6:12]
        rb3 = cb[12:18]
        rb4 = cb[18:]

        # 转换后的编码转为十进制备用
        ri1 = int(rb1, 2)
        ri2 = int(rb2, 2)
        ri3 = int(rb3, 2)
        ri4 = int(rb4, 2)

        # 处理末尾为０的情况，以＇＝＇填充
        if i - 1 >= l and ri3 == 0:
            ri3 = -1

        if i >= l and ri4 == 0:
            ri4 = -1

        result += base64_table[ri1] + base64_table[ri2] + base64_table[ri3] + base64_table[ri4]

        i += 1

    return result

def decode_b64(s):
    l = len(s)
    i = l-1
    result = ''
    while i >= 0:
        s4 = s[i]
        s3 = s[i-1]
        s2 = s[i-2]
        s1 = s[i-3]

        ri4 = base64_table.index(s4)
        ri3 = base64_table.index(s3)
        ri2 = base64_table.index(s2)
        ri1 = base64_table.index(s1)

        if ri4 == len(base64_table)-1:
            ri4 = -1
        if ri3 == len(base64_table)-1:
            ri3 = -1

        rb4 = bin(ri4)[2:].rjust(6, '0')
        rb3 = bin(ri3)[2:].rjust(6, '0')
        rb2 = bin(ri2)[2:].rjust(6, '0')
        rb1 = bin(ri1)[2:].rjust(6, '0')

        cb = rb1 + rb2 + rb3 + rb4

        if ri4 == -1:
            cb = cb[:-8]
        if ri3 == -1:
            cb = cb[:-8]


        cb3 = cb[16:]
        cb2 = cb[8:16]
        cb1 = cb[:8]

        p3 = chr(int(cb3, 2)) if cb3 != '' else ''
        p2 = chr(int(cb2, 2)) if cb2 != '' else ''
        p1 = chr(int(cb1, 2)) if cb1 != '' else ''

        result = p1 + p2 + p3 + result

        i -= 4

    return result

```


# base64 原表

```python
base64_table = ['A', 'B', 'C', 'D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','a', 'b', 'c', 'd','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z', '0', '1', '2', '3','4','5','6','7','8','9', '+', '/','=']

print(len(base64_table))

flag = "flag{hello_world}"
l = len(flag) 

for i in range(l):
	if i%3 == 0:
		if i+3 < l:
			t1=bin(ord(flag[i]))[2:].rjust(8,'0')
			t2=bin(ord(flag[i]))[2:].rjust(8,'0')
			t3=bin(ord(flag[i]))[2:].rjust(8,'0')
			b=t1+t2+t3
			b1=b[:6]
			b2=b[6:12]
			b3=b[12:18]
			b4=b[18:24]

			b11 = int(b1,2)
			b12 = int(b2,2)
			b13 = int(b3,2)
			b14 = int(b4,2)

			b111=base64_table[b11]+base64_table[b12]+base64_table[b13]+base64_table[b14]
			print(b111)
		else:
			print (i)
			
	

'''

for i in flag:
	t = bin(int(ord(i)))[2:].rjust(8,'0')
'''
```

# python3 实现
```python
import  base64
Str = list("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/")
model = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
for i in range(6,15):
    Str[i],Str[i+10] = Str[i+10],Str[i]
Str = ''.join(Str)
enc = "zMXHz3TIgnxLxJhFAdtZn2fFk3lYCrtPC2l9".swapcase()
dec = ""
for i in range(len(enc)):
    dec += model[Str.find(enc[i])]
print (dec)
print (Str)
print (base64.b64decode(dec))
```

# base64变表
```python
import base64
import string

str1 = "x2dtJEOmyjacxDemx2eczT5cVS9fVUGvWTuZWjuexjRqy24rV29q"

string1 = "ZYXABCDEFGHIJKLMNOPQRSTUVWzyxabcdefghijklmnopqrstuvw0123456789+/"
string2 = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

print (base64.b64decode(str1.translate(str.maketrans(string1,string2))))
```

# base64变表1
```python
import base64
import string
#用新表编码后的字符串
flag = 'x2dtJEOmyjacxDemx2eczT5cVS9fVUGvWTuZWjuexjRqy24rV29q'
# 新的表
base_table_1 = 'ZYXABCDEFGHIJKLMNOPQRSTUVWzyxabcdefghijklmnopqrstuvw0123456789+/'
base64_table = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
t3 = ''
for i in range(len(flag)):
	t1 = flag[i]
	for j in range(len(base_table_1)):
		if base_table_1[j] == t1:
			t2 = base64_table[j]
			t3 += t2

print(base64.b64decode(t3))
print(base64.b64decode(flag.translate(str.maketrans(base_table_1,base64_table))))
```
# 例题1-base64变表
```python
import base64
import string

str1 = "YQxhbQ57ihau0yWuihSI0hTg5gss0BSwVySsjoeIioAA5hbBjolx0hqvdl=="

string1 = "1234OPQRSTUVWijklmnopXyz05YZabcdefghqABCDEFGHIJKLMNrstuvwx6789+/"
string2 = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

print(base64.b64decode(str1.translate(str.maketrans(string1,string2))))
```

