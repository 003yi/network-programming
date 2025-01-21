###### tags: `網路程式設計`
# 網路程式作業 HW2
## 1. 使用wireshark分別抓反查範例的DNS query與response封包
* DNS標頭截圖
![](https://i.imgur.com/BqNja5u.png)
* query封包
![](https://i.imgur.com/FSML4hZ.png)
* response封包
![](https://i.imgur.com/1H2UiwC.png)
## 2. 印出subnet的所有ip其domain name
```cpp=
#include <bits/stdc++.h>
#include <winsock.h>
using namespace std;

int main(){

char ipaddr[50];
//initial WSAStartup
WSADATA wsadata;
WSAStartup(0x101,(LPWSADATA) &wsadata);
 string s;
for(int i=1;i<=255;i++)
{
    s = "140.130.175." + to_string(i);
    stringstream num;
    num << s;
    num >> ipaddr;
//gethostbyaddr()
cout << "What is" << ipaddr << "'s domain name?" << " " << endl;
LPHOSTENT hp;

struct in_addr sAddr;

sAddr.s_addr=inet_addr(ipaddr);

hp=gethostbyaddr((LPSTR) &sAddr, sizeof(sAddr),AF_INET);
if(hp->h_name != nullptr)
{
    cout << "host name:" << " " << hp->h_name << endl;
}
}
return 0;
}
```
![](https://i.imgur.com/oA3JLPD.jpg)
.
.
.
![](https://i.imgur.com/SHnLFyh.jpg)

# 3. 本週心得
今天正式接觸到了一點程式-->DNS查詢程式，雖然說有一點難理解，但經過老師上課仔細的講解，也算是有初步的認識。上學期學了計網，沒有經過實作，都不太懂在說甚麼，但這學期修到這堂課，算有學以致用，加深以前學過的觀念！這門課不簡單，但我會加油的！




