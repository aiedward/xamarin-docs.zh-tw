---
title: "連線到 Mac"
description: "Xamarin.iOS for Visual Studio 可讓開發人員在 Windows 電腦上使用 Visual Studio IDE 來建立、組建 iOS 應用程式及針對這些應用程式進行偵錯。 本指南說明 Xamarin.iOS for Visual Studio 所提供的功能，以及如何建立與 Mac 組建主機的連線。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d0234cad243e523716d95f467e3fe44c50a182d5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="connecting-to-the-mac"></a>連線到 Mac

_Xamarin.iOS for Visual Studio 可讓開發人員在 Windows 電腦上使用 Visual Studio IDE 來建立、建置 iOS 應用程式，及針對這些應用程式進行偵錯。本指南說明 Xamarin.iOS for Visual Studio 所提供的功能，以及如何建立與 Mac 組建主機的連線。_

Visual Studio 會透過 SSH 連線到 Mac，這提供數個優點，包括：

- Visual Studio 可以直接啟動和控制組建代理程式。 不再有使用者可見的應用程式需要手動啟動和停止。

- Visual Studio 中的新「連線管理員」將會探索、驗證及記住 Mac 組建主機。

- 由於所有通訊都會透過 SSH 的安全通道傳送，因此只需要一個連至連接埠 22 的單一連接埠連線。

- 一有變更發生時，就會立即通知 Visual Studio。 例如，插入 iOS 裝置時，工具列會立即更新。

- 多個 Visual Studio 執行個體可以同時連線。

- 連線將不會打擾到開發。 只會在執行需要 Mac 的作業 (例如進行偵錯或使用「iOS 設計工具」) 時，出現連線到 Mac 的提示。

連線到 Mac 是由用於其功能各個不同組件 (例如 iOS 設計工具代理程式和組建代理程式) 的多個處理序所組成 – 這些處理序皆由 Broker 所控制。 這個 Broker 是由 Visual Studio 所控制並更新的，並且會在任何獨立處理序當機時，自動重新啟動這些處理序。

下圖顯示一個簡單的 Xamarin.iOS 開發工作流程概觀：

[![iOS 開發工作流程](images/xma2.png)](images/xma2.png)

