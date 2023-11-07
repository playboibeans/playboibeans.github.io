---
layout: post
title: 自己的串流自己架 - 樹莓派 Plex 伺服器 + NAS 系統架設
description: 自行使用樹莓派 Raspberry Pi 4 搭配 OpenMediaVault 架設 NAS 系統和 Plex 伺服器，建立屬於自己的串流服務。
categories:
- Blog
tags:
- 樹莓派
- Raspberry Pi
- OpenMediaVault
- Plex
date: 2023-11-07 10:43 +0800
---
## 那顆我以為壞掉的硬碟
前陣子回老家翻到了大學時候買的一顆 Sony 硬碟，當時使用到後期怎麼讀都讀不到，原本以為是硬碟壞掉了想要拿去原廠修，沒想到再看到他已經是畢業 4 年之後。<br>
誰知道突發奇想的拿起了**另一顆壞掉的**硬碟的 USB 線一接，搭拉，竟然就這樣復活了，原來一直以來都是線的問題。<br>
但替代的硬碟早就買了，硬生生的多出了 1TB 的空間好像也暫時用不到，就決定來做一組已經想了一陣子的 NAS ＋ Plex 伺服器系統。<br>
以下和大家介紹我的搭建流程。僅供參考。<br>

## 準備需要設備
決定把這套系統架在 Raspberry 上，優點我不知道，但就是想玩玩看，所以上蝦皮買了一套 Raspberry Pi 4B 8GB 的套組。<br>
剛好那時後 Pi 5 剛發表沒多久，所以 Pi 4B 已經在降價了，買下來約 3000 台幣，我覺得還可以接受。<br>
![Raspberry Pi 4B 8GB](/assets/img/pi/02.webp){: lqip="/assets/img/pi/lqip/02.webp" }
*Raspberry Pi 4B 8GB ＋ 小風扇 ＋ 透明塑膠外殼*
另外還需要準備的東西有：
- **MicroSD 卡**（裝作業系統）
- **外接硬碟**（或要很大的 SD 卡也可以）
- **Type C 電源**（有的手機充電頭不合用，建議直接買原廠的，免去查資料的困擾）
- **網路線**（Pi 可以用 Wi-Fi，但架設 OMV 的時候會讀不到 Wi-Fi，所以設置階段一定要有實體網路線）
- **另一台電腦**（來 SSH 操作 Pi）
![Raspberry Pi + 外接硬碟](/assets/img/pi/03.webp){: lqip="/assets/img/pi/lqip/03.webp" }
*Raspberry Pi + 老硬碟*

