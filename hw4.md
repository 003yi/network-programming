###### tags: `網路程式設計`
# 網路程式作業 HW4
## 1. 使用wireshark抓python echo client連至 python echo server過程中所有的封包，並統計總共的封包數。
![](https://i.imgur.com/1DE3foQ.png)
總共有27個封包，包括連線的三個、結束的四個、還有hello 1~5各自的四個封包。
## 2. 實作Simple TCP/IP Server
* 丟棄服務 (Discard)
![](https://i.imgur.com/1Gl5q8P.png)
* 就是不用寫出send，因為這樣server端就不會回傳給client。
* 還有多加一個while判斷輸入符號第一個字元如果是'.'就結束連線，等待下一個client。
```cpp=
// simple TCP echo server

#include <stdio.h>
#include <winsock.h>

int main(){
	SOCKET sd,clnt_sd;//sd是原本的監聽socket,後者為client的socket
	WSADATA wsadata;
	struct sockaddr_in serv,clnt;//要得知server和client(連線)的位址
	int i,n;
	char str[100];//收到的內容

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);//建立監聽socket(IPV4,TCP服務)
	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);//建議用7000以上
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sd, (struct sockaddr *) &serv, sizeof(serv));//得知port有沒有被使用
    while(1)
    {
        listen(sd,5);//監聽等待,數字表示允許幾個封包等待
        int clnt_len=sizeof(clnt);//client的長度
        printf("Server waits.");
        clnt_sd =  accept(sd, (struct sockaddr *)  &clnt,&clnt_len );//當有new clinet進入就會接收(在這邊持續等待)
        while(1)
        {
            n = recv(clnt_sd,str, 100, 0);//連線到的client,收到的字元,上限大小,封包大小
            if(clnt_len > 0 && str[0]=='.')
            {
                break;
            }
            str[n]='\0';//加上字串結束符號
            printf("Server recveive:%s\n",str);
            //send(clnt_sd,str, strlen(str)+1,0);//把收到的內容送回去
        }
        closesocket(clnt_sd);//關閉連線,把socket歸回
    }


	closesocket(sd);//關閉連線,把socket歸回
	WSACleanup();

}
```
* 日期時間服務 (Daytime)
![](https://i.imgur.com/vPjqd17.png)
* 加上老師提供的程式碼，然後把send裡的str改成asctime(timeinfo)。
```cpp=
// simple TCP echo server

#include <stdio.h>
#include <winsock.h>
#include "time.h"

int main(){


	SOCKET sd,clnt_sd;//sd是原本的監聽socket,後者為client的socket
	WSADATA wsadata;
	struct sockaddr_in serv,clnt;//要得知server和client(連線)的位址
	int i,n;
	char str[100];//收到的內容

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);//建立監聽socket(IPV4,TCP服務)
	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);//建議用7000以上
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sd, (struct sockaddr *) &serv, sizeof(serv));//得知port有沒有被使用
    while(1)
    {
        listen(sd,5);//監聽等待,數字表示允許幾個封包等待
        int clnt_len=sizeof(clnt);//client的長度
            printf("Server waits.");
            clnt_sd =  accept(sd, (struct sockaddr *)  &clnt,&clnt_len );//當有new clinet進入就會接收(在這邊持續等待)
        while(1)
        {
            time_t rawtime;
            struct tm * timeinfo;
            time (&rawtime);
            timeinfo = localtime(&rawtime);
            printf("\n Date/time is: %s",asctime(timeinfo));
            send(clnt_sd,asctime(timeinfo), strlen(asctime(timeinfo))+1,0);//把收到的內容送回去
            break;

        }
        closesocket(clnt_sd);//關閉連線,把socket歸回
    }

	closesocket(sd);//關閉連線,把socket歸回

	WSACleanup();

}

```
* 每日一句 (QUOTE)
![](https://i.imgur.com/myfgpKv.png)
* 我是採用亂數取餘數的方法，去讓他隨機出現一個值，我把精句設在一個char的二維陣列裡面，所以最後就會傳回那一列的字，
```cpp=
// simple TCP echo server

#include <stdio.h>
#include <winsock.h>
#include <stdlib.h> /* 亂數相關函數 */

int main(){
	SOCKET sd,clnt_sd;//sd是原本的監聽socket,後者為client的socket
	WSADATA wsadata;
	struct sockaddr_in serv,clnt;//要得知server和client(連線)的位址
	int i,n,x;
	srand(time(NULL));

	char str[20][60]={{"你是我的最初，也是我的最後。"},{"每天早上醒來，看到你和陽光都在，這就是我想要的未來。"},{"一個人不可能讓所有人都滿意。"},{"說好了不動情，我卻動了心。"},{"等待是一種痛。忘掉也是一種痛。"},{"如果你無法忘掉昨天，就不會有一個更好的明天。"}};//收到的內容

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);//建立監聽socket(IPV4,TCP服務)
	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);//建議用7000以上
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sd, (struct sockaddr *) &serv, sizeof(serv));//得知port有沒有被使用
    while(1)
    {
        x = rand()%6;
        listen(sd,5);//監聽等待,數字表示允許幾個封包等待
        int clnt_len=sizeof(clnt);//client的長度
        printf("Server waits.");
        clnt_sd =  accept(sd, (struct sockaddr *)  &clnt,&clnt_len );//當有new clinet進入就會接收(在這邊持續等待)
        printf("Server recveive:%s\n",str[x]);
        send(clnt_sd,str[x], strlen(str[x]),0);//把收到的內容送回去
        closesocket(clnt_sd);//關閉連線,把socket歸回
    }
	closesocket(sd);//關閉連線,把socket歸回

	WSACleanup();

}
```
* 字元產生器 (CHARGEN)
![](https://i.imgur.com/wKBZRTH.png)
* 就是server一直重複印出str，也一直回傳str給client。
```cpp=
// simple TCP echo server

#include <stdio.h>
#include <winsock.h>

int main(){
	SOCKET sd,clnt_sd;//sd是原本的監聽socket,後者為client的socket
	WSADATA wsadata;
	struct sockaddr_in serv,clnt;//要得知server和client(連線)的位址
	int i,n;
	char str[100]={"“!#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[]^_`abcdefg"};//收到的內容

	WSAStartup(0x101,&wsadata);
	sd = socket(AF_INET, SOCK_STREAM, 0);//建立監聽socket(IPV4,TCP服務)
	serv.sin_family = AF_INET;
	serv.sin_port = htons(1234);//建議用7000以上
	serv.sin_addr.s_addr = inet_addr("127.0.0.1");

	bind(sd, (struct sockaddr *) &serv, sizeof(serv));//得知port有沒有被使用

	listen(sd,5);//監聽等待,數字表示允許幾個封包等待
	int clnt_len=sizeof(clnt);//client的長度
        printf("Server waits.");
        clnt_sd =  accept(sd, (struct sockaddr *)  &clnt,&clnt_len );//當有new clinet進入就會接收(在這邊持續等待)
	while(1)
    {
        //n = recv(clnt_sd,str, 100, 0);//連線到的client,收到的字元,上限大小,封包大小
        printf("Server recveive:%s\n",str);
        send(clnt_sd,str, strlen(str),0);//把收到的內容送回去

    }
	closesocket(sd);//關閉連線,把socket歸回
	closesocket(clnt_sd);//關閉連線,把socket歸回
	WSACleanup();

}
```
## 心得
這次的作業讓我更知道伺服端和客戶端的應用、也更加了解裡面的程式碼，第一次聽完的時候還沒有很懂，但我有重聽老師講解程式碼的地方，並且註解重點，這堂課並沒有我想的枯燥乏味，其實還蠻有趣的！
