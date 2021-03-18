# 在 iOS 裝置上執行 Linux shell 環境

---

## 前言

因為一位朋友的介紹，最近對 "在手機上運作 Linux" 這件事很著迷。雖然也不知道運作後要幹嘛就是了 XD 反正就是好玩咩~

這類的 App 在 Android 上比較多，但除非你的裝置有 root 過，否則使用上會遭遇一些限制。

前幾天無聊試著在 Apple App Store 上搜關鍵字，結果看到一個有趣的 App -- **iSH** (點下圖跳 App Store)


[![](images/icon.png)](https://apps.apple.com/us/app/ish-shell/id1436902243)

iSH 的官網在[這邊](https://ish.app/)，網站上的標題就告訴你 "The Linux shell for iOS." 感覺好像很厲害，可以玩看看。


## 官方資源 


這類的東西其實非常冷門，沒什麼人在討論。唯一能找到最好的資源都在 [iSH 官方的 GitHub](https://github.com/ish-app/ish)。

你所有需要知道的內容都寫在 GitHub 所附的 [WiKi](https://github.com/ish-app/ish/wiki) 分頁中。

因為這篇定位在 Quickstart Guide，所以我只列出幾篇我認為你應該要先看的:

* **(Help) [User Interface](https://github.com/ish-app/ish/wiki/User-Interface)**

原則上一裝好 iSH、打開 App，馬上就會進入一個 Linux shell 環境。iSH 使用的 distro 是 [Alpine Linux](https://zh.wikipedia.org/wiki/Alpine_Linux)，是個適用於嵌入式系統的微型 Linux。

你應該要先看這篇，了解在 iSH 的環境中怎麼操作鍵盤。

![](images/iphone-keyboard.png)

我過去曾用過一些 iOS 上的 SSH Apps，這些 App 在連線主機後都會提供鍵盤讓你輸入，但說真的他們都沒有 iSH 做得那樣好 -- iSH 的鍵盤配置是我目前看過最好的。看它特別將 Tab、Ctrl、Esc 鍵獨立出來就知道它真的是為了 Linux 的使用者設計的。


* **(Help) [Using iSH](https://github.com/ish-app/ish/wiki/Using-iSH)**

這篇會稍微提到 apk 的用法，你也可以去查 [Alpine Linux package management](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management) 的文件。


* **(Help) [What works?](https://github.com/ish-app/ish/wiki/What-works%3F)**

iSH 的 shell 環境中有些指令是不 work 的，這裡整理了一份表格。


* **(Tutorials) [Install & Activate Alternate Filesystems](https://github.com/ish-app/ish/wiki/Install-&-Activate-Alternate-Filesystems)**

iSH 的運作方式是幫你 bootup 一個 Filesystems。這篇文章告訴你如何啟用一個客製化的 Filesystem。

文件示範是下載 Alpine Linux 的 Mini Root Filesystem，然後在 iSH App 中啟動它。


* **(Tutorials) [Running an SSH server](https://github.com/ish-app/ish/wiki/Running-an-SSH-server)**

雖然 iSH 的鍵盤很好用，但是若可以透過 SSH 連進去 Alpine Linux 的環境會更好，就可以使用一般的鍵盤下指令了。這篇文章教你如何啟用 SSH server。


## 其他資源


* Alpine Linux 的 [下載頁](https://alpinelinux.org/downloads/)

這邊可以下載 for 不同平台的 filesystem tarball。iSH 預設使用的是 `x86 的 Mini Root Filesystem`。


* [Mirror health - Alpine Linux](https://mirrors.alpinelinux.org/)

你需要設定 apk repository 的 URLs 才能從網路下載套件。為了讓下載速度更快，你需要在上面查詢離你最近的 mirror 站台。


## iSH 操作紀錄

下面的操作會嘗試下載 Alpine Linux 的 Filesystem tarball 到 iphone 中，然後從 iSH App 中 bootup 它。

也會嘗試在 iSH 提供的 Alpine Linux 環境中架設 SSH server，讓我能從另一台電腦以 ssh 登入。

這些圖是我邊操作邊截圖的，可能有一些指令會得到錯誤的結果，就當作筆記吧 :p

我所使用的手機是 iPhone 6s Plus 128GB, iOS 11.0.2。透過連線 WiFi 路由器出 internet。


### 1. 下載並啟用 Filesystem


iSH App 啟動後，會馬上進入一個 linux shell。從歡迎畫面的字串看，是 Alpine Linux。它還提示是你可以用 `apk add <package>` 來安裝套件。

![](images/iSH-alpine_01.png)

如果沒有網路連線能力，Linux 系統等於廢了武功。我測試 `netstat` 的指令，結果是失敗的。原因可能是它根本沒有對應的 proc filesystem。

`ps` 的指令可以用...行程很少耶 @@

`dmesg` 沒有顯示出訊息。這可能表示啟動這個系統並沒有經過 bringup linux kernel 的動作。而是直接從 user space 的 init 開始。

/bin 下面看到 busybox...嗯~ 果然這是個微型的 Linux 系統。

![](images/iSH-alpine_02.png)

init 系統使用的是 OpenRC，而不是目前桌上型 distro 主流的 systemd。

`ifconfig` 的指令依然不能使用，原因應該跟前面提到的一樣，根本沒有對應的 proc filesystem。

![](images/iSH-alpine_03.png)


根據手冊試著去 Alpine Linux 的 [下載頁](https://alpinelinux.org/downloads/)，找到 Mini Root Filesystem 的分類，然後下載 x86 的 tarball。

目前最新的檔名是: **alpine-minirootfs-3.13.2-x86.tar.gz** (其實你用 `tar -zxvf` 把它解壓縮你就知道是怎麼一回事了)

![](images/iSH-alpine_04.png)

iSH App 可以將剛剛的 rootfs tarball import 進 App 中。但是你需要透過第三方的 App 幫忙。

我選擇的是 Dropbox，所以你需要先將這個 tarball 上傳到你 Dropbox 的空間。手機也要裝 Dropbox App。

然後點 iSH 鍵盤右邊 **(i)** 的圖示進入設定頁面。進入後點選 `Filesystems`。

![](images/iSH-alpine_05.png)

你會看到目前 iSH 運作的 rootfs 是 default。事實上我猜它跟我剛剛下載的 tarball 應該是一樣的 XD 但是還是手動做一次測試吧。

點選右上方的 `Import`。

![](images/iSH-alpine_06.png)

這個畫面就不是 iSH 的畫面了，是 iOS 用來做 App 之間交換資料的畫面。如果你的 iphone 有裝 Dropbox App 也設定好。你應該可以點選到你剛剛上傳的 tarball。

點選檔案後，iSH 就會從 Dropbox 上將檔案下載回來。

![](images/iSH-alpine_07.png)

然後你就會看到 Filesystems 的清單中多了一個 alpine-minirootfs-3.13.2-x86，點擊它。

![](images/iSH-alpine_08.png)

然後選 `Boot From This Filesystem`。有沒有發現它說的是 boot filesystem，所以為何剛剛那個 dmesg 沒有訊息的原因應該可以串起來了吧。

點了之後你會發現 App 馬上關掉 XD 這是正常的，官方文件有提到。當你再次打開時，iSH 就會 booting 新的 rootfs。

#### 注意 

其實這個作法有點問題，如果你新的 rootfs 有問題的話，你的 iSH 啟動後就會卡死 = =。我第一次安裝時遇到這個問題，而且似乎無解。因為 iSH App 一啟動就幫你 boot rootfs 了，你根本來不及切換 rootfs。所以我最後就把 App 整個移除，再重裝一次，第二次竟然就正常了。

![](images/iSH-alpine_09.png)

如果一切都好，啟用後你應該會看到這樣的歡迎畫面。

![](images/iSH-alpine_10.png)


### 2. 設定套件管理程式

跟使用 apt 一樣，下載之前都要打 `apk update` 結果有錯誤訊息。

原本以為網路根本不通。結果好奇做了 ping test 發現可以出 internet 耶!!

這就有趣了，代表雖然 ifconfig 指令無法執行，但是系統的網路是有通的，喔耶!! 

![](images/iSH-alpine_11.png)

只要網路沒問題，接下來應該就容易多了。你要去查 [Alpine Linux 的套件管理文件](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management)。他會告訴你要將套件下載的 repo URLs 寫在這個設定檔。

![](images/iSH-alpine_12.png)

為了讓下載更快，你還可以去查台灣的 mirror 站台。

![](images/iSH-alpine_13.png)

因為 apk 所指定的站台 URLs 有帶 distro 的版號，所以你要先查目前運作中的 Alpine Linux 的版本。

```
cat /etc/alpine-release
```

是 3.12.0，然後編輯 `/etc/apk/repositories`

```
vi /etc/apk/repositories
```

順便測試一下它的鍵盤是不是使用 vi 完全沒問題 (結果: 真的沒問題，超讚!)

![](images/iSH-alpine_14.png)

加上這兩行即可。其實下面的 `file://` 可以註解掉，否則等等 apk 的指令會出現 WARNING 訊息，雖然不影響下載安裝，但看了討厭 XD

```
http://alpine.ccns.ncku.edu.tw/alpine/v3.12/main
http://alpine.ccns.ncku.edu.tw/alpine/v3.12/community
```

![](images/iSH-alpine_15.png)

`cat /etc/apk/repositories` 再次確認

![](images/iSH-alpine_16.png)

然後執行 `apk update` ... 成功了!!!

![](images/iSH-alpine_17.png)


### 3. 下載 SSH server

可以使用 `apk search openssh` 搜尋看看有沒有相關的套件

![](images/iSH-alpine_18.png)

apk 安裝的方式是用 add -- 執行 `apk add openssh`

真的有在下載耶，豪港動 QQ

![](images/iSH-alpine_19.png)

下載成功。Alpine 不像主流的 Linux distro，下載完後不會自動啟動服務，要自己手動啟用。

![](images/iSH-alpine_20.png)


### 4. 啟用 SSH server

我習慣啟用前先確認 netstat 和 ps 的結果。

![](images/iSH-alpine_21.png)

手動啟用 sshd 的指令是 `/usr/sbin/sshd` 執行後馬上出現錯誤訊息

> sshd: no hostkeys available -- exiting

就算你打算用帳號密碼登入，但還是要設定 hostkeys。


附帶一提，在 iSH 啟用後的 Linux 環境中，你就是 root

![](images/iSH-alpine_22.png)

這邊就是 Linux 的知識了。你必須知道當你打算使用 root 身分透過 ssh 登入時，你需要改那些地方:

```
vi /etc/ssh/sshd_config
# 將 PermitRootLogin 改為 yes
```

![](images/iSH-alpine_23.png)

如果你要使用帳號密碼登入，你還必須要替 root 設定密碼。執行 `passwd`

![](images/iSH-alpine_24.png)

執行 `ssh-keygen -A` 替系統產生 hostkeys。注意! 這個步驟有點久 (當然不是半小時那種久)。第一執行時我以為它當了，結果等一會之後，就執行成功了。

你可以看到它應該將 key 產生在 `/etc/ssh` 這個路徑下

![](images/iSH-alpine_25.png)

系統的時間有點不準，但以 `date` 查詢比對可以知道檔案是剛剛產生的。

![](images/iSH-alpine_26.png)

這邊我有點搞錯了，hostkey 跟使用者 ssh 進去要交換的 key 應該是兩件事。所以我根本沒必要將 public key 傳到另一台主機。但是就當作是順便測試 scp 指令吧 :p

首先，我發現可以 ping 到網域內的另一台主機。

![](images/iSH-alpine_27.png)

測試 scp 指令，將檔案複製到遠端主機...成功!

![](images/iSH-alpine_28.png)

這是遠端主機的 shell 畫面，可以看到檔案真的有從我的 iphone 傳過去。

![](images/iSH-alpine_29.png)

好吧! 再次啟用 sshd，執行 `/usr/sbin/sshd` 這次成功了!

使用 `ps` 檢查發現多了 PID:54 的 sshd 行程，表示 sshd 運作中!!

![](images/iSH-alpine_30.png)

我一直很好奇當 iSH 在我的 iphone 上執行，並啟用了一個 Linux shell 環境時，這個 shell 環境對外的 IP 到底是誰？該不會就是手機的 IP 吧？

所以我先查了 iphone 的 IP -- 172.16.99.x 是 WiFi router 配給我 iphone 的 IP。

![](images/iSH-alpine_31.png)

然後我找一台同網段的 Linux 主機，ssh iphone 的 IP...成功!!!

真的連入 iphone 內的 Alpine Linux 了!!!

![](images/iSH-alpine_32.png)

補充一點，如果遇到突然 ssh 連不上。可以在 Alpine Linux 上將 sshd 重啟。

我用比較簡單的方式，sshd 的 PID 是 54，所以先 `kill 54` 將 sshd 行程結束，然後再重新打 `/usr/sbin/sshd` 啟用一次。

你會發現系統又會給 sshd 一個新的 PID:71

![](images/iSH-alpine_33.png)

另外，如果你打 Linux 的關機指令 `poweroff`，iSH App 就會退出了。

![](images/iSH-alpine_34.png)


以上，希望你也覺得有趣 XD

~ END ~