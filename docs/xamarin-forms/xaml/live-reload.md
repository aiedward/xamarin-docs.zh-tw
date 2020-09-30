---
title: 'Xamarin Live 重載 (預覽) '
description: 查看對您的 XAML 所做的變更反映，而不需要進行其他編譯和部署。
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdc5b715d4502590b56b41b864835a47076ad518
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556447"
---
# <a name="xamarin-live-reload-preview"></a>Xamarin Live 重載 (預覽) 

> [!NOTE]
> Xamarin Live 重載的預覽已結束，我們想要感謝所有人提供您的意見反應和意見反應。 
>
> 若要在您的應用程式執行時編輯 XAML，請使用[的 Xamarin.Forms XAML 熱重新載入](~/xamarin-forms/xaml/hot-reload.md)。
>

Xamarin Live 重載可讓您 **變更您的 XAML 並查看其即時反映，而不需要進行其他編譯和部署**。 對 XAML 所做的任何變更都會在儲存時重新部署，並反映在部署目標上。

## <a name="requirements"></a>需求

* 使用 .NET 工作負載的行動裝置**開發**， [Visual Studio 2017 15.7 版或更新版本](https://visualstudio.microsoft.com/vs/)。
* [ Xamarin.Forms 3.0.0 或](https://www.nuget.org/packages/Xamarin.Forms/)更新版本。

## <a name="getting-started"></a>快速入門
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. 從 Visual Studio Marketplace 安裝 Xamarin Live 重載

Xamarin Live Reload 會透過 Visual Studio Marketplace 散發。 若要安裝擴充功能，請造訪 [Visual Studio Marketplace 網站上的 Xamarin Live 重載頁面](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) ，然後按一下 [ **下載**]。

開啟已下載的 .vsix，然後按一下 [ **安裝**]。

![Visual Studio installer Xamarin Live 重載確認](images/LiveReloadVSIXInstall.png)

或者，您也可以在 Visual Studio 內部 [**擴充功能和更新**] 對話方塊的 [**線上**] 索引標籤中搜尋它。

### <a name="2-configure-your-app-to-use-live-reload"></a>2. 設定您的應用程式以使用即時重載

將即時重載新增至現有的行動應用程式，可透過三個步驟來完成：

1. 請確認所有專案都已更新為使用[ Xamarin.Forms 3.0.0 或](https://www.nuget.org/packages/Xamarin.Forms/)更新版本或更新版本。

2. 新增 **LiveReload** NuGet 套件：

    a. **.NET Standard** –將 **LiveReload** NuGet 安裝到您的 .NET Standard 2.0 程式庫中。 這不需要安裝在您的平臺專案中。 確定 **封裝來源** 設定為 [ **全部**]。
    
    b. **共用專案** –將 **LiveReload** NuGet 安裝到所有平臺專案 (例如 ANDROID、iOS、UWP 等 ) 。 確定 **封裝來源** 設定為 [ **全部**]。

    [![使用 NuGet 封裝管理員新增 Xamarin Live 重載 NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. 將加入 `LiveReload.Init();` 至類別中的函式 `Application` ，如下列程式碼片段所示：

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. 開始即時重載

編譯及部署您的應用程式。 部署應用程式之後，請開啟 XAML 檔案，進行一些變更，然後儲存檔案。 您的變更會重新部署至部署目標。

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live 重載適用于任何 XAML 檔案的變更。 變更 c # 或新增/移除 NuGet 套件需要新的組建和部署才會生效。

## <a name="frequently-asked-questions"></a>常見問題集 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Visual Studio for Mac 上有 Xamarin Live 重載嗎？ 

否，Xamarin Live 重載的預覽版本僅適用于 Visual Studio 2017。

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>這是否適用于所有程式庫（例如 Prism）？ 

因為您的應用程式已編譯，所以 Live 重載適用于所有程式庫（例如 Prism）和協力廠商控制項程式庫，例如 Telerik、Infragistics、Syncfusion、ArcGIS、GrapeCity 和其他控制廠商。

### <a name="what-changes-does-live-reload-redeploy"></a>Live 重載會重新部署哪些變更？ 

Live 重載只會套用對 XAML 或 CSS 所做的變更。 如果您對 c # 檔案進行變更，將需要重新編譯。 

### <a name="what-platforms-are-supported"></a>支援哪些平台？ 

Live 重載適用于支援的任何平臺 Xamarin.Forms ，包括 Android、iOS 和 UWP。

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>這是否適用于模擬器、模擬器和實體裝置？ 

是，Live 重載適用于所有有效的部署目標，包括 Android 模擬器、iOS 模擬器和實體裝置。 部署至裝置時，裝置和電腦必須位於相同的 Wi-fi 網路上。

### <a name="does-this-work-with-corporate-networks"></a>這是否適用于公司網路？

如果您要對 Android 模擬器或 iOS 模擬器進行偵錯工具，Live Reload 會使用 localhost 進行通訊。 如果您想要部署到裝置，裝置和電腦必須位於相同的 Wi-fi 網路。 在無法進行這項工作的情況下，您可以 [設定您自己的 Live reload 伺服器](#live-reload-server)，讓您可以即時重載，而不需要網路連線設定。

### <a name="does-it-require-debugging-the-app"></a>是否需要對應用程式進行偵錯工具？ 

否。 事實上，您甚至可以在任意數目的裝置或模擬器/模擬器上 (Android、iOS 和 UWP) 啟動所有支援的應用程式目標，並一次查看全部更新。 

## <a name="limitations"></a>限制

* 僅支援重載 XAML。
* 除非使用 MVVM，否則可能無法在重新部署之間維護 UI 狀態。

## <a name="known-issues"></a>已知問題

* Visual Studio 僅支援。
* 連結必須設定為 **不要連結** 或 **連結 Framework sdk** 
* 若要重載整個應用程式的資源 (也就是 **應用程式 .xaml** 或共用資源字典) ，則會重設應用程式導覽。 
* 重載 ContentView 目前需要重載包含的頁面。
* 包含 AutomationId 的元素可能會造成重載失敗。
* 在調試 UWP 時編輯 XAML 可能會導致執行時間損毀。 因應措施：使用 [ **啟動但不調試] (Ctrl + F5) ** ，而不是 **啟動偵錯工具 (F5) **。

## <a name="troubleshooting"></a>疑難排解

### <a name="error-codes"></a>錯誤碼

* **XLR001**： *目前的專案參考 ' LiveReload ' NuGet 套件版本 ' [version] '，但 Xamarin Live 重載延伸模組需要版本 ' [version] '。*

  為了允許即時重載功能的快速反復專案和演進，NuGet 封裝和 Visual Studio 延伸模組必須完全相符。 將您的 NuGet 套件更新為您已安裝的相同延伸模組版本。

* **XLR002**： *Live 重載在從命令列建立時至少需要 ' MqttHostname ' 屬性。或者，將 ' EnableLiveReload ' 設定為 ' false ' 以停用此功能。*

  從命令列建立 (或持續整合) 中的時，無法使用即時重載所需的屬性，因此必須明確提供。 

* **XLR003**： *Live reload NuGet 套件需要安裝 Xamarin Live 重載 Visual Studio 擴充功能。*

  嘗試建立參考 Live Reload NuGet 封裝但未安裝視覺效果延伸模組的專案。  

* *載入元件時發生例外狀況： FileNotFoundException：無法載入元件 ' Xamarin. Reload.sql，Version = 0.3.27.0，Culture = 中性，PublicKeyToken = '。*

  主專案應使用， `PackageReference` 而不是 `packages.config`

### <a name="app-doesnt-connect"></a>應用程式未連接

建立應用程式時， **工具 > 選項 > Xamarin > 即時重載** (主機名稱、埠和加密金鑰) 內嵌于應用程式中，如此一來，當執行時 `LiveReload.Init()` ，連接就不需要進行配對或設定，就能成功連線。

除了一般網路問題外 (防火牆、不同網路) 的裝置，應用程式可能無法成功連線的主要原因是因為它的設定與 Visual Studio 中的不同。 這可能會在下列情況發生：

* 應用程式已在不同的電腦上進行編譯。
* 應用程式已在不同的 Visual Studio 會話中進行編譯和部署，並已核取 [ **自動產生加密金鑰** ] (**> Xamarin > Live 重載的 [工具] > 選項**] 中的預設) 。
* Visual Studio 設定已變更 (例如主機名稱、埠或加密金鑰) 但未重新建立並部署應用程式。

這些案例都是透過建立及部署應用程式來解決。

### <a name="uninstalling-preview-1"></a>正在卸載 Preview 1

如果您有較舊的預覽，但卸載時遇到問題，請遵循下列步驟：

1. ** (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload**中刪除資料夾 C:\Program 檔 (注意：將 "Enterprise" 取代為已安裝的版本，並將 "Preview" 取代為 "2017" （如果您已安裝到穩定的 VS) 
2. 開啟該 Visual Studio 的 **開發人員命令提示** 字元，然後執行 `devenv /updateconfiguration` 。 

## <a name="tips--tricks"></a>秘訣 & 訣竅

* 只要即時重載設定未變更 (包括加密金鑰（例如，如果您關閉 **自動產生加密金鑰**) 而且您是從同一部電腦建立），除非您變更程式碼或相依性，否則您不需要在初始部署之後建立並部署應用程式。 您可以重新開機先前部署的應用程式，它會連接到最後使用的主機。

* 您可以連接到相同 Visual Studio 會話的裝置數量沒有任何限制。 您可以視需要在多個裝置/模擬器中部署及啟動應用程式，以同時查看即時重載是否能在所有的時間上運作。

* Live Reload 只會重載您應用程式的使用者介面部分，但是 *它不會重新建立* 您的頁面，也不會取代您的視圖模型 (或系結內容) 。 這表示 *整個* 應用程式狀態一律會跨重載保留，包括您插入的相依性。

## <a name="live-reload-server"></a>Live Reload Server

在從執行中應用程式連線到您的電腦 (如使用 `localhost` 或 `127.0.0.1` 在 **工具中 > 選項 > Xamarin > Live 重載**) 不可能 (例如防火牆、不同的網路) ，您可以改為設定遠端伺服器，而這兩個 IDE 和應用程式都會連線到。

Live Reload 使用標準 [MQTT 通訊協定](https://mqtt.org/) 來交換訊息，因此可以與 [協力廠商伺服器](https://github.com/mqtt/mqtt.github.io/wiki/servers)通訊。 甚至還有 [公用伺服器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (也稱為「 *代理* 程式) 可供您使用。 Live 重載已使用 `broker.hivemq.com` 和 `iot.eclipse.org` 主機名稱進行測試，以及 [www.cloudmqtt.com](https://www.cloudmqtt.com) 和 [www.cloudamqp.com](https://www.cloudamqp.com)所提供的服務。 您也可以在雲端中部署自己的 MQTT 伺服器，例如 [Azure 上的 HiveMQ](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud)。

您可以設定任何埠，但通常會使用遠端伺服器的預設1883埠。 即時重載訊息使用強大的端對端 AES 對稱式加密，因此可以安全地連線到遠端伺服器。 根據預設，會在每個 Visual Studio 會話上重新產生加密金鑰和初始化向量 (IV) 。

最簡單的方式是在 Azure 中的空白 Ubuntu VM 上安裝 [mosquitto](https://mosquitto.org) 伺服器：

1. 在 Azure 入口網站中建立新的 Ubuntu Server VM
2. 在 [網路] 索引標籤中新增 1883 (預設 MQTT 埠) 的輸入連接埠規則
3. 開啟 [Cloud Shell](/azure/cloud-shell/overview) (bash 模式) 
4. `ssh [USERNAME]@[PUBLIC_IP]`使用您在 1) 中選擇的使用者名稱，以及您 VM 總覽頁面中顯示的公用 IP 輸入
5. 執行 `sudo apt-get install mosquitto` ，輸入您在1中選擇的密碼) 

現在您可以使用該 IP 連接到您自己的 MQTT 伺服器。