## 安裝 Raspberry Pi OS
### 燒錄及設置 Raspberry Pi OS
首先先以其他電腦下載 [Raspberry Pi Imager](https://www.raspberrypi.com/software/){:target='_blank'} 來將 Raspberry Pi OS 燒錄到 SD 卡中。<br>
這邊我以 Mac 來做安裝示範：<br>
1. 進到 Imager 後看到這個畫面，分別有三個地方要做選擇。
![Pi Installation](/assets/img/pi/pi-install/01.webp)

2. 最左邊的 Raspeberry Pi Device 選擇的 Pi 裝置，我的是 4B 所以我選擇 Raspberry Pi 4。
![Pi Installation](/assets/img/pi/pi-install/02.webp)

3. 中間的操作系統我先進到下滑後的 Raspberry Pi OS (other)。
![Pi Installation](/assets/img/pi/pi-install/03.webp)

4. 然後選擇 Raspberry Pi OS (Legacy, 64-bit) Lite。<br>
因為 OMV 目前還不支援，不能選擇 **Raspberry Pi OS Lite (64-bit)**。
![Pi Installation](/assets/img/pi/pi-install/15.webp)

5. 最右邊的儲存卡則選擇我的 SD 卡。<br>
這邊顯示的是 **Raspberry Pi OS Lite (64-bit)**，是因為這是舊的截圖，<br>應該要顯示 **Raspberry Pi OS Lite (64-bit)**。
![Pi Installation](/assets/img/pi/pi-install/05.webp)

6. 按下 NEXT 後會問是否要進行設置，選擇編輯設置。
![Pi Installation](/assets/img/pi/pi-install/06.webp)

7. 如果你的 Mac 有儲存 Wi-Fi 密碼可以按 Yes 輸入指紋或電腦密碼讓系統自動帶入你的 Wi-Fi 資訊給 Pi。
![Pi Installation](/assets/img/pi/pi-install/07.webp)

8. 然後我們就可以來編輯這些資訊：
 - 主機名
 - 帳號和密碼
 - 如果沒有前一步的自動帶入，這邊可以設置 Wi-Fi
![Pi Installation](/assets/img/pi/pi-install/08.webp)

9. 點擊 SERVICES 的頁面來開啟 SSH 服務，然後按保存。
![Pi Installation](/assets/img/pi/pi-install/09.webp)

10. 選擇是。
![Pi Installation](/assets/img/pi/pi-install/10.webp)

11. 等到寫入完成後就可以移除 SD 卡，插到 Pi 上。
![Pi Installation](/assets/img/pi/pi-install/11.webp)
![Pi Installation](/assets/img/pi/pi-install/12.webp)

### SSH 進入 Raspberry Pi
接下來 SSH 進到 Pi 裡準備做 OpenMediaVault 的設置：
1. 打開連接了同一網路的電腦的 Terminal 後輸入 `ssh 上面第八步設置的帳號@上面第八步設置的主機名.loacl`，<br>
例如我的我就會是 `ssh beans@beanspi.local`。
![Pi SSH](/assets/img/pi/pi-install/13.webp)
2. 按 enter 後會請你輸入 YES，之後再輸入上面一起設置的密碼，就會進到 Pi 的主機裡頭。<br>
如果看到這樣的畫面就是成功了。
![Pi SSH](/assets/img/pi/pi-install/14.webp)


## 設置 OpenMediaVault NAS 系統
我決定先以 OpenMediaVault 架設 NAS 系統。<br>
如果要單純安裝 Plex 伺服器也可以，但這樣只能透過 Terminal 來操作硬碟上的檔案，<br>
所以我安裝了 NAS 系統，把同一個硬碟位置用 SMB/CIFS 分享出來，<br>
這樣單純做硬碟內的檔案操作會比較方便一點，真的有東西需要暫時存放在硬碟上也很方便。<br><br>

### 安裝 OpenMediaVault
首先我們先分別執行以下兩段指令來更新：<br>
`sudo apt update`<br>
`sudo apt upgrade`<br>
如果有提示 `Do you want to continue? [Y/n]` 則輸入 Y。<br>
等待幾分鐘等跑完後再輸入這段指令來下載 OpenMediaVault：<br>
```
sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
```
這個階段會跑個大約 20 分鐘，請耐心等候。<br>

### 登入 OpenMediaVault
完成後會發現我們沒有辦法再以 beans@beanspi.local 這個方式執行 ssh，這是因為 OMV 安裝好後這些權限都會被修改，需要另外進到 OMV 去做開啟。<br>
所以我們會需要到路由器的管理頁面去找到 Raspberry Pi 的 IP，<br>
通常設備名稱會是我們前面設定的主機名稱，不會太難找。<br>
例如我的是 192.168.11.252：<br>
![Pi IP](/assets/img/pi/pi-install/16.webp)

將這串 IP 輸入到瀏覽器裡後就能進到 OMV 的登入畫面。<br>
![OpenMediaVault 登入畫面](/assets/img/pi/pi-install/17.webp)

輸入預設帳號密碼進入<br>
預設帳號：admin<br>
預設密碼：openmediavault<br>

**這邊請立即進入右上角的變更密碼處進行密碼變更。**<br>
![OpenMediaVault 密碼變更](/assets/img/pi/pi-install/18.webp)

### 設置檔案系統
修改完密碼後我們需要建立一個檔案系統來進行共享檔案夾。<br>
1. 首先進到左手邊儲存裝置裡的檔案系統：
![OpenMediaVault 檔案系統設置](/assets/img/pi/pi-install/19.webp)

2. 點擊左上角三角形掛載現有的檔案系統：
![OpenMediaVault 檔案系統設置](/assets/img/pi/pi-install/20.webp)

3. 第一欄的檔案系統下拉選單即可選擇要作為共享檔案夾的硬碟：
![OpenMediaVault 檔案系統設置](/assets/img/pi/pi-install/21.webp)

4. 儲存後上方會有有一欄黃色警示，請按下右上角的套用：
![OpenMediaVault 檔案系統設置](/assets/img/pi/pi-install/22.webp)

### 設置共享檔案夾
完成檔案系統設置後我們現在就可以將他設為共享檔案夾
1. 首先進到檔案系統下方的共享檔案夾：
![OpenMediaVault 共享檔案夾](/assets/img/pi/pi-install/23.webp)

2. 點擊左上角的新建：
![OpenMediaVault 共享檔案夾](/assets/img/pi/pi-install/24.webp)

3. 將資訊輸入，可以自行設置，因為使用的是翻到的 Sony 硬碟所以我叫他 Sony。<br>
檔案系統選擇剛剛掛載的檔案系統：
![OpenMediaVault 共享檔案夾](/assets/img/pi/pi-install/25.webp)

4. 儲存後一樣記得按右上角的套用：
![OpenMediaVault 共享檔案夾](/assets/img/pi/pi-install/26.webp)

### SMB/CIFS 設置
1. 接下來移動到左手邊 服務 > SMB/CIFS > 設定 中的左上角啟用 SMB：
![SMB/CIFS 設置](/assets/img/pi/pi-install/27.webp)

2. 右下角儲存完後，一樣按右上角套用：
![SMB/CIFS 設置](/assets/img/pi/pi-install/28.webp)

3. 接下來移動到 服務 > SMB/CIFS > 共享 將剛剛設置的共享檔案夾加進來：
![SMB/CIFS 設置](/assets/img/pi/pi-install/29.webp)
![SMB/CIFS 設置](/assets/img/pi/pi-install/30.webp)

完成後我們就能透過 Finder 或 Windows 檔案總管存取我們的 NAS 了！<br>
只要連線到 Pi 的 IP 位置並輸入我們前面設置的帳號密碼即可：
![SMB/CIFS 設置](/assets/img/pi/pi-install/31.webp)

## 設置 Plex 伺服器

接下來我們設置 Plex 伺服器，<br>
從 Terminal SSH 進到 Pi 裝置裡之後依序輸入以下指令：<br>
```
sudo apt install apt-transport-https
```
```
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```
```
curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
```
```
sudo apt update
```
```
sudo apt install plexmediaserver
```
```
sudo systemctl start plexmediaserver
```
```
sudo systemctl enable plexmediaserver
```

完成後重新啟動：
```
sudo reboot
```

可以執行這段指令，如果有看到綠色的 active 表示伺服器正常運作：
```
sudo service plexmediaserver status
```
確定有在運作後就可以到瀏覽器輸入 IP:32400/web 來進入 Plex 後台，<br>
例如：192.168.11.252:32400/web。<br>
接下來跟著 Plex 的指示設定，將檔案夾位置導引到外接在 Raspberry Pi 上的硬碟或 SD 卡讀取需要被串流的檔案即可！<br>
![PLEX](/assets/img/pi/pi-install/32.webp)

就這樣！<br>
我們完成設定，將用不到的硬碟和 Raspberry Pi 搭建成 NAS 和 Plex 串流伺服器了！<br>