> [!IMPORTANT]
>  Visual Studio 實際上會啟動個別的 MSBuild 處理序來建置專案。 此處理序會建立與 Mac 的新連線，也就是說當 Visual Studio 進行組建時，實際上有兩個從 Windows 到 Mac 的 SSH 連線。 從[命令列](#commandline)進行組建只會建立一個 MSBuild 處理序。 為了簡單起見，此圖表的所有連線都只由一個箭號代表。

## <a name="requirements"></a>需求

Xamarin.iOS for Visual Studio 實現卓越的技術：不僅可讓開發人員在 Windows 電腦上使用 Visual Studio IDE 來建立、組建 iOS 應用程式，還可針對這些應用程式進行偵錯。 它無法單獨完成這些工作 – 在沒有 Apple 編譯器的情況下，無法建立 iOS 應用程式，而在沒有 Apple 憑證及程式碼簽署工具的情況下，也無法部署這些應用程式。 這意謂著 Xamarin.iOS for Visual Studio 安裝必須連線到已連上網路的 Mac OS X 電腦 (稱為「主機」或「組建主機」)，才能為您執行這些工作。 設定好之後，Xamarin 的工具將會儘可能讓程序無縫接軌。

### <a name="system-requirements"></a>系統需求

系統需求可於[在 Windows 上安裝 Xamarin.iOS](~/ios/get-started/installation/windows/index.md#system-requirements) 指南中找到


#### <a name="compatibility"></a>相容性

> [!IMPORTANT]
>  Windows 電腦必須與所連線的 Mac 使用相同的 Xamarin.iOS 版本。 確保此條件成立：                                                    
>                                                                                                                 
> - **Visual Studio 2015 和更早的版本**：確定您與 Visual Studio for Mac 位於相同的[更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)上。
>                                                                                                                 
> - **Visual Studio 2017 發行版本**：確定您位於 Visual Studio for Mac 的**穩定**通道上。
>                                                                                                                 
> - **Visual Studio 2017 預覽版本**：確定您位於 Visual Studio for Mac 的 **Alpha 版**通道上。 Visual Studio 不會檢查 Xamarin.iOS SDK 和 Xcode 是否存在且版本相容。
>   這將會由組建代理程式進行檢查而產生組建錯誤，以及由「iOS 設計工具」進行檢查而產生設計工具錯誤。

### <a name="connecting-to-the-mac"></a>連線到 Mac

#### <a name="mac-setup"></a>Mac 設定

若要設定 Mac 主機，您必須啟用 Visual Studio 的 Xamarin 擴充功能與 Mac 之間的通訊。 若要這樣做，請依照下面的步驟，在 Mac 上允許 [Remote Login] \(遠端登入\)：

1. 開啟 *Spotlight* (同時按**⌘ 鍵和空格鍵**) 並搜尋「遠端登入」，然後選取 [共享] 結果項目。 這會開啟 [系統偏好設定] 的 [共享] 面板：

   [![Spotlight 的遠端登入搜尋](images/spotlight.png)](images/spotlight.png)

2. 勾選左側 [服務] 清單中的 [遠端登入] 選項，以允許 Xamarin for Visual Studio 連線到 Mac：

   [![勾選 [服務] 清單中的 [遠端登入] 選項](images/sharing.png)](images/sharing.png)

3. 確定 [遠端登入] 已設定為允許 [所有使用者] 存取，或是右側清單之允許的使用者清單中包含您的 Mac 使用者名稱或群組。

此外，如果您的 OS X 防火牆已設定為預設封鎖已簽署的應用程式，則您可能需要允許 `mono-sgen` 接收傳入連線。 如果是這種情況，將會顯示警示對話方塊來提示您。

如果您的 Mac 上有目前已開啟的工作階段，且與 Visual Studio 位於相同的網路上，Visual Studio 現在應該就能探索到它。

Visual Studio 會將您 Mac 上的代理程式啟動並停止，讓身為使用者的您無須執行任何其他操作。

### <a name="windows-setup"></a>Windows 設定

請務必在您的 Windows 電腦上[安裝](~/ios/get-started/installation/windows/index.md) Xamarin 工具。

### <a name="connecting-to-the-mac-build-host"></a>連線到 Mac 組建主機

連線到 Mac 組建主機的方式有兩種：

在 iOS 工具列上：

[![iOS 工具列](images/image1.png)](images/image1.png)

或在 Visual Studio 中瀏覽至 [工具] > [選項]，選取 [Xamarin] > [iOS 設定]，然後按一下 [尋找 Xamarin Mac Agent] 按鈕：

[![尋找 Xamarin Mac Agent](images/image2.png)](images/image2.png)

以上述任一方式瀏覽都會前往 **Mac Agent** 對話方塊，如下所示：

[![Mac Agent 對話方塊](images/image3.png)](images/image3.png)

這會顯示先前已連線並儲存為已知電腦的所有電腦清單，或可供「遠端登入」的電腦。

請按兩下 Mac 來選取該 Mac 以與其連線。 第一次連線到 Mac 時，系統會提示您輸入 Mac 使用者認證以允許遠端連線：

[![輸入 Mac 使用者認證](images/image4.png)](images/image4.png)

代理程式將會使用這些認證來建立與 Mac 的新 SSH 連線。 如果成功，就會建立 SSH 金鑰，並在該 Mac 上的 `authorized_keys` 檔案中[註冊](#commandline)該金鑰。 在後續的連線中，代理程式會使用使用者名稱和金鑰檔來連線到最近連線過的已知組建主機。

> [!NOTE]
>  **注意**：輸入認證時，您必須使用_使用者名稱_，而不是_全名_。  您可以在 [終端機] 中使用 `whoami` 命令來找此使用者名稱。  例如，從下面的螢幕擷取畫面中，帳戶名稱將會是 **amyb**，而不是 **Amy Burns**：
>
> ![在 [終端機] 應用程式中尋找使用者名稱](images/image5.png)

已成功建立連線時，[主機選取] 對話方塊中會顯示 [已連線] 圖示，如下所示：

[![主機旁邊有 [已連線] 圖示的 [主機選取] 對話方塊](images/image6.png)](images/image6.png)

在任一時間都只能有一個已連線的 Mac。

在清單中的每部電腦 (不論是否已連線) 上按一下滑鼠右鍵時，都會顯示操作功能表，可讓您視需要 [連接]、[中斷連接] 或 [不要記住這部 Mac]：

[![[連接]、[中斷連接] 或 [不要記住這部 Mac] 操作功能表](images/image7.png)](images/image7.png)

如果您選擇 [不要記住這部 Mac]，就必須重新輸入您的認證，才能再次與它連線。

<a name="manual-add"/>

### <a name="manually-adding-a-mac"></a>手動新增 Mac

在某些情況下，如果您在 [主機選取] 對話方塊中沒有看到列出 Mac 的 mDNS 名稱，您可能會想要手動新增 Mac。 若要這樣做，請依照下列步驟進行操作：

1. 在 Mac 上瀏覽至 [系統偏好設定] > [共享] > [遠端登入] 來找出 Mac 的 IP 位址：

   [![[系統偏好設定] 中的 Mac IP 位址](images/image8.png)](images/image8.png)

   或者，如果您偏好使用命令列，則可以在 [終端機] 中輸入 `ipconfig getifaddr en0` 來找出 IP 位址 (請注意，視連線類型而定，變數可能會是 `en1``en2` 等)：

   [![[終端機] 應用程式中的 IP 位址](images/image9.png)](images/image9.png)

2. 返回 Visual Studio，然後在 [主機選取] 對話方塊中，選取 [新增 Mac]：

   [![[主機選取] 對話方塊](images/image10.png)](images/image10.png)

3. 在 [新增 Mac] 對話方塊中，輸入 Mac 的 IP 位址，然後按一下 [新增]：

   [![在 [新增 Mac] 對話方塊中輸入 Mac 的 IP 位址](images/image11.png)](images/image11.png)

4. 最後，輸入 Mac 系統管理員帳戶的使用者名稱 (不是全名) 和對應的密碼：

   [![輸入使用者名稱和密碼](images/image12.png)](images/image12.png)

按一下 [登入] 之後，Visual Studio 就會使用 SSH 來登入 Mac 電腦，並將這部 Mac 新增為已知的電腦。

<a name="commandline"/>

### <a name="command-line-support"></a>命令列支援

代理程式也支援從命令列建置 Xamarin.iOS 設定。  若要使用它，您將必須把下列必要的參數傳遞給 MSBuild：

- `ServerAddress` – Mac 伺服器的 IP 位址。

- `ServerUser` – 要用來登入 Mac 伺服器的使用者名稱 (不是全名)。

- `ServerPassword` – 用來登入 Mac 主機的密碼 (選用)。

`ServerPassword` 不是必要參數。

取而代之的是，第一次傳遞密碼 (不論是使用 Visual Studio 還是命令列來傳遞) 時，會針對該特定的 Windows、Mac 及使用者組態產生一個金鑰組，並儲存在 Windows 電腦上以供日後使用。 此金鑰組會位於 **%localappdata%\Xamarin\MonoTouch\id_rsa** 中。  如果您未傳遞 `ServerPassword` 參數，系統就會使用 `id_rsa` 金鑰檔來進行驗證。

以下顯示使用 **xamUser** 帳戶搭配 **mypassword** 密碼來連線到 Mac 10.211.55.2 的範例：

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

### <a name="summary"></a>總結

本文探索了 Visual Studio 與 iOS 組建及 Mac 上設計工具之間的連線，可讓您使用 Visual Studio 來組建 Xamarin.iOS 應用程式。

### <a name="related-links"></a>相關連結

- [安裝](~/ios/get-started/installation/windows/index.md)
- [連線疑難排解](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [使用 XMA 將 Mac 連線到您的 Visual Studio 環境 (影片)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent) \(英文\)
