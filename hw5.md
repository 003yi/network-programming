###### tags: `網路程式設計`
# 網路程式作業 HW5
## 1. 連至2個Server
* 請寫一個client程式，分別連至Simple TCP/IP Service的Discard與CharGen Server。
* 該Client會將所有接收來自CharGen Server的封包，都送往Discard Server。
* 請持續印出每秒送往Discard Server的Byte數量。
![](https://i.imgur.com/11Fp0Ts.png)



```c=
#include <stdio.h>
#include <bits/stdc++.h>
#include <string.h>
#include <stdlib.h>
#include <winsock.h>
#include <time.h>

#define MAXLINE 1024    /* 字串緩衝區長度 */
using namespace std;
int main()
{
    SOCKET        sd,sd1;     /* socket 描述子 */
    struct sockaddr_in    serv;
    char  str[MAXLINE];
    int   n;
    int total;
    int port;
    WSADATA wsadata;
    time_t start,end;
    /*
    * 呼叫 WSAStartup() 註冊 WinSock DLL 的使用
    */

    if (WSAStartup(0x101,(LPWSADATA) &wsadata) != 0) //代表有問題
    {
        fprintf(stderr,"echo_srv: WSAStartup() fails!");
        exit(1);
    }

    /*printf("Echo server IP: ");
    fgets(str, MAXLINE, stdin);//把IP存到str裡
    printf("Echo server port: ");
    scanf("%d",&port);//取得port*/
    strcpy(str,"127.0.0.1");
    port = 9; //discard port
    /*
    * 填寫 sockaddr_in 結構 (serv) 。
    * 內容包括：server 的 IP 位址，port number 等等。
    */

    serv.sin_family       = AF_INET;
    serv.sin_addr.s_addr  = inet_addr(str);
    serv.sin_port         = htons(port);

    /*
    * 開啟一個 TCP socket.
    */

    if ( (sd=socket(AF_INET, SOCK_STREAM, 0)) == SOCKET_ERROR) //連線
    {
        fprintf(stderr,"echo_cli: can't open tcp socket\n");
        exit(1);
    }

    /*
    * 連接至 echo server
    */

    if ( connect(sd, (LPSOCKADDR) &serv, sizeof(serv))== SOCKET_ERROR)
    {
        fprintf(stderr, "echo_cli: can't connect to echo server\n");
        exit(1);
    }
//======================================================================
    strcpy(str,"127.0.0.1");
    port = 19; //chargen port
    /*
    * 填寫 sockaddr_in 結構 (serv) 。
    * 內容包括：server 的 IP 位址，port number 等等。
    */

    serv.sin_family       = AF_INET;
    serv.sin_addr.s_addr  = inet_addr(str);
    serv.sin_port         = htons(port);

    /*
    * 開啟一個 TCP socket.
    */

    if ( (sd1=socket(AF_INET, SOCK_STREAM, 0)) == SOCKET_ERROR) //連線
    {
        fprintf(stderr,"echo_cli: can't open tcp socket\n");
        exit(1);
    }

    /*
    * 連接至 echo server
    */

    if ( connect(sd1, (LPSOCKADDR) &serv, sizeof(serv))== SOCKET_ERROR)
    {
        fprintf(stderr, "echo_cli: can't connect to echo server\n");
        exit(1);
    }
//======================================================================
    start =time(NULL);//開始時間
    while(true)
    {
        if ( (n=recv(sd1, str, MAXLINE, 0))==0)
        {
          fprintf(stderr,"echo_cli: connection closed\n");
          break;
        }
        else if (n==SOCKET_ERROR)
        {
          fprintf(stderr,"echo_cli: recv() error!\n");
          break;
        }
        n = send(sd, str, strlen(str)+1,0);
        if (n==SOCKET_ERROR) //傳輸過程可能出現問題
        {
          fprintf(stderr,"echo_cli: recv() error!\n");
          break;
        }
        //printf("Byte/sec=%d",n);
        total += n;
        end =time(NULL);//結束時間
        if (difftime(end,start) > 1.0)
        {
            printf("\n==============================\n");
            printf("send Byte/sec: %d \n",total);
            total  = 0;
            start = time(NULL);
        }

        //else{send(sd, str, strlen(str)+1;}
        //else printf(str);

    }
    /*
    * 結束 WinSock DLL 的使用
    */
    closesocket(sd);
    WSACleanup();
    return 0;
}



```
## 2. 中繼程式轉送
* 請寫一個中繼程式，模仿代理伺服器( proxy server)提供以下兩個服務
* (1) datetime服務: 當收到從client傳來字串「time」時，會去詢問Simple TCPIP datetime server的時間，再傳送給client
* (2) ip查詢dns服務：當收到從client傳來字串「ask://ip位址」時，中繼程式會呼叫gethostbyaddr()查詢後，再將該ip的domain name回覆給client。
例如client送出字串：ask://8.8.8.8 ，中繼程式會回覆，而client會收到並印出 dns.google
![](https://i.imgur.com/mgwfXfK.png)
```c=
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <winsock.h>
#include <string>
#include <bits/stdc++.h>
#define MAXLINE 1024    /* 字串緩衝區長度 */
using namespace std;
int main()
{
  SOCKET        serv_sd, cli_sd,sd;//ad->中繼連到dest的server /* socket 描述子 */
  struct sockaddr_in    serv, cli,dest;
  int   cli_len, n,port;
  char  str[MAXLINE];
  string s= "";
  char ipaddr[] = {"8.8.8.8"};
  char ipaddr1[] = {"140.130.41.11"};
  WSADATA wsadata;

  /*
   * 呼叫 WSAStartup() 註冊 WinSock DLL 的使用
   */

  if (WSAStartup(0x101,(LPWSADATA) &wsadata) != 0) //代表有問題
  {
    fprintf(stderr,"echo_srv: can't use WinSock DLL\n");
    exit(1);
  }
// add here
// connect to dest
  strcpy(str,"127.0.0.1");
  port = 13;

  dest.sin_family       = AF_INET;
  dest.sin_addr.s_addr  = inet_addr(str);
  dest.sin_port         = htons(port);

  /*
   * 開啟一個 TCP socket.
   */

  if ( (sd=socket(AF_INET, SOCK_STREAM, 0)) == SOCKET_ERROR) {
    fprintf(stderr,"echo_cli: can't open tcp socket\n");
    exit(1);
  }

  /*
   * 連接至 echo server
   */

  if ( connect(sd, (LPSOCKADDR) &dest, sizeof(dest))== SOCKET_ERROR) {
    fprintf(stderr, "echo_cli: can't connect to echo server\n");
    exit(1);
  }


  /*
   * 開啟 TCP socket
   */

  if ( (serv_sd=socket(AF_INET, SOCK_STREAM, 0)) == SOCKET_ERROR) {
    fprintf(stderr,"echo_srv: can't open TCP socket\n");
    exit(1);
  }

  /*
   * 指定 socket 的 IP 位址和 port number
   */

   serv.sin_family      = AF_INET;
   serv.sin_addr.s_addr = 0;
   //serv.sin_port        = htons(IPPORT_ECHO);
   serv.sin_port        = htons(7777);
   // 指定 IPPORT_ECHO 為 echo port

   if (bind(serv_sd, (LPSOCKADDR) &serv, sizeof(serv)) <0) {
     fprintf(stderr, "echo_srv: can't bind local address\n");
     exit(1);
   }


   /*
    * 呼叫 listen() 使 socket 進入「監聽」狀態，並指定
    * 最多可同時接受的連接要求（在佇列中）
   */

   if ( listen(serv_sd, 5) < 0) //最多五個
   {
     fprintf(stderr,"echo_srv: listen() error\n");
     exit(1);
   }


   /*
    * 等待 clinet 程式的連接。
    * 注意！我們只允許一個 client 程式的連接
    */

    cli_len=sizeof(cli);
    while (1) {
      printf("echo_srv: waiting for client\n");
      if ( (cli_sd=accept(serv_sd, (struct sockaddr *)&cli, &cli_len)) == SOCKET_ERROR )
        {
            fprintf(stderr, "echo_srv: accpet() error\n");
            closesocket(cli_sd);
        }
      else {
        while (1)
        {

            if ( (n=recv(cli_sd, str, MAXLINE, 0))==0)
            {
                fprintf(stderr, "echo_srv: connection closed\n");
                break;
            }
            else if (n==SOCKET_ERROR)
            {
                fprintf(stderr, "echo_srv: recv() error!\n");
                break;
            }
            str[n]='\0';
            printf("echo_srv from client: %s\n",str);
            s+=str;
            if(s == "t")
            {
                   // 顯示從 client 傳來的字串
                //add here 2
                //send to dest(轉送)
                /*if ( send(sd, str, strlen(str), 0) == SOCKET_ERROR) {
                fprintf(stderr, "echo_srv: connection closed\n");
                break;
                }*/
                //recv from dest
                if ( (n=recv(sd, str, MAXLINE, 0))==0)
                {
                    fprintf(stderr, "echo_srv: connection closed\n");
                    break;}
                else if (n==SOCKET_ERROR)
                {
                    fprintf(stderr, "echo_srv: recv() error!\n");
                    break;
                }
                str[n]='\0';
                printf("echo_srv from dest: %s\n",str);   // 顯示從 client 傳來的字串

                //add complete
                if ( send(cli_sd, str, strlen(str), 0) == SOCKET_ERROR)
                {
                    fprintf(stderr, "echo_srv: connection closed\n");
                    break;
                }
                s="";
            }
            else if(s == "b")
            {
                //gethostbyaddr()
                    //cout << "What is" << ipaddr << "'s domain name?" << " " << endl;
                LPHOSTENT hp;

                struct in_addr sAddr;

                sAddr.s_addr=inet_addr(ipaddr1);

                hp=gethostbyaddr((LPSTR) &sAddr, sizeof(sAddr),AF_INET);
                if ( send(cli_sd, hp->h_name, strlen(hp->h_name), 0) == SOCKET_ERROR)
                {
                    fprintf(stderr, "echo_srv: connection closed\n");
                    break;
                }
                if(hp->h_name != nullptr)
                {
                    cout << "host name:" << " " << hp->h_name << endl;
                }
                s="";
            }
            else if(s == "a")
            {
                //gethostbyaddr()
                    //cout << "What is" << ipaddr << "'s domain name?" << " " << endl;
                LPHOSTENT hp;

                struct in_addr sAddr;

                sAddr.s_addr=inet_addr(ipaddr);

                hp=gethostbyaddr((LPSTR) &sAddr, sizeof(sAddr),AF_INET);
                if ( send(cli_sd, hp->h_name, strlen(hp->h_name), 0) == SOCKET_ERROR)
                {
                    fprintf(stderr, "echo_srv: connection closed\n");
                    break;
                }
                if(hp->h_name != nullptr)
                {
                    cout << "host name:" << " " << hp->h_name << endl;
                }
                s="";
            }
          }
    }
    }

   /*
    * 結束 WinSock DLL 的使用
    */
   closesocket(serv_sd);
   closesocket(cli_sd);
   closesocket(sd);
   WSACleanup();

   return 0;
}
```
## 3. 本週心得
這星期比較像是統合之前教的觀念，昨天看完有點覺得有點矇，今天寫完確實還是有點不懂，好像都被搞混了XD不知道誰是誰，希望之後能多更了解程式在做甚麼，這樣對以後要寫的程式也比較方便。
