###### tags: `網路程式設計`
# 網路程式作業 HW3
## 1. 使用wireshark抓封包
![](https://i.imgur.com/mg4U8EX.png)
從8~31都是TCP封包，有四個英文字母加上enter、開始、結束，而每一個字母都有四個封包，因為傳送出去和接收都匯回傳一個ACK，所以總共有31-4=27個封包。
(4~7是一開始就自己跳了一個enter)
## 2. 5種服務的示範程式
程式碼:
```python=
import socket
sendData = " I love NCYU CSIE"
HOST = '127.0.0.1'
while(True):
    print('請輸入服務的代號(1~5)已執行服務')
    print('1. 回應服務(Echo)')
    print('2. 丟棄服務(Discard)')
    print('3. 日期時間服務(Daytime)')
    print('4. 每日一句(QUOTE)')
    print('5. 字元產生器(CHARGEN)')

    num = int(input())
    if(num == 1):
        PORT = 7
    elif(num == 2):
        PORT = 9
    elif(num == 3):
        PORT = 13
    elif(num == 4):
        PORT = 17
    elif(num == 5):
        PORT = 19
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)#(建立socket->模組.API(IP,TCP協議))
    s.connect((HOST, PORT))
    print('執行結果:')
    if(num == 1):
        print('send: ' + sendData)
        s.send(sendData.encode())
        inData = s.recv(1024)
        print('recv: ' + inData.decode(errors='ignore'))
    elif(num == 2):
        print('send: ' + sendData)
        s.send(sendData.encode())
    else:
        inData = s.recv(1024)
        print('recv: ' + inData.decode(errors='ignore'))
    s.close()
print('close connection')

```
輸出畫面:
![](https://i.imgur.com/5nId2or.png)
![](https://i.imgur.com/y8iJxHI.png)




## 心得:
這禮拜介紹了telnet的四個用途，主要在介紹簡單的TCP/IP服務，包含了回應、丟棄、日期時間、每日一句、字元產生器。一開始操作的時候發現ctrl+]沒辦法使用，導致第一題的作業沒辦法關閉連線。第二題的程式用python寫起來確實輕鬆多，雖然老師有講解了C的程式，但說實在的我好像還是太懂，因為真的比較複雜一些。我覺得老師上課用影片的方式還不錯，因為有不懂的地方可以一再的重複，直到自己懂為止。
