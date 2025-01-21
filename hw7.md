###### tags: `網路程式設計`
# 網路程式作業 HW7
* client/server都只要建立一個socket。
* send()改成 sendto()，比TCP的send()多了對方的IP位址、IP長度。因為沒有用accept()或connect()建立連線，所以要先設定對方IP/port後才能送出。
* UDP用recvfrom()接收前，沒有accept()，收到之後才知道傳送端IP與port。
## 練習1:以UDP來一送一收
* 發現若沒有接收端(server)只有傳送端(client)，依然可以正常的傳送及結束，不會發現任何的異狀。如果是TCP就會發生錯誤。
![](https://i.imgur.com/6sIqa3a.png)
直接把封包送出，對方收不收的到並不知道
![](https://i.imgur.com/RQLIOE4.png)
## 練習2:以UDP來一送二收
* 使用UDP，傳送端只需要1個socket，如果是使用TCP則需要用兩個socket。
![](https://i.imgur.com/0iE3kB4.png)
## 練習3:以UDP來二送一收
* 使用UDP，傳送端只需要1個socket，如果是使用TCP則需要用兩個socket。
![](https://i.imgur.com/mbc1hky.png)
## 練習4:UDP實作變化：當傳送對象是固定對象時
* 可以用connect()來固定送出的對象，之後就可用sendto()指定對象, 也可用send()不指定對象。
Server (recviver) 同練習3
![](https://i.imgur.com/i0dCOgK.png)
## 練習5:UDP實作變化：當傳送端想用固定port時
* 傳送端可用bind()來指定傳送port(似乎不太穩定)
Server (recviver) 同練習3
![](https://i.imgur.com/nJ0oBVQ.png)
## 練習6:UDP實作變化：當傳接送端不關心傳送來源時
* 也可用recv()，就不用管傳送來源時
Client (sender) 同練習5
![](https://i.imgur.com/YDf9ui7.png)
## 心得
UDP雖然比TCP更簡單使用，不用建立也不用關閉連線，一個sd可以同時與多個Sd收送，但同樣的它也會造成一些問題，像是沒有錯誤偵測、流量控制、封包一旦送出就一無所知了。

