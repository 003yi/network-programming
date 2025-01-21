###### tags: `網路程式設計`
# 網路程式作業 HW6
* 本週投影片提到的聊天室延伸功能有：
    - 三人交談
    - 不雅字馬掉 (Ex: FxxK )
    - 當有人說bye，結束此聊天室
    - 語句翻譯
    - client設定發言者名後，將來對話顯示發言者
    - 當有人說bye，結束此聊天室
    - 二間聊天室
### 我使用了三人交談、不雅字馬掉 (Ex: FxxK )、當有人說bye，結束此聊天室
![](https://i.imgur.com/YGl5wJs.png)
左邊分別是3個clients、中間則是3個viewers、最右邊則是伺服端。
最後說bye的時候是有回傳到viewer的，但來不及截圖就關閉了。
#### server
```cpp=
// simple TCP chatroom server
// v3: Not require take turn talking
#include <bits/stdc++.h>
#include <winsock.h>

using namespace std;

int main(){
	SOCKET sd,clnt_sd0,clnt_sd1,clnt_sd2,clnt_sd3;//sd0為每次建立連線的socket
	WSADATA wsadata;
	struct sockaddr_in serv,clnt0,clnt1,clnt2,clnt3;
	int i,n,pos=0,pos1=0;
	char str[100];
	string s;
	char str2[100];
	string s1="fuck",s2="fxxk",s3="bye";
	

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);
	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sd, (struct sockaddr *) &serv, sizeof(serv));

	listen(sd,5);
	int clnt_len1=sizeof(clnt1);
	int clnt_len2=sizeof(clnt2);
	int clnt_len3=sizeof(clnt3);
	printf("Server waits.\n");
	clnt_sd1 =  accept(sd, (struct sockaddr *)  &clnt1,&clnt_len1);
	printf("(1) 1 connection.\n");
	clnt_sd2 =  accept(sd, (struct sockaddr *)  &clnt2,&clnt_len2);
	printf("(2) 2 connections.\n");
	clnt_sd3 =  accept(sd, (struct sockaddr *)  &clnt3,&clnt_len3);
	printf("(3) 3 connections.\n");

	int clnt_len0=sizeof(clnt0);
	while(1){
		
		clnt_sd0 =  accept(sd, (struct sockaddr *)  &clnt0,&clnt_len0);
		n = recv(clnt_sd0,str, 100, 0);
		s+=str;
		if (n <= 0 ) break;
		closesocket(clnt_sd0);//收到資料就關閉s
		if(s.find( s3, pos1) != -1)//bye
		{
			send(clnt_sd1,str, strlen(str)+1,0);//送給sd1 
		send(clnt_sd2,str, strlen(str)+1,0);//送給sd2
		send(clnt_sd3,str, strlen(str)+1,0);//送給sd3
			printf("----對話已結束-----%s\n");
			system("pause");
		}
		while(pos!=-1)//fuck
		{
			pos = s.find( s1, pos);
			if(pos == -1){break;}
			s.replace(pos, s1.length(), s2);
	    	pos += s2.length(); ///換下一個位置, 準備下一次迴圈的 find()
		}
		stringstream num;
	    num << s;
	    num >> str2;
		
		printf("%s\n",str2);
		send(clnt_sd1,str2, strlen(str2)+1,0);//送給sd1 
		send(clnt_sd2,str2, strlen(str2)+1,0);//送給sd2
		send(clnt_sd3,str2, strlen(str2)+1,0);//送給sd3
		s.clear();//清除緩存
		pos1=0;
		pos=0;
	}
	closesocket(sd);
	closesocket(clnt_sd0);
	closesocket(clnt_sd1);
	closesocket(clnt_sd2);
	closesocket(clnt_sd3);
	WSACleanup();
}
```
#### client(三個都是一樣的)
```cpp=
// simple TCP chat client(1)
// v3: no take turn

#include <stdio.h>
#include <winsock.h>

int main(){
	SOCKET sd;
	WSADATA wsadata;
	struct sockaddr_in serv;
	int i,n;
	char str[100]="I love NP!";
	

	WSAStartup(0x101,&wsadata);


	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");
	while(1){
		char s[100]="client1:";
		sd = socket(AF_INET, SOCK_STREAM, 0);
		printf("Input1:");
		gets(str);//接收輸入文字
		strcat(s,str);
		connect(sd, (struct sockaddr *)  &serv,sizeof(serv) );//建立連線(先輸入再連線才不會卡住)
		send(sd,s,strlen(s)+1,0);//送出
		printf("Send1:%s\n",str); 
		
	}
	closesocket(sd);//關閉連線
	WSACleanup();
}
```
#### viewer(三個都一樣)
```cpp=
// simple TCP chat client viewwer
// v3: two client chat through server
//顯示->不斷接收
#include <stdio.h>
#include <winsock.h>

int main(){
	SOCKET sd;
	WSADATA wsadata;
	struct sockaddr_in serv;
	int i,n;
	char str[100]="I love NP!";

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);

	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	connect(sd, (struct sockaddr *)  &serv,sizeof(serv) );
	printf("Connect to server.\n");

	while(1){
		
		memset(str,0,100);
		n = recv(sd,str, 100, 0);//不斷接收
		if (n <=0) break;
		printf("%s\n",str);

	}
	closesocket(sd);
	WSACleanup();

}
```
## 心得
這星期的課程基本上是能理解的，只是在添加功能的過程中寫的程式有點問題，才導致一直沒寫對，基本的架構大致是對的！希望之後的作業能更得心應手！做得速度能更快一點，
