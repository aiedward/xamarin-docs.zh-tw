---
title: Xamarin Live Reload （預覽）
description: 查看即時反映的 XAML 變更，而不需要另一個編譯和部署。
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: a5a5a9acf47603601461660df689a7a5fa6aee00
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728339"
---
# <a name="xamarin-live-reload-preview"></a>Xamarin Live Reload （預覽）

> [!NOTE]
> Xamarin Live Reload 的預覽已結束，我們想要感謝每個人提供您的意見與意見。 請閱讀我們 Visual Studio 2019 的[藍圖](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap)，以深入瞭解我們在 Xamarin. 表單中使用的新生產力功能。 此延伸模組將繼續提供 Visual Studio 2017，但不會接收未來的更新。

Xamarin Live Reload 可讓您**變更 XAML 並查看其即時反映，而不需要另一個編譯和部署**。 對 XAML 所做的任何變更都會在儲存時重新部署，並反映在部署目標上。

## <a name="requirements"></a>需求

* 使用 .NET 的行動裝置**開發**工作負載， [Visual Studio 2017 15.7 版或更新版本](https://visualstudio.microsoft.com/vs/)。
* [3.0.0 或更高](https://www.nuget.org/packages/Xamarin.Forms/)版本。

## <a name="getting-started"></a>使用者入門
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. 從 Visual Studio Marketplace 安裝 Xamarin Live Reload

Xamarin Live Reload 會透過 Visual Studio Marketplace 散發。 若要安裝此擴充功能，請造訪[Visual Studio Marketplace 網站上的 Xamarin Live Reload 頁面](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload)，然後按一下 [**下載**]。

開啟已下載的 .vsix，然後按一下 [**安裝**]。

![Visual Studio installer Xamarin 即時重載確認](images/LiveReloadVSIXInstall.png)

或者，您可以在 Visual Studio 內 [**擴充功能和更新**] 對話方塊的 [**線上**] 索引標籤中搜尋它。

### <a name="2-configure-your-app-to-use-live-reload"></a>2. 將您的應用程式設定為使用 Live Reload

將即時重載新增至現有的行動應用程式可以透過三個步驟來完成：

1. 請確認所有專案都已更新，可使用[3.0.0 或以上](https://www.nuget.org/packages/Xamarin.Forms/)版本或更高版本。

2. 新增**LiveReload** NuGet 套件：

    a. **.NET Standard** –將**LiveReload** NuGet 安裝到您的 .NET Standard 2.0 程式庫。 這不需要安裝在您的平臺專案中。 確定 [**套件來源**] 設定為 [**全部**]。
    
    b. **共用專案**–將**LiveReload** NuGet 安裝到所有平臺專案（例如 ANDROID、iOS、UWP 等等）。 確定 [**套件來源**] 設定為 [**全部**]。

    [![使用 NuGet 套件管理員新增 Xamarin Live 重載 NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. 將 `LiveReload.Init();` 新增至 `Application` 類別中的函式，如下列程式碼片段所示：

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

編譯和部署您的應用程式。 部署應用程式之後，請開啟 XAML 檔案，進行一些變更，然後儲存檔案。 您的變更會重新部署到部署目標。

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live Reload 會與任何 XAML 檔案的變更搭配運作。 變更C#或新增/移除 NuGet 套件需要新的組建，且部署才會生效。

## <a name="frequently-asked-questions"></a>常見問題集 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Xamarin Live Reload 可以在 Visual Studio for Mac 上取得嗎？ 

否，Xamarin Live Reload 的預覽版本僅適用于 Visual Studio 2017。

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>這適用于所有程式庫，例如 Prism 嗎？ 

因為您的應用程式已編譯，所以 Live Reload 會與所有程式庫（例如 Prism）和協力廠商控制程式庫（例如 Telerik、Infragistics、Syncfusion、ArcGIS、GrapeCity 和其他控制廠商）搭配運作。

### <a name="what-changes-does-live-reload-redeploy"></a>Live Reload 重新部署有哪些變更？ 

Live Reload 只會套用對 XAML 或 CSS 所做的變更。 如果您對檔案進行變更C# ，則需要重新編譯。 

### <a name="what-platforms-are-supported"></a>支援哪些平臺？ 

Live Reload 適用于 Xamarin 所支援的任何平臺，包括 Android、iOS 和 UWP。

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>這適用于模擬器、模擬器和實體裝置嗎？ 

是的，Live Reload 適用于所有有效的部署目標，包括 Android 模擬器、iOS 模擬器和實體裝置。 裝置和電腦必須位於相同的 Wi-fi 網路上，才能部署至裝置。

### <a name="does-this-work-with-corporate-networks"></a>這會與公司網路搭配使用嗎？

如果您要對 Android 模擬器或 iOS 模擬器進行偵錯工具，Live Reload 會使用 localhost 進行通訊。 如果您想要部署至裝置，裝置和電腦必須位於相同的 Wi-fi 網路上。 在不可行的情況下，您可以[設定自己的即時重載伺服器](#live-reload-server)，讓您可以即時重載，而不論網路連線設定為何。

### <a name="does-it-require-debugging-the-app"></a>是否需要對應用程式進行偵錯工具？ 

No。 事實上，您甚至可以在任意數目的裝置或模擬器/模擬器上啟動所有支援的應用程式目標（Android、iOS 和 UWP），並一次查看所有的更新。 

## <a name="limitations"></a>限制

* 僅支援重載 XAML。
* 除非使用 MVVM，否則重新部署之間可能不會維護 UI 狀態。

## <a name="known-issues"></a>已知問題

* 僅 Visual Studio 支援。
* 連結必須設定為**不連結**或**僅連結 Framework sdk** 
* 重載應用程式的資源（也就是**應用程式 .xaml**或共用的資源字典），應用程式導覽會重設。 
* 重載 ContentView 目前需要重載包含的頁面。
* 包含 AutomationId 的元素可能會導致重載失敗。
* 在偵測 UWP 時編輯 XAML 可能會導致執行時間損毀。 因應措施：使用 **[啟動但不進行偵測] （Ctrl + F5）** ，而不是**開始調試（F5）** 。

## <a name="troubleshooting"></a>疑難排解

### <a name="error-codes"></a>錯誤碼

* **XLR001**：*目前的專案參考 ' LiveReload ' NuGet 套件版本 ' [版本] '，但 Xamarin Live 重載延伸模組需要版本 ' [版本] '。*

  為了讓即時重載功能能夠快速反復反覆運算和進化，NuGet 封裝和 Visual Studio 延伸模組必須完全相符。 將您的 NuGet 套件更新至您已安裝的相同延伸模組版本。

* **XLR002**： *Live Reload 從命令列建立時，至少需要 ' MqttHostname ' 屬性。或者，將 ' EnableLiveReload ' 設定為 ' false ' 以停用此功能。*

  從命令列（或連續整合）中建立時，無法使用 Live Reload 所需的屬性，因此必須明確地提供。 

* **XLR003**： *Live reload NuGet 套件需要安裝 Xamarin Live reload Visual Studio 延伸模組。*

  嘗試建立參考即時重載 NuGet 套件的專案，但未安裝視覺效果延伸模組。  

* *載入元件時發生例外狀況： FileNotFoundException：無法載入元件 ' Xamarin. 重載，版本 = 0.3.27.0，文化特性 = 中性，PublicKeyToken = '。*

  主專案應該使用 `PackageReference`，而不是 `packages.config`

### <a name="app-doesnt-connect"></a>應用程式未連接

建立應用程式時，[**工具] > 選項 > Xamarin > Live Reload** （主機名稱、埠和加密金鑰）的資訊會內嵌在應用程式中，因此當 `LiveReload.Init()` 執行時，連線就不需要進行配對或設定，連線就會成功。

除了一般網路問題（防火牆、其他網路上的裝置），應用程式可能無法成功連接 IDE 的主要原因是它的設定與 Visual Studio 中的不同。 這可能發生在下列情況：

* 應用程式已在不同的電腦上編譯。
* 應用程式已在不同的 Visual Studio 會話中編譯和部署，而且在 [**工具 > 選項] > [Xamarin] > [即時重載**] 中，會核取 [**自動產生加密金鑰**] （預設值）。
* Visual Studio 設定已變更（也就是主機名稱、埠或加密金鑰），但未重新建立及部署應用程式。

這些案例都是透過重新建立及部署應用程式來解決。

### <a name="uninstalling-preview-1"></a>卸載 Preview 1

如果您有較舊的預覽，但卸載時發生問題，請遵循下列步驟：

1. 刪除資料夾**C:\Program Files （x86） \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** （注意：以您已安裝的版本取代 "Enterprise"，如果您安裝到穩定的 VS，則以 "2017" 取代 "Preview"）
2. 開啟該 Visual Studio 的**開發人員命令提示**字元，然後執行 `devenv /updateconfiguration`。 

## <a name="tips--tricks"></a>秘訣和竅門

* 只要即時重載設定不會變更（包括加密金鑰，例如，如果您關閉**自動產生加密金鑰**），而且您是從相同的電腦建立，除非您變更程式碼或相依性，否則您不需要在初始部署之後建立及部署應用程式。 您只要重新開機先前部署的應用程式，它就會連接到最後使用的主機。

* 您可以連接到相同 Visual Studio 會話的裝置數目並無任何限制。 您可以視需要在多個裝置/模擬器中部署和啟動應用程式，以查看在同一時間內進行的即時重載。

* Live Reload 只會重載應用程式的使用者介面部分，*但不會重新建立*您的頁面，而不會取代您的視圖模型（或系結內容）。 這表示*整個*應用程式狀態一律會保留在重載之間，包括您插入的相依性。

## <a name="live-reload-server"></a>Live Reload 伺服器

在從執行中的應用程式連線到您的電腦的情況下（例如，使用 `localhost` 或 `127.0.0.1` 在 [工具] [ **> 即時重載] 的 [工具] > > 選項**中所表示）不可行（也就是防火牆、不同的網路），您可以設定遠端伺服器，而這兩個 IDE 和應用程式將會連線到。

Live Reload 會使用標準[MQTT 通訊協定](https://mqtt.org/)來交換訊息，因此可以與[協力廠商伺服器](https://github.com/mqtt/mqtt.github.io/wiki/servers)通訊。 您甚至可以使用[公用伺服器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers)（也稱為「訊息*代理*程式」）。 Live Reload 已經過 `broker.hivemq.com` 和 `iot.eclipse.org` 主機名稱，以及[www.cloudmqtt.com](https://www.cloudmqtt.com)和[www.cloudamqp.com](https://www.cloudamqp.com)所提供的服務進行測試。 您也可以在雲端中部署自己的 MQTT 伺服器，例如[Azure 上的 HiveMQ](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud)。

您可以設定任何通訊埠，但通常會使用遠端伺服器的預設1883埠。 即時重載訊息使用強式端對端 AES 對稱式加密，因此可以安全地連接到遠端伺服器。 根據預設，加密金鑰和初始化向量（IV）都會在每個 Visual Studio 會話上重新產生。

可能最簡單的方式是在 Azure 中的空白 Ubuntu VM 上安裝[mosquitto](https://mosquitto.org)伺服器：

1. 在 Azure 入口網站中建立新的 Ubuntu Server VM
2. 在 [網路功能] 索引標籤中新增1883（預設 MQTT 埠）的新輸入連接埠規則
3. 開啟[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) （bash 模式）
4. 使用您在1中選擇的使用者名稱，輸入 `ssh [USERNAME]@[PUBLIC_IP]`，並在您的 VM [總覽] 頁面中顯示公用 IP
5. 執行 `sudo apt-get install mosquitto`，並輸入您在1中選擇的密碼）

現在您可以使用該 IP 連接到您自己的 MQTT 伺服器。
