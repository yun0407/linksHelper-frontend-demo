# LinksHelper
A platform for mutual help by clicking on links.

## 專案簡介
LinksHelper 是一個以「互點連結」為概念的互助平台，鼓勵使用者透過點擊彼此的連結來互助、累積曝光。
此專案為一個全端應用，使用 React.js 製作前端介面，搭配 Node.js 後端與 Redis 資料庫，完整實現從前端提交、後端儲存、使用者點擊追蹤到結果展示的流程。

> 練習目的：熟悉前後端整合、Redis 操作、部署與網站結構設計。

---

## 技術-程式語言工具

| 層級  |  使用技術   |
|------|------------|
| 前端 | React.js (Create React App), Tailwind CSS |
| 後端 | Node.js |
| 資料庫 | Redis |
| 其他 | Axios、CORS、自訂 URL 解析 |
| 部署 | EC2 + Apache + Redis DB  |

---

## 功能簡介

- 使用者可提交個人想要被點擊的連結
- 系統會顯示他人提交的連結以供點擊
- 點擊連結時會自動追蹤點擊紀錄
- 每位使用者每次只能點一筆，避免重複刷點
- 後端使用 Redis 儲存連結狀態與點擊次數
- 點擊後自動更新資料並回傳新列表
- 簡單防作弊邏輯與資料驗證

---

## 關於網站

### 網站
[https://www.linkshelper.com/](https://www.linkshelper.com/)
> ⚠️ 注意：此網域已停止續費，因此網站屆時將無法訪問。
> 建議觀看影片或參考截圖。

---

### Demo

#### [操作範例影片](https://youtu.be/A3pvNLcLX_0)

#### [GitHub Pages 展示](https://yun0407.github.io/linksHelper-frontend-demo/)


#### [使用教學說明](https://www.dcard.tw/f/buyonline/p/257384116?cid=6C9E087B-75AF-49D5-9CEF-D498E5E63A8F)

---

### 畫面展示

| 首頁 | 第一位點擊 | 第二位點擊 |
|------|----------|--------|
| ![image](https://hackmd.io/_uploads/SJpyYDfwle.png)| ![image](https://hackmd.io/_uploads/BJ72CLmDgl.png)|![image](https://hackmd.io/_uploads/HJMBxwmwgl.png)|
| **第二位點擊確認** | **第二位儲存結果** | **贊助畫面** |
|![image](https://hackmd.io/_uploads/HyrtxDXPel.png)|![image](https://hackmd.io/_uploads/Byu5xDmvll.png)|![image](https://hackmd.io/_uploads/B1fPKPGPxe.png)|

---

## 專案架構

linksHelper/
├── my-app/ # React 前端 + Node.js 後端
│ └── server/ # Node.js/Express server + Redis
├── infra/ # 部署與系統設定檔案
│ ├── apache/ssl.conf
│ ├── cron/clear_redis.cron
│ ├── scripts/
│ │ ├── clear_redis.sh
│ │ └── linkshelper_monitor.sh
│ └── systemd/linkshelper_monitor.service

## 開發與部署說明

### 前端啟動（React）

```
bash
cd my-app
npm install
npm start
```

### 後端啟動

```
cd my-app/server
npm install
node server.js
```

### redis 啟動

```redis-server```

### 部署設定

本專案原部署於 AWS EC2，前端使用 AWS S3+cloudfront, Apache 反向代理設定 HTTPS 與轉發。

Apache 設定檔範例位於： `infra/apache/ssl.conf`

請將該檔案放至 EC2 路徑 /etc/apache2/sites-enabled/ssl.conf 並重新啟動 Apache (`systemctl restart apache2`)

```
sudo apachectl configtest
sudo systemctl reload apache2
```

### 腳本

#### 清除 Redis 每日資料腳本（crontab 自動化）

腳本位於 `infra/scripts/clear_redis.sh`，透過 crontab 每天自動清理 Redis。
crontab 設定樣板 `infra/cron/clear_redis.cron`

#### 自動監控服務腳本（systemd）
若後端掛掉，可透過 systemd 自動監控並重啟。
腳本：`infra/scripts/linkshelper_monitor.sh`
systemd 服務設定：`infra/systemd/linkshelper_monitor.service`
