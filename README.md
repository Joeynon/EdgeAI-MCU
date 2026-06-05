# EdgeAI MCU 課程實作成果報告與應用設計

[cite_start]本專案記錄了基於 **Ameba Pro2 (AMB82-mini)** 開發板所進行的一系列 EdgeAI 與微處理機邊緣運算專案 [cite: 11][cite_start]。內容涵蓋 Web 伺服器控制、網頁前端 Vibe Coding、YOLOv7 影像辨識、硬體周邊（I2C/SPI）驅動及結合大語言模型（LLM）的物聯網視覺生成應用，並提出一項基於 EdgeAI MCU 的創新智慧硬體設計 。

* **專案 GitHub 連結**: `https://github.com/Joeynon/EdgeAI-MCU`
* **專案展示網頁 (GitHub Pages)**: `https://Joeynon.github.io/EdgeAI-MCU`

---

## 🛠 課程作業實作說明與成果展現

### (一) WebServer 雙按鈕 LED 控制 (`WebServer_ControlLEDx2`)
* **實作說明**:  
    [cite_start]將基礎的 `WebServer_ControlLED` 範例程式碼透過 AI 進行修改，在建立的 Wi-Fi 伺服器網頁前端擴充為兩個獨立的按鈕區塊，分別用來控制 AMB82-mini 上的藍色 LED（LED_B）與綠色 LED（LED_G）[cite: 13, 14]。當使用者操作智慧型手機連接至開發板的熱點並開啟網頁時，即可透過異步請求即時切換這兩顆 LED 的亮滅狀態。
* **成果展現**:  
    [cite_start]成功提交手機畫面與硬體互動之成果 [cite: 15]。從網頁上的控制按鈕被觸發時，AMB82-mini 上的藍色與綠色 LED 隨之進行正確的硬體狀態切換。

### (二) Vibe Coding 網頁動態應用
* **實作說明**:  
    利用大語言模型（Google AI Studio 或 ChatGPT）進行無程式碼/低程式碼的前端視覺設計（Vibe Coding），生成一個具備高質感互動介面的網頁應用程式 [cite: 18]。將此 `your_app.html` 放入 SD 卡中 [cite: 25]，並調用 AMB82-mini 的檔案系統範例 `READHTMLFile` [cite: 19, 22, 23]。當手機連線至開發板時，開發板即化身為獨立的 Web 伺服器，將 SD 卡中的 HTML 網頁推送給手機端瀏覽 [cite: 20]。
* **成果展現**:  
    成功上傳執行應用的手機截圖 [cite: 26]，手機端成功開啟深色主題、具備點擊與化學元素煉製互動特效的精美網頁介面。

### (三) YOLOv7 邊緣運算在地化監控 (`YOLOv7_Survellience`)
* **實作說明**:  
    [cite_start]執行 AMB82-mini 內建的 YOLOv7 深度學習神經網路模型 [cite: 32][cite_start]，用於即時偵測鏡頭前方的特定物體（如 person, bicycle, car, motorcycle, bus, truck） [cite: 28][cite_start]。本實驗結合了 `WebSocket Viewer` 提供即時影像串流 [cite: 29][cite_start]，並透過 `NTPClient` 同步網路時間來進行存檔命名 [cite: 30][cite_start]。實作中修改了原始 Sketch 的時間限制條件，以達到全天候錄影 [cite: 33]：
    ```cpp
    // 原始程式碼限制在半夜 0~6 點紀錄
    [cite_start]if(hour>=0 && hour<=6) // [cite: 34]
    
    // 修改為全天候不限時紀錄
    if(hour>=0) { ... } // [cite: 35, 36]
    ```
* **成果展現**:  
    上傳偵測的數張檔案照片 [cite: 37]，網頁影像串流（Video Streaming DEMO）成功捕捉到鏡頭前的人臉，並在四周標示出 YOLOv7 模型即時推論的邊界框（Bounding Box）。

### (四) Vibe Coding - 盲人友善視覺助理 (`Visual Assistant`)
* **實作說明**:  
    Fork 助教的視覺助理專案後 [cite: 39, 40, 41]，利用大語言模型修改 `index.html` 的 UI/UX 設計 [cite: 45]。為了符合盲人或弱視使用者的使用情境，作業要求簡化使用介面使其適合盲人使用 [cite: 44]。大幅度簡化介面，將按鈕面積最大化、加入高對比度的配色，並設計出極具辨識度的「拍照」與「辨識物品」兩大核心區塊。
