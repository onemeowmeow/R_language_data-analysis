# R_language_資料視覺化應用
### 1. 基本函數: 統計

- **mean() : 平均值**
    - **`mean(dat$目標欄位, na.rm = TRUE)`  #na.rm→排除空值**
- **sd()  : 標準差**
    - **`sd(dat$目標欄位, na.rm = TRUE)`**
- **var() :變異數**
    - **`var(dat$目標欄位, na.rm = TRUE)`**
- **median() : 中位數**
    - **`median(dat$目標欄位, na.rm = TRUE)`**
- **quantile() :百分位數**
    - **`quantile(dat$目標欄位, na.rm = TRUE)`**
    - **`quantile(dat$eGFR, 0.95, na.rm = TRUE)`  #第95%的百分位數**
- **min() & max() :最大最小值**
    - **`min(dat$eGFR, na.rm = TRUE)`**  **`min(dat$eGFR, na.rm = TRUE)`**
- **table() : 列聯表**
    - **`table(dat$因素1, dat$因素2)`**
- **prop.table() : 列聯表的百分比**
    
    ```r
    #全部 相加為100%
    tab2 = table(dat$Cancer, dat$Diabetes)
    prop.table(tab2)
    
    #row 相加為100%
    prop.table(tab2, 1)
    
    #col 相加100%
    prop.table(tab2, 2)
    ```
    
- **paste() : 標示平均數±標準差**

```r
#寫法 :　**paste(mean(資料), "±", sd(資料))**

**paste(mean(dat[dat[,"欄位"] == 1,]$eGFR, na.rm = TRUE), "±", sd(dat[dat[,"Disease"] == 1,]$eGFR, na.rm = TRUE), sep = "")
#sep="" 資料串接不要有間隔**
```

- **formatC() : 指定小數點位數**
    - **`formatC(m, digits = 想要保留的位數, format = "f")`**

### 2. 描述性統計

```r
#將Disease欄位所有病人為1的人的eGFR欄位抓出，不計入na值算平均
mean(dat[dat[,"Disease"] == 1,]$eGFR, na.rm = TRUE)
```

### 3. 線性迴歸

- **lm() :**

```r
lm(y ~ x, data)
#x項為
#data：代表資料框，資料框包含了回應變數和獨立變數
#~ 符號：代表預測，由X變項去預測Y變項

lm(dat[,"eGFR"] ~ ., data = dat[,c("SBP", "DBP"),drop=FALSE])
# drop=FALSE : 雖然只以SBP和DBP去做預測，但不要刪除其他資料內未使用到的變相，以利後續若需擴充變項
```

### 4. 預測模型

**被編碼成0、1以及0、1、2這樣的變項，放入線性迴歸中要把他更改編碼格式為one-hot encoding**

**model.matrix**

```r
x = c(0, 1, 2, 1, NA, 2)
coding_x = model.matrix(~as.factor(x))
coding_x
```

```r
coding_Income = one_hot_coding(dat[,"Income"]) #產生新欄位叫income 並 onehot_encoding
coding_Education = one_hot_coding(dat[,"Education"])
dat1 = cbind(dat, coding_Income, coding_Education)  #將新的併入舊的

#將第7、8、11~14欄位的資料 變成 one-hot-encoding 後去預測eGFR
lm(dat[,"eGFR"] ~ ., data = dat1[,c(7:8, 11:14)]) 
```

## 統計資料圖示

---

### 1. 基礎繪圖函數

**基本圖示**

- **hist() : 直方圖**
    - `hist(dat[,"目標欄位"],``col = "red")` : 以紅色顯示幕標欄位資料圖示
- **boxplot() : 盒鬚圖**
    - `boxplot(dat[,"目標欄位"])`
- **pie() : 圓餅圖** ( 需事先統計好資料才可以呈現)
    - `pie(**table**(dat[,"Education"]), col = c("blue", "red", "green"))`

**基本操作**

- **par() :** 指定繪圖環境。其中最常見的應用為把4張圖放在同一張畫布內
- **pdf() :** 把圖片存出去
    - **最後一定要用函數「dev.off()」關掉那個PDF檔案**
- **plot.new()、plot.window() :開一張新畫布**
- **lines() : 按照順序把幾個點連起來**
    
    ```r
    x = c(1, 4, 7)
    y = c(2, 9, 6)
    plot.new()
    plot.window(xlim = c(0, 10), ylim = c(0, 10))
    lines(x, y)
    
    ```
    

**3D繪圖**

- 套件: **scatterplot3d :**
    
    **`library(rgl)` : 支援互動式的3D圖像。**
  

