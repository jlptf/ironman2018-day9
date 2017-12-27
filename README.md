## Day 9 - yaml

### 本日共賞

* yaml
* map
* list
* 描述第一個 Pod 物件

### 希望你知道

* [與 k8s 溝通: kubectl](https://ithelp.ithome.com.tw/articles/10193502/draft)

#### yaml

k8s 使用 yaml 檔來描述物件，因此，在進一步了解 k8s 前，至少對 yaml 有基本的認識。yaml 全名為 (Yet Another Markup Language)，是一種用來描述設定且易讀的文字格式 (human-readable text-based format)。

> 說實話，剛開始閱讀真的很頭痛。不過真的不確定是不是被催眠了，看越久就越習慣，然後真的覺得很容易閱讀。

在 k8s 中使用 yaml 會帶給你幾項好處

* `方便`：部署不需要再輸入一大堆參數
* `易維護`：可以利用版本控制 (git, bitbucket, ..) 追蹤變化
* `彈性`：很容易新增或刪除項目

使用 yaml 基本上只要弄清楚 map 與 list，大致上就沒有閱讀或寫作上的問題。

#### map

map 是由 key-value 組成。很方便用於描述設定，例如

```
# 註解內容    <=== 註解就用 # 符號
---          <=== 利用這個來區隔不同物件的設定
apiVersion: v1   <=== 指定使用 v1 版本 的 api
kind: Pod        <=== 指定類型為 Pod
```

第一行 `---` 是分隔線，如果想在一個 yaml 撰寫多個物件設定的話，可以利用 `---` 做區分。

> 如果只有一組設定，`---` 可以省略。

上面的內容不需要解釋應該也很容易了解，我們定義了 `apiVerion` 是 v1 而 `kind` 是 Pod。

如果熟悉 JSON 的話，上述的設定等於以下 JSON

```javascript
{
   "apiVersion": "v1",
   "kind": "Pod"
}
```

再看一個更複雜的結構

```
---
apiVersion: v1
kind: Pod
metadata:
  name: web   <=== 這裡的意思就是指定 Pod 的名稱為 web
  labels:     
    app: web  <=== 替 Pod 打上 app:web 的標記
```

這裡都只是用 map 組合成較複雜的結構，其中 `metadata` 中包含了 `name` 與 `labels`，而 `labels` 又包含了 `app`。習慣上，我們會使用兩個空白來描述被包含的內容

> 對齊與空格數很重要，必須要一致。如果你想使用四個空格，那整份文件就必須使用四個空格來組成

對應的 JSON 會長這樣

```javascript
{
   "apiVersion": "v1",
   "kind": "Pod",
   "metadata":{
   	  "name": "web",
   	  "labels":{
   	    "app": "web"
   	  }
   }
}
```

#### list

list 由 `-` 開頭並加入一個空格，例如

```php
args:
- parameters
- "custom message"
```

JSON 會變成

```javascript
{
  "args": ["parameters", "custom message"]
}
```

當然，map 也可以是 list 的成員，例如

```
spec:
  containers:   <=== 底下的 map 都是 list 的成員
  - name: frontend
    image: nginx
    port:       <=== 底下的 map 都是 list 的成員
    - containerPort: 80
```

等於以下的 JSON

```javascript
{
  "spec":{
    "containers":[{
      "name": "frontend",
      "image": "nginx",
      "port": [{
        "containerPort": "80"
      }]
    }]
  }
}
```

#### 描述第一個 Pod 物件

學會了上面的 yaml，我們就可以動手攥寫第一個 Pod 物件並嘗試部署到 k8s 中。事實上，我們已經寫好了！把上述的例子重新組合一下，並存成 pod.yaml

```
# pod.yaml

---
apiVersion: v1   <=== 指定使用 v1 版本的 api
kind: Pod        <=== 指定類型為 Pod
metadata:        <=== 將 Pod 命名為 web，並加入 app:web 的標記
  name: web
  labels:
    app: web
spec:                <=== 規格描述
  containers:        <=== 描述容器
  - name: frontend   <=== 將容器命名為 frontend
    image: nginx     <=== 使用 nginx 映像檔
    ports:
    - containerPort: 80   <=== 指定使用 80 port
```

還記得如何利用 kubectl 部署 yaml 檔嗎？試試底下指令

```bash
$ kubectl apply -f pod.yaml
pod "web" created
```

恭喜你！終於完成了第一個 Pod 物件的部署。

> 試試 [與 k8s 溝通: kubectl](https://ithelp.ithome.com.tw/articles/10193502/draft) 學過的 kubectl get pods 指令 看看能發現什麼


本文與部署檔案同步發表於 [https://jlptf.github.io/ironman2018-day9/](https://jlptf.github.io/ironman2018-day9/)