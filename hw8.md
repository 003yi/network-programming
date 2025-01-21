###### tags: `網路程式設計`
# 網路程式作業 HW8
## 練習1：請寫一組程式來測試：
* 若兩方都先送後收，可以運作嗎？可以，雙方都會執行，可知送出的順序沒有差別。
![](https://i.imgur.com/AiaGI4r.png)
* 若兩方都先收後送，可以運作嗎？不可以，兩者都在等待接收，永遠都跑不到送出的階段。
## 練習2：
* 完成「送一次，收多次」的實驗
(藉由限制server一次只能接收6byte)
由此可知傳送接收都是透過buffer
![](https://i.imgur.com/w7IvDBc.png)
* 完成「送多次，收一次」的實驗
(一次送出5個封包，server端先暫緩1秒鐘(讓封包都到齊ㄌ在接收))
![](https://i.imgur.com/43Mo7Ms.png)

## 練習3:完成UDP成對收送的實驗
雖然client送出了五個封包，但server還是能一次完全接收。
![](https://i.imgur.com/F6FiiPd.png)
## 練習4:完成單次傳送文字檔的實驗
文字檔的完整性很重要，適合用TCP
只傳字元部分，結尾\0不傳。
strlen(str)，不加一。
接收若要印出，自行尾部加\0。
client只讀一次，送一次、server只收一次，。
![](https://i.imgur.com/kp3VIgl.png)
## 練習5:完成連續傳送大量文字檔的實驗
0d0a:在Windows上的換行符號，0a:在UNIX上的換行符號。
不同的系統對換行的的解釋不太一樣。
可處理較大文字檔。
client邊讀邊送、server邊收邊存。
當client send()兩次，server不一定也會成功recv()兩次，可能會有boundary problem。
![](https://i.imgur.com/NK1twJf.png)
## 練習6:完成連續傳送大量二位元檔的實驗
* 可處理影像檔
將MAXSIZE 設成30，就能看出連續傳送的過程。
![](https://i.imgur.com/fn0bQXl.png)
![](https://i.imgur.com/RbnBVZP.png)

## 練習7:請完成UDP傳檔的實驗
server不需要連線也不需要accept，所以sd只需一個。
不會關閉連線，不知道甚麼時候會結束。因此才需要改良
![](https://i.imgur.com/VJBXXeu.png)
![](https://i.imgur.com/7uceFq2.png)
## 練習8:請完成UDP傳檔改良的實驗
多設了counter計算傳送次數、sum計算封包數量。
1. 不知道傳送結束->用暗號告知
```cpp=
send(sd,"END",4,0);//client傳送END回去給server去比較封包是否傳完
```
2. 不知道有無遺失->傳送完畢，附上送出總量
```cpp=
n = recv(sd, buf, MAXSIZE, 0);
            if(sum != atoi(buf)){printf("OH...Pkt Number mismatched!!\n");}
            else{printf("Correct,Pkt Number matched!!\n");}
//比較server接師的封包數量和client傳送的封包樹是否相同
```
3. 不知道是否有人亂入->只收特別IP與port
利用recvfrom()可得知特定IP與port
![](https://i.imgur.com/XzHNjnP.png)
## 心得
這次的作業是傳送接收檔案作業，做完全部8個練習，確實也挺花時間的，但至少有懂程式在寫甚麼，收穫很多！
