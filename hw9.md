###### tags: `網路程式設計`
# 網路程式作業 HW9
* 當socket設為**block mode(攔阻模式)**，API會等完成才回覆，才會導致程式有時候會卡住。
block mode的**優點**:流程簡單、容易debug、回覆就表示程式已經完成；**缺點**:無法進行其他動作、功能受限。
* 當socket設為**non-block mode(非攔阻模式)**，API會立即回覆，但是回覆並不代表就是完成，需要透過不斷的檢查(透過迴圈)才能確認完成。(程式比較有彈性)(TCP和UDP都可改成此模式)
* 改變socket模式->放置於須要改變模式之前 (recv() or accept())
```cpp=
u_long iMode=1;//非攔阻模式(當值為1的時候)
ioctlsocket(cli_sd,FIONBIO,&iMode);//透過呼叫改掉
```
iMode設為0，為攔阻模式
iMode設為1，為非攔阻模式
## 應用1
* 聊天室第1版中，雙方在對話時必須於一人一句。現在可以讓"一人多話，一人少話"。
* 由於client/server雙方都有接收，都須將socket改為非攔阻模式。
* 將非欄阻用在recv()部分。
![](https://i.imgur.com/8NYS1fJ.png)
## 應用2
* 聊天室第2版，可以解除一人一句的限制。當兩個client不規律地傳送server，server都可以及時轉送。
* 將非欄阻用在recv()部分。
* ![](https://i.imgur.com/mZXTKA3.png)
## 應用3
* 將非欄阻使用在accept()上面。
* server可**服務多個client**。當已有一個client連入並進行傳送中，另一個client中途連入也可以。
![](https://i.imgur.com/VG4TH1x.png)

## 心得
這星期介紹了非阻攔模式，程式不再只是一收一送，可以讓一方傳送比較多次，只要藉由clock去計時，就能打成此目的；也可以讓server服務多個client，和以往有些不同。