* **成果展現**:  
    * **手機執行截圖**: 精簡後的網頁畫面中僅保留超大面積的藍色「拍照」與紫色「辨識物品」按鈕 [cite: 47]，並展示杯子的拍攝辨識。
    * **GitHub 代碼庫截圖**: 藉由 `Settings > Pages > Branch` 選擇 `Main` 產生可執行網頁 [cite: 42, 43]，HTML 原始碼已正確上傳至儲存庫中 [cite: 46]。

### (五) 紅外線測距與 TFT 螢幕即時顯示 (`IR ranger + TFT display`)
* **實作說明**:  
    本實驗練習硬體總線的進階切換。使用第二組 I2C1 總線（SDA1, SCL1）藉由在主程式中設定 `Wire1.begin()` [cite: 53, 72]，並修改 Realtek 核心套件庫中的 `VL53L0X.cpp` 底層驅動 [cite: 54, 55]：
    ```cpp
    // 將預設總線修改為使用 Wire1
    VL53L0X::VL53L0X(): bus(&Wire1), // [cite: 56, 57]
    ```
    搭配結合 `Continuous` 測距範例與 `LCD_Screen_ILI9341_TFT` 顯示範例 [cite: 50, 51, 60, 62]，將讀取到的距離數值即時換算為公分（cm）並格式化後渲染在螢幕上。
* **成果展現**:  
    硬體電路運作正常，上傳的照片清晰顯示出紅外線感測器與 TFT 螢幕的動態互動，成功顯示當前距離：`Distance: 8.2 cm` [cite: 74]。

### (六) MPU6050 六軸感測器姿態解算 (`MPU6050_DMP_GetHeading`)
* **實作說明**:  
    驅動 MPU6050 加速度計與陀螺儀晶片 [cite: 76]，並啟用其內部的數位運動處理器（DMP，Digital Motion Processor）進行硬體加速的姿態解算 [cite: 75]。運行 `MPU6050_DMP6_GetHeading.ino` 程式以獲取精準的航向角（Heading Angle） [cite: 77]，消除累積漂移 [cite: 78]。
* **成果展現**:  
    當硬體模組旋轉時，上傳的螢幕截圖顯示 Arduino 序列埠監控視窗（Serial Monitor）正即時輸出穩定變化的姿態角度數據 [cite: 79]。

### (七) 溫濕度 WebServer 監測系統 (`WebServer with DHT11`)
* **實作說明**:  
    融合 `DHT_Tester` 與 `ReceiveData` 範例 [cite: 81, 82, 83]，將 DHT11 溫濕度感測器實裝於 GPIO Pin 8 [cite: 82]。修改 `ReceiveData` 程式以讀取 DHT11 [cite: 84, 85]，AMB82-mini 定時讀取環境中的溫度與濕度數值，並將數據動態嵌入於 HTTP 回應的 HTML 代碼中，最後透過網頁端渲染出一個「DHT11 溫濕度監測系統」儀表板。
* **成果展現**:  
    智慧型手機瀏覽器開啟至 `172.20.10.2` [cite: 86]，成功顯示動態環境數據（例如：濕度：59.00%、溫度：28.10 °C / 82.58 °F、體感溫度：29.48 °C）。

### (八) 多媒體大語言模型應用 (`GenAIVisionTTS`)
* **實作說明**:  
    此實驗展示了邊緣端與雲端 AI 的多媒體深度整合 [cite: 87]。結合 `Camera_2_Lcd_JPEGDEC` 影像處理與 `GenAIVisionTTS` 模型推論 [cite: 89, 90]，本系統實作了**「AI情緒感知音樂播放器」**的功能 [cite: 98, 101]。系統拍攝人臉影像後上傳至視覺大語言模型，並搭配規格書提供的 Prompt 提示詞要求進行情緒分析與歌曲推薦 [cite: 99]：
    > *"Analyze the emotion (happy, angry, sad, or joyful) of the person in the image. Based on the detected emotion, recommend a suitable song filename... Respond in the exact format: 'Emotion: [emotion], Song: [filename.mp3]'..."* [cite: 99]
    大模型推論後回傳格式化結果，AMB82-mini 解析回應後，除了在 LCD 上顯示影像外，亦透過音訊腳位與外接喇叭播放 SD 卡中相對應的情緒主題曲 [cite: 99, 101]，並延伸支援文字轉語音（TTS）播報 [cite: 90, 101]。
* **成果展現**:  
    成功上傳成果影片與程式碼 [cite: 102]，實裝展示了 AMB82-mini 外接鏡頭、TFT 螢幕與揚聲器喇叭，完整展現拍照、多模態大模型視覺分析與音訊撥放之整合。

---

## 💡 EdgeAI MCU 創新應用設計：智慧庭園管家 (Smart Garden Butler)