```r
#先劃出平面散佈圖
plot3d(x = iris[,"Sepal.Length"],
       y = iris[,"Sepal.Width"],
       z = iris[,"Petal.Length"],
       col = COLOR, size = 3, xlab = "Sepal Length", ylab = "Sepal Width", zlab = "Petal Length")

#挑出第一種花，畫成一個3D拓圓形，做出分類的感覺
VCOV = cov(iris[1:50,c("Sepal.Length", "Sepal.Width", "Petal.Length")])
MEAN = apply(iris[1:50,c("Sepal.Length", "Sepal.Width", "Petal.Length")], 2, mean)

ellips <- ellipse3d(VCOV, centre = MEAN, level = 0.95)
plot3d(ellips, col = "red", alpha = 0.2, add = TRUE, box = FALSE)

#挑出第二種花
VCOV = cov(iris[51:100,c("Sepal.Length", "Sepal.Width", "Petal.Length")])
MEAN = apply(iris[51:100,c("Sepal.Length", "Sepal.Width", "Petal.Length")], 2, mean)

ellips <- ellipse3d(VCOV, centre = MEAN, level = 0.95)
plot3d(ellips, col = "green", alpha = 0.2, add = TRUE, box = FALSE)

#挑出第三種花
VCOV = cov(iris[101:150,c("Sepal.Length", "Sepal.Width", "Petal.Length")])
MEAN = apply(iris[101:150,c("Sepal.Length", "Sepal.Width", "Petal.Length")], 2, mean)

ellips <- ellipse3d(VCOV, centre = MEAN, level = 0.95)
plot3d(ellips, col = "blue", alpha = 0.2, add = TRUE, box = FALSE)
```

### 2. **地理資訊的視覺化**

**套件** 

- **RgoogleMaps : 最常拿來繪製Google地圖的套件**

```r
#api 設定
lat = c(22.88751, 23.41373)
lon = c(120.023, 120.6562)
center = c(mean(lat), mean(lon))
zoom = min(MaxZoom(range(lat), range(lon)))
MyMap = GetMap(center = center, zoom = zoom, API_console_key = 'AIzaSyA4DVFtF70aXE7RgrXViy2z5Ku2pMkVxFI')

#衛星圖
MyMap2 = GetMap(center = center, zoom = zoom, maptype = "satellite", API_console_key = 'AIzaSyA4DVFtF70aXE7RgrXViy2z5Ku2pMkVxFI')

```

- **標示點位置 : PlotOnStaticMap()**

```r
PlotOnStaticMap(MyMap, lat = subdat$最小統計區中心點Y, lon = subdat$最小統計區中心點X, pch = 19, col = "red", cex = 1)
```

## 文字檔操作

---

### 基本操作

