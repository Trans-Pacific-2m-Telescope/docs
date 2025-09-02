# 望遠鏡測試相關流程

## 指令手冊版本比較重點 (60 pages)
關於望遠鏡軟體的部份，我們跟 UNAM 討論過後，有提出新的要求以及關於使用手冊的內容需要釐清的部份。由於英文版手冊為 15年前 (2010) 的版本，日文手冊為去年 (2024) 的版本，內容我已經比對過。原本以為日文版應該是最完整的內容，但關於 Nasmyth 相關的指令以及狀態在日文版是缺失的，只在英文版中有提及。此外，指令、狀態、格式、數值範圍等在英文版與日文版有諸多衝突以及重複，我需要西村公司協助釐清。

由於文件是要給包括 UNAM 的人員使用，因此是英文版，如果需要翻譯成日文的話，請翻譯林先生協助處理，請西村公司確認正確性為何。

比較緊急或重要的要求是：
1. 望遠鏡伺服器的連線數，根據英文版，只允許四個連線，我們的控制軟體需要六個連線，希望能增加連線數到六。
2. 需要請西村公司提供能做 Pointing Model Map 的軟體，可以接受 Windows 軟體。
3. 雖然我們要跟 UNAM 共同開發控制軟體，仍希望能有一套 Windows 望遠鏡控制軟體做為可靠的備案（操作手冊上的 Master 軟體）。
4. 目前手冊內容沒有提及，但我們希望望遠鏡伺服器提供遠端 Power ON 的功能。
5. 目前手冊內容沒有提及，但我們希望望遠鏡伺服器提供溫度監測的資訊。

以上問題希望能在望遠鏡打包之前得到答覆，有的可能需要做功能上的更新。
接下來的問題只是需要釐清，因此可以晚點回覆。

完整內容請參考附件內容，一共有 60 頁，簡述如下。

第一部份 (p3-p5) 為整體問題，除了上述已經提到的比較緊急的 5 點以外，還有 20 點左右不緊急但是仍重要的問題，都在此一併提問。這些問題是發展自動化控制軟體很需要釐清的功能。

第二部份 (p6-p60) 為指令細節問題，頁數雖然很多但是要處理的問題並不困難。我逐項條列日文版以及英文版所有指令以及狀態碼，並在最後一個欄位 [Available] 設了 Check Box 要請西村公司一一勾選，確認哪個語言版本才是正確的，以及該指令或狀態碼是否依舊有效。
此外倒數第二個欄位為 [Note]，有一部分指令、狀態碼需要更精確的理解，因此要請西村公司幫忙釐清並回答。
這部份的問題同樣很重要，因為伺服器會將數個望遠鏡狀態以組合的形式呈現在一組 HEX 碼中，就算我們能正確解碼，如果我們無法識別狀態碼對應的望遠鏡狀態是哪一種，那我們等於無法與望遠鏡溝通。

由於時間緊急，因此先就以上的需求提出，UNAM 如果還有後續追加的問題，我會再更新文件內容。


## 日本西村工廠現場測試 (2025/09/03~05)

檢查過後發現不需要安裝 astropy 這個套件，因此這一步不需要做。
```
$ pip install astropy
```


每個測試檔案會自動紀錄結果到各自對應的 log 檔案中，請將 log 檔案一併帶回。
<!-- 
總共有三個測試檔案：
| 測試檔案名稱 | 測試類型 |log 檔案名稱 |
| --- | --- | --- |
| test_commands_batch.py | 指令測試（測試輸入格式） | test_commands_batch.log |
| test_commands_highfreq.py | 壓力測試（短時間內送大量指令） | test_commands_highfreq.log | -->

### 第一步：不管執行哪個測試程式，第一步都是先選擇望遠鏡伺服器 IP 與 Port

**1. 沒有望遠鏡伺服器的狀態下，需要模擬連線：**

需要在本機端打開**兩個**終端機視窗，其中一個終端機視窗 (Terminal) 執行模擬伺服器程式：
```
$ python fake_server_advanced.py
```
另一個終端機視窗 (Terminal) 執行望遠鏡控制程式：

```
$ python test_commands_query.py
```

會出現以下選單：
```
Select server to connect:
1. Fake server (127.0.0.1:8765)
2. Real server (192.168.11.111:8765)
3. Custom
Enter 1/2/3 (default 1): 
```
預設 1 會連線到本機端的模擬伺服器。