[cite_start]針對 AMB82-mini 強大的邊緣推論能力與多媒體周邊，本專案規劃了一款具備主動感知與多模態互動能力的**智慧庭園管家**裝置 [cite: 11, 90]。

### 1. 硬體架構組成
* [cite_start]**核心處理器**: AMB82-mini (內建 Wi-Fi, BLE, NPU 邊緣運算加速器) [cite: 11]
* **視覺與多媒體模組**: 
    * [cite_start]內建高畫質相機鏡頭（用於植物狀態與病蟲害捕捉） [cite: 89]
    * [cite_start]ILI9341 TFT 顯示螢幕（即時顯示系統狀態與植物健康數據） [cite: 51]
    * [cite_start]外接揚聲器喇叭（用於語音反饋與警報播報） [cite: 90]
* **環境與硬體感測**:
    * [cite_start]DHT11 溫濕度感測器（監測庭園微氣候） [cite: 82]
    * 土壤水分/電導率感測器（經由 ADC 讀取，評估灌溉需求）
    * [cite_start]VL53L0X 紅外線測距感測器（用於偵測是否有動物或人員靠近植物） [cite: 50]
* **執行機構**: 微型水泵與繼電器模組（用於自動化精準灌溉）

### 2. 核心功能與應用場景
* **植物健康邊緣辨識**:  
    [cite_start]系統定時透過相機拍攝植物葉片，利用 AMB82-mini 的 NPU 執行輕量化分類模型，初步篩選植物是否有枯萎或異常。若發現異常，則將影像推送至大語言模型進行深度多模態分析（Visual LLM） [cite: 90][cite_start]，精確診斷出病蟲害類型，並透過揚聲器進行語音提醒 [cite: 90][cite_start]（例如：「主人，觀葉植物似乎感染了紅蜘蛛，建議進行葉面噴水清潔」），同時在 TFT 螢幕上顯示照顧方針 [cite: 51]。
* **主動式生態監控 (YOLOv7)**:  
    [cite_start]當植物管家放置於戶外時，利用內建的 YOLOv7 監控模型進行周遭環境守護 [cite: 32][cite_start]。若 VL53L0X 偵測到物體靠近 [cite: 50][cite_start]，且 YOLOv7 辨識出為 `bird` 或 `cat` 等可能破壞植栽的動物時（擴充預設類別） [cite: 28][cite_start]，硬體將主動透過揚聲器播放特定頻率的驅趕音效 [cite: 90][cite_start]，或透過 WebServer 傳送警報圖片至主人手機 [cite: 12]。
* **環境動態平衡灌溉**:  
    [cite_start]結合 DHT11 氣溫數據與土壤水分感測器 [cite: 82][cite_start]，系統會將微氣候狀態即時呈現在手機 Web 控制端 [cite: 80]。若泥土過於乾燥且微氣候顯示高溫，系統將自動啟動微型水泵進行澆灌，實現真正的智慧化無人精準全自動養護。

---

## 📈 學習心得

[cite_start]在這一系列的微處理機與邊緣 AI 實驗中，我深入體會到了嵌入式系統從傳統的「被動感測」走向「主動運算」的巨大變革。過去使用微控制器（MCU）多半只能做簡單的開關訊號讀取（如 DHT11 的數位訊號或簡單的 I2C 通訊） [cite: 50, 82][cite_start]；然而在 **AMB82-mini** 這個平台上 [cite: 11][cite_start]，我們不僅成功克服了底層硬體（如將 VL53L0X 的 I2C 總線重新導向至 `Wire1`）的配置難題 [cite: 53, 54][cite_start]，更能直接在微控制器上運行 YOLOv7 這種複雜的神經網路模型 [cite: 28, 32]。

[cite_start]特別令我驚豔的是 **Vibe Coding** 與**多模態雲端 AI（GenAI Vision）**的結合 [cite: 17, 90][cite_start]。透過大語言模型的輔助，身為開發者的我們能夠以極高的效率生成高質感的網頁介面 [cite: 18, 45][cite_start]，甚至讓硬體設備具備了「看圖說故事」與「理解人類情緒並推薦音樂」的思考能力 [cite: 97, 98, 99][cite_start]。微控制器的定位不再只是死板的硬體線路，而是成為一個能聽、能看、能說話且具備思考邏輯的 **AI Agentic Device（智能體裝置）** [cite: 1]。這些寶貴的實作經驗，不僅大幅提升了我對軟硬體整合、網絡協定與邊緣運算硬體底層的理解，也為我未來在開發更複雜的物聯網（IoT）與人工智慧邊緣系統時，奠定了無比紮實的基礎。