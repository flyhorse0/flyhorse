查询特征

s盒初始化，256，%256

# RC4题目源码

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
int b[300];
int s[300];
char key[10]="tallmewhy";//(??) 
char data[1000]={
  0xF5, 0x8C, 0x8D, 0xE4, 0x9F, 0xA5, 0x28, 0x65, 0x30, 0xF4, 
  0xEB, 0xD3, 0x24, 0xA9, 0x91, 0x1A, 0x6F, 0xD4, 0x6A, 0xD7, 
  0x0B, 0x8D, 0xE8, 0xB8, 0x83, 0x4A, 0x5A, 0x6E, 0xBE, 0xCB, 
  0xF4, 0x4B, 0x99, 0xD6, 0xE6, 0x54, 0x7A, 0x4F, 0x50, 0x14,
  0xE5, 0xEC
};//(??)
int main(){
	int j,q,n;
	for(int i=0;i<256;i++){
		b[i]=key[i%9];  //8??Կ?ĳ??? ???䣩 
		s[i]=i;
	}
	for(int i=0;i<256;i++){
		j=(j+s[i]+b[i])%256;
		q=s[i];
		s[i]=s[j];
		s[j]=q^0x37;
	}
	for(int i=0;i<256;i++){
		printf("%d ",s[i]);
	}
	printf("\n"); 
	int i=0,t;
	j=0;
	for(int w=0;w<42;w++){  //32?data?ĳ??? ???䣩 
	
		i=(i+1)%256;
		j=(j+s[i])%256;
		
		q=s[i];
		s[i]=s[j];
		s[j]=q; //???? 
		
		t=(s[i]+s[j])%256;
		data[i-1]^=s[t];//s[t]?????Կ 
	}
	puts(data);
	return 0;
}
```
###  rc4-exp
```python

import base64
ct=[0xD8, 0xE5, 0x85, 0xBE, 0xE7, 0xF8, 0x58, 0x75, 0x95, 0x65,
  0x85, 0xE3, 0xA6, 0x47, 0x59, 0xB9, 0x14, 0x6F, 0x33, 0xB5,
  0xCA, 0x84, 0x0B, 0xE7, 0x92, 0x0E, 0xD2, 0xFD, 0x64, 0x18,
  0x96, 0xD0, 0x0F, 0x5E, 0x44, 0x3E]
for i in range( len(ct)):
	ct[i]=ct[i]^0x23
s = ""
for i in ct:
	s += chr(i)

def rc4_main(key="init_key",message="init_message"):
    s_box = rc4_init_sbox(key)
    crypt = rc4_excrypt(message, s_box)
    return crypt
def rc4_init_sbox(key):
    s_box = list(range(256))
    j= 0
    for i in range(256):
        j = (j + s_box[i] + ord(key[i % len(key)]))% 256
        s_box[i], s_box[j] = s_box[j], s_box[i]
    return s_box
def rc4_excrypt(plain,box):
    plain = base64.b64decode(plain.encode( 'utf-8'))
    plain = bytes.decode(plain)
    res = []
    i = j = 0
    for s in plain:
        i = (i +1)% 256
        j=(j + box[i]) % 256
        box[i],box[j] = box[j],box[i]
        t = (box[i] + box[j])% 256
        k = box[t]
        res.append(chr(ord(s)^ k))
    cipher = "".join(res)
    print(cipher)
    return cipher
s = str(base64.b64encode(s.encode( ' utf-8')),'utf-8 ')
rc4_main("thisiskkk",s)
```

### ere-exp.py
```python
import string
import base64

miwen = "ScDZC1cNDZaxnh/2eW1UdqaCiJ0ijRIExlvVEgP43rpxoxbYePBhpwHDPJ=="

base_table_1 = "ZYXWVUTSRQPONMLKJIHGFEDCBAabcdefghijklmnopqrstuvwxyz/+9876543210"
base64_table = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

print(base64.b64decode(miwen.translate(str.maketrans(base_table_1,base64_table))))

'''
def rc4_main(key = "init_key", message = "init_message"):
    # print("RC4解密主函数调用成功")
    s_box = rc4_init_sbox(key)
    crypt = rc4_excrypt(message, s_box)
    return crypt
def rc4_init_sbox(key):
    s_box = list(range(256))  # 我这里没管秘钥小于256的情况，小于256不断重复填充即可
    # print("原来的 s 盒：%s" % s_box)
    j = 0
    for i in range(256):
        j = (j + s_box[i] + ord(key[i % len(key)])) % 256
        s_box[i], s_box[j] = s_box[j], s_box[i]
    # print("混乱后的 s 盒：%s"% s_box)
    return s_box
def rc4_excrypt(plain, box):
    # print("调用解密程序成功。")
    plain = base64.b64decode(plain.encode('utf-8'))
    plain = bytes.decode(plain)
    res = []
    i = j = 0
    for s in plain:
        i = (i + 1) % 256
        j = (j + box[i]) % 256
        box[i], box[j] = box[j], box[i]
        t = (box[i] + box[j]) % 256
        k = box[t]
        res.append(chr(ord(s) ^ k))
    # print("res用于解密字符串，解密后是：%res" %res)
    cipher = "".join(res)
    # print("解密后的字符串是：%s" %cipher)
    # print("解密后的输出(没经过任何编码):")
    return  cipher
rc4_main("flag{123321321123badbeef012}", "HcWAX+cMWAaxnh09eD+FdqaXiQ/ijIRVxlvEVgK78rpxoxbBeKYhpwSWKQ")
'''
```