**2. 有望遠鏡伺服器的狀態下，直接連線到望遠鏡伺服器：**

只要在本機打開**一個**終端機視窗 (Terminal) 執行望遠鏡控制程式：

```
$ python test_commands_query.py
```
會出現以下選單：
```
Select server to connect:
1. Fake server (127.0.0.1:8765)
2. Real server (192.168.11.111:8765)
3. Custom
Enter 1/2/3 (default 1): 
```
望遠鏡伺服器的 IP 與 Port 預設為選項 2。如果望遠鏡伺服器的 IP 有變動，可以選擇 3 自訂 IP 與 Port。

### 第二步：選擇測試檔案

**1. Query 測試指令回應狀態**

只測試望遠鏡的指令以及參數的格式是否正確：

```
$ python test_commands_query.py
```
選擇完 伺服器 IP 與 Port 之後，會出現以下選單：

預設 1 為批次模式 (Batch mode)，有選單可選擇執行檔案。  
如果要自行輸入指令，請選擇 2 互動模式 (Interactive mode)。

```
Select mode:
1. Batch mode [default]
2. Interactive mode
Enter 1/2 (default 1): 
```
預設 1 為批次模式 (Batch mode)，會出現以下選單：
```
Select batch command file:
1. test_commands_A_short.script [default]
2. test_commands_A_long.script [default]
3. test_commands_telescope_dome_mirror.script
4. custom
Enter 1/2/3/4 (default 1): 
```



|選取 Script 檔|作用|使用時機|測試次數|
|-|-|-|-|
|test_commands_A_short.script|測試 A 指令 (望遠鏡狀態查詢) I |不論望遠鏡是否允許操作，只要允許伺服器連線，就測試伺服器對於 A 指令的反應。|5|
|test_commands_A_long.script|測試 A 指令 (望遠鏡狀態查詢) II |不論望遠鏡是否允許操作，只要允許伺服器連線，就測試伺服器對於 A 指令的反應。|1|
|test_commands_telescope_dome_mirror.script|測試望遠鏡、圓頂、次鏡的指令|當望遠鏡不允許操作，但允許與伺服器連線，則測試伺服器對於望遠鏡、圓頂、次鏡指令的反應。<br>如果望遠鏡不允許操作時，也不允許與伺服器連線測試指令，則這個檔案不測試。<br>若望遠鏡允許操作，則跳過這個檔案，直接進入下個測試流程。|5|

如果要自行輸入指令，請選擇 2 互動模式 (Interactive mode)。
```
Enter command to send (type 'exit' or 'quit' to leave interactive mode):

cmd>
```

**2. Control 測試控制望遠鏡**



如果允許操控望遠鏡移動，則執行測試控制望遠鏡的指令。

```
$ python test_commands_control.py
```
選擇完 伺服器 IP 與 Port 之後，會出現以下選單：

預設 1 為批次模式 (Batch mode)，有選單可選擇執行檔案。  
如果要自行輸入指令，請選擇 2 互動模式 (Interactive mode)。
```
Select mode:
1. Batch mode [default]
2. Interactive mode
Enter 1/2 (default 1): 
```
預設 1 為批次模式 (Batch mode)，會出現以下選單：
```
Select batch command file:
1. test_move_secondary.script [default]
2. test_move_telescope.script
3. test_move_dome.script
4. custom
Enter 1/2/3 (default 1): 
```

|選取 Script 檔|作用|使用時機|測試次數|
|-|-|-|-|
|test_move_secondary.script|測試次鏡的移動|當允許操作望遠鏡移動的時候才使用|5|
|test_move_telescope.script|測試望遠鏡的移動|當允許操作望遠鏡移動的時候才使用|5|
|test_move_dome.script|測試圓頂的移動|圓頂尚未蓋好，但若允許測試圓頂指令時才使用|5|


如果要自行輸入指令，請選擇 2 互動模式 (Interactive mode)。
```
Enter command to send (type 'exit' or 'quit' to leave interactive mode):

cmd>
```

<!--
**2. 壓力測試**

短時間內送大量指令，此為望遠鏡伺服器可容許最短時間。

```
$ python test_commands_highfreq.py
```

選擇完 伺服器 IP 與 Port 之後會出現 interval 選單，預設為 0.1 秒，請三個選單都測試：

```
Select interval:
1. 0.1 sec (100ms) [default]
2. 0.2 sec (200ms)
3. 0.05 sec (50ms)
Enter 1/2/3 (default 1): 
```
-->
