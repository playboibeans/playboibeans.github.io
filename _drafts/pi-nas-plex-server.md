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
---
## 那顆我以為壞掉的硬碟
前陣子回老家翻到了大學時候買的一顆 Sony 硬碟，當時使用到後期怎麼讀都讀不到，原本以為是硬碟壞掉了想要拿去原廠修，沒想到再看到他已經是畢業 4 年之後。<br>
誰知道突發奇想的拿起了**另一顆壞掉的**硬碟的 USB 線一接，搭拉，竟然就這樣復活了，原來一直以來都是線的問題。<br>
但替代的硬碟早就買了，硬生生的多出了 1TB 的空間好像也暫時用不到，就決定來做一組已經想了一陣子的 NAS ＋ Plex 伺服器系統。<br>

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
首先先以其他電腦下載 [Raspberry Pi Imager](https://www.raspberrypi.com/software/){:target='_blank'} 來將 Raspberry Pi OS 燒錄到 SD 卡中。<br>
![Pi Installation](/assets/img/pi/pi-install/01.webp)
![Pi Installation](/assets/img/pi/pi-install/02.webp)
![Pi Installation](/assets/img/pi/pi-install/03.webp)
![Pi Installation](/assets/img/pi/pi-install/04.webp)
![Pi Installation](/assets/img/pi/pi-install/05.webp)
![Pi Installation](/assets/img/pi/pi-install/06.webp)
![Pi Installation](/assets/img/pi/pi-install/07.webp)
![Pi Installation](/assets/img/pi/pi-install/08.webp)
![Pi Installation](/assets/img/pi/pi-install/09.webp)
![Pi Installation](/assets/img/pi/pi-install/10.webp)
![Pi Installation](/assets/img/pi/pi-install/11.webp)
![Pi Installation](/assets/img/pi/pi-install/12.webp)

然後 SSH 進到 Pi 裡。

## 建立 OpenMediaVault NAS 系統
我決定先以 OpenMediaVault 架設 NAS 系統。<br>
如果要單純安裝 Plex 伺服器也可以，但這樣只能透過 Terminal 來操作硬碟上的檔案，<br>
所以我安裝了 NAS 系統，把同一個硬碟位置用 SMB/CIFS 分享出來，<br>
這樣單純做硬碟內的檔案操作會比較方便一點，真的有東西需要暫時存放在硬碟上也很方便。<br>