- **字串**
    - **連接**  **#****`"AB"`**
        - 物件相同 : **`paste(c("A", "B"), collapse = "")`**
        - 物件不同 : **`paste("A", "B", sep = "")`**
    - **分割**
        - **`strsplit("A.B", split = "." , fixed = TRUE)`**
    - **查重比對 :** 回傳布林值
        
        ```r
        x = c("AB", "AA")
        grepl("B", x)
        
        #TRUE FALSE 
        ```
        
    - **查找  :**  **`gregexpr("目標文字" , 目標字串, fixed = TRUE)`**
        - 回傳出現位置
            
            ```r
            x = c("ABABC", "CCAAE")
            gregexpr("A", x)
            
            #[[1]]
            ## [1] 1 3
            ## [1] 1 1
            
            # [[2]]
            ## [1] 3 4
            ## [1] 1 1
            ```
            
        - 取值  **`substr` ( )**
            
            **`substr("ndmc1234", 1, 4)`      #** **`"ndmc"`**
            
        - 取代  **`gsub ("被換字", "欲換字", 目標字串)`**
            
            ```r
            x = "AABB"
            gsub("A", "C", x)
            ```
            
    - 文字屬性
        - 字串長度 : **`nchar(x)`**
            
            ```r
            x = c("A","AAA","AAAAA")
            nchar(x)
            
            #[1] 1 3 5
            ```
            
        - **大小寫切換 :**  **`tolower("文字")`  /** **`toupper("文字")`**
    
    ### Practice :處理半格式化的文字資料
    
    ```r
    # readlines :讀檔案
    dat = readLines("102年9月.txt", encoding = "UTF-8")
    dat1 = dat[grepl("經緯度：", dat)]
    dat2 = gsub('經緯度：', "", dat1)
    dat3 = strsplit(dat2, '、')
    lat = rep(NA, length(dat3))
    lon = rep(NA, length(dat3))
    for (i in 1:length(dat3)) {
    if (grepl('南', dat3[[i]][[1]])) {lat_sign = -1}else {lat_sign = 1}
      current_lat = strsplit(dat3[[i]][[1]], '度')
      current_lat = gsub('北緯', '', current_lat[[1]])
      current_lat = gsub('南緯', '', current_lat)
      current_lat = gsub('分', '', current_lat)
      current_lat = as.numeric(current_lat)
      lat[i] = (current_lat[1] + current_lat[2] / 100) * lat_sign
    if (grepl('西', dat3[[i]][[2]])) {lon_sign = -1}else {lon_sign = 1} #西為1 東為-1
      current_lon = strsplit(dat3[[i]][[2]], '度')
      current_lon = gsub('東經', '', current_lon[[1]])
      current_lon = gsub('西經', '', current_lon)
      current_lon = gsub('分', '', current_lon)
      current_lon = as.numeric(current_lon)
      lon[i] = (current_lon[1] + current_lon[2] / 100) * lon_sign  
    }
    
    # 標示在地圖上 (配合L.10)
    library(RgoogleMaps)
    
    #以下點為上面第一步取得的資料
    lon = c(107.02, 103.3444, 112.402, 117.417, 103.37, 104.05, 98.45)
    lat = c(10.13, 1.0918, -7.099, -0.164, 1.07, 4.52, 3.58)
    
    center = c(mean(lat), mean(lon))
    zoom = min(MaxZoom(range(lat), range(lon)))
    
    ```
    
    ### 正則表達式
    
    - 描述某些patten的位置
        - **`[Aa]` : A 或 a”**
        - **`[^1-9]` : not 1:9**
            
            ```r
            x = c("abc123", "abcd12!34")
            gsub("[^0-9]", "", x)
            
            #找出不屬於0-9的所有字元取代為空值
            ```
            
        - **`[a-zA-Z]` : 所有英文字母   ###** **`[w-Z]` :不可使用**
        - 用**大括號**來描述我們可以允許的條件
            - `"*" /  {0, }` : 至少出現 0次, 最多無限多次”
            - `"+" / {1, }`  : 至少出現 1次, 最多無限多次
            - `"?" / {0,1}`   : 至少出現 0次, 最多出現 1次
        
        ```r
        gsub(" +", " ", "nice to  meet   you.")
        ## [1] "nice to meet you."
        
        gsub(" {1,}", " ", "nice to  meet   you.")
        ## [1] "nice to meet you."
        ```
        
        - 用**小括號**用來確認字串寫法
            - `"$"` : 字尾限定
            - `"^"`  : 字首限定
            - `"|"`  : or
            - `"."` : 任意字元
        
        ```r
        x = c("how are you", "hi Jack", "nice to meet you")
        grepl("you$", x) 
        
        # TRUE FALSE TRUE
        
        grepl("^h", x)
        # TRUE TRUE FALSE 
        ```
        
    
    ### 相似度與拼寫校正
    
    - **stringdist  套件: 文字相似度比較**
        - 函數: **stringsim ()**
        
        ```r
        library(stringdist)
        stringsim("abcde", "abcdfg", method = "cosine")
        ## [1] 0.7302967
        
        #method = “cosine” : 確保比對的字串即使順序不同也可以做比對
        
        stringsim("abcd", "cabd", method = "cosine")
        ## [1] 1
        ```
        
    - **hunspell 套件: 拼寫校正**
        - 函數: **hunspell () →只能矯正單字，尚無法矯正語法 →會列出可能的欲表達的單字**
        
        ```r
        library(hunspell)
        words = c("beer", "wiskey", "wine")
        correct = hunspell_check(words)
        hunspell_suggest(words)
        
        ##  [1] "beer"  "veer"  "beet"  "bee"   "beers" "beery" "bees"  "seer"  "bier" 
        ## [10] "bear"  "been"  "leer"  "deer"  "beep"  "peer" 
        ## 
        ## [[2]]
        ## [1] "whiskey"  "whiskery"
        ## 
        ## [[3]]
        ##  [1] "quine" "win"   "wines" "swine" "twine" "wince" "wined" "whine" "wins" 
        ## [10] "wise"  "sine"  "wane"  "nine"  "wire"  "tine"
        ```
        
        ### Practice 3
        
        ```r
        original_sentence = "I could’nt tell there friends"splited_sentence = strsplit(original_sentence, " ")[[1]]
        correct = hunspell_check(splited_sentence)
        if (sum(!correct) > 0) {
          wrong_pos = which(!correct)
        	for (iin wrong_pos) {
            splited_sentence[i] = hunspell_suggest(splited_sentence[i])[[1]][1]
          }
        }
        paste(splited_sentence, collapse = " ")
        
        ## [1] "I couldn't tell there friends"
        ```
