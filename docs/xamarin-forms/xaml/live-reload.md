---
title: 即時重新載入
description: 請參閱變更您的 XAML 反映即時的而不需要其他的編譯和部署。
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 05/11/2018
ms.openlocfilehash: f05b79e58a9fa87e13aeb45af2dbb46139051005
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109987"
---
# <a name="xamarin-live-reload"></a>Xamarin 即時重新載入

![預覽](~/media/shared/preview.png)

Xamarin 即時重新載入可讓您**對您的 XAML 中的變更並看到其反映即時的而不需要其他的編譯及部署**。 將要重新部署您的 XAML 所做的變更上儲存，並反映在您的部署目標上。

使用即時重新載入時，會編譯您的應用程式，因為它適用於所有的程式庫和協力廠商控制項。 即時重新載入適用於所有平台 Xamarin.Forms 支援，包括 Android、 iOS 和 UWP 和適用於所有有效的部署目標，包括模擬器，模擬器，以及實體裝置上。

> [!NOTE]
> [在即時重新載入預覽已結束](https://github.com/xamarin/Xamarin.Forms/issues/4155#issuecomment-431596130)
>
> 在沒有討論這[ ![，參加在聊天室 https://gitter.im/xamarin/live-reload](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/xamarin/live-reload?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## <a name="requirements"></a>需求

* [Visual Studio 2017 15.7 版或更新版本](https://visualstudio.microsoft.com/vs/)具有**使用.NET 進行行動開發**工作負載。
* [Xamarin.Forms 3.0.0 以上](https://www.nuget.org/packages/Xamarin.Forms/)。

## <a name="getting-started"></a>快速入門
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1.從 Visual Studio Marketplace 安裝 Xamarin 即時重新載入

Xamarin 即時重新載入 Visual Studio marketplace 發佈。 若要安裝擴充功能，請造訪[Visual Studio Marketplace 上的 Xamarin 即時重新載入頁面](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload)網站並按一下 **下載**。

開啟已下載完畢，然後按一下.vsix**安裝**。

![Visual Studio 安裝程式確認 Xamarin Live 重新載入](images/LiveReloadVSIXInstall.png)

或者，您可以搜尋在**線上**索引標籤中**擴充功能和更新**在 Visual Studio 內的對話方塊。

### <a name="2-configure-your-app-to-use-live-reload"></a>2.設定您的應用程式使用即時重新載入

即時重新載入加入現有的行動裝置應用程式可在三個步驟：

1. 請確定所有的專案已更新為使用[Xamarin.Forms 3.0.0 以上](https://www.nuget.org/packages/Xamarin.Forms/)或更新版本。

2. 新增**Xamarin.LiveReload** NuGet 套件：

    a. **.NET standard** – 安裝**Xamarin.LiveReload**到您的.NET Standard 2.0 程式庫的 NuGet。 這不需要安裝在您的平台專案。 請確認**套件來源**設為**所有**。
    
    b. **共用專案**– 安裝**Xamarin.LiveReload** NuGet 的所有平台專案 （例如 Android、 iOS、 UWP 等）。 請確認**套件來源**設為**所有**。

    [![將 Xamarin 即時重新載入 NuGet 使用 NuGet 封裝管理員新增](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. 新增`LiveReload.Init();`建構函式中`Application`類別，如下列程式碼片段所示：

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

### <a name="3-start-live-reloading"></a>3.啟動即時重新載入

編譯及部署您的應用程式。 已部署應用程式後，開啟 XAML 檔案、 進行一些變更，並儲存檔案。 部署目標來重新部署您的變更。

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

即時重新載入適用於任何 XAML 檔案的變更。 C# 或加入/移除 NuGet 套件的變更需要新的組建和部署才會生效。

## <a name="frequently-asked-questions"></a>常見問題集 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>位於 Xamarin 即時重新載入 Visual Studio for Mac 嗎？ 

Xamarin 即時重新載入的初始預覽版本僅適用於 Visual Studio 2017。 適用於 Visual Studio for Mac 的支援已規劃在未來的版本。

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>這與所有的程式庫，例如 Prism 運作？ 

因為您的應用程式編譯時，即時重新載入適用於所有程式庫，例如 Prism 和協力廠商控制項程式庫，例如 Telerik、 Infragistics、 Syncfusion，ArcGIS，GrapeCity 和其他控制項廠商。

### <a name="what-changes-does-live-reload-redeploy"></a>即時重新載入重新部署的哪些變更？ 

即時重新載入只適用於 XAML 或 CSS 所做的變更。 如果您變更 C# 檔案時，就必須使用重新編譯。 重新載入 C# 的支援已規劃在未來的版本。

### <a name="what-platforms-are-supported"></a>支援哪些平台？ 

即時重新載入適用於任何平台包括 Android、 iOS 和 UWP 的 Xamarin.Forms 所支援。

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>這在模擬器，模擬器和實體裝置上運作？ 

是，即時重新載入適用於所有有效的部署目標，包括 Android 模擬器、 iOS 模擬器和實體裝置。 部署到裝置，必須在裝置與電腦相同的 Wi-fi 網路上。

### <a name="does-this-work-with-corporate-networks"></a>這與公司網路運作？

如果您正在偵錯的 Android 模擬器或 iOS 模擬器，即時重新載入會使用 localhost 進行通訊。 如果您想要部署至裝置，必須位於相同的 Wi-fi 網路上的裝置和電腦。 在其中不可行的情況下，您可以[設定您自己的即時重新載入伺服器](#live-reload-server)，這可讓您以即時重新載入，不論網路連線設定。

### <a name="does-it-require-debugging-the-app"></a>它需要偵錯應用程式嗎？ 

否。 事實上，您甚至可以任意數目的裝置或模擬器/模擬器上啟動所有受支援的應用程式鎖定 （Android、 iOS 及 UWP），並查看它們全部一次更新。 

## <a name="limitations"></a>限制

* 只重新載入的 XAML 支援。
* 除非使用 MVVM UI 狀態可能無法維護重新部署，之間。

## <a name="known-issues"></a>已知問題

* 只有在 Visual Studio 中。
* 連結必須設為**不要連結**或**僅連結 Framework Sdk** 
* 重新載入整個應用程式的資源 (亦即**App.xaml**或共用資源字典)，就會重設應用程式瀏覽。 這將會在下一個預覽版本中修正。
* 重新載入 ContentView 目前需要重新載入包含的頁面。 這將會在下一個預覽版本中修正。
* 包含 AutomationId 的項目可能會造成重新載入失敗。
* 編輯 XAML，而偵錯 UWP 可能會導致執行階段當機。 因應措施： 使用**但不偵錯 （Ctrl + F5） 開始**而不是**開始偵錯 (F5)**。

## <a name="troubleshooting"></a>疑難排解

### <a name="error-codes"></a>錯誤碼

* **XLR001**:*目前的專案參考 'Xamarin.LiveReload' NuGet 套件版本 [版本]，但 Xamarin 即時重新載入延伸模組需要版本 [版本]。*

  若要允許快速反覆項目 」 和 「 即時重新載入功能的演進，必須完全符合 nuget 和 Visual Studio 擴充功能。 您已安裝的延伸模組相同的版本更新您的 nuget 套件。

* **XLR002**:*即時重新載入至少需要 'MqttHostname' 屬性時從命令列建置。或者，設定 'EnableLiveReload' 為 'false' 會停用此功能。*

  即時重新載入所需的屬性時，就無法使用建置從命令列 （或在持續整合），並因此必須明確提供。 

* **XLR003**:*即時重新載入 nuget 套件需要安裝 Xamarin Live 重新載入 Visual Studio 延伸模組。*

  嘗試建置專案，參考即時重新載入 nuget 套件，但未安裝 Visual 的擴充功能。  

* *載入組件時發生例外狀況： System.IO.FileNotFoundException： 無法載入組件 ' Xamarin.Live.Reload，版本 = 0.3.27.0，Culture = neutral，PublicKeyToken ='。*

  應該使用主專案`PackageReference`而不是 `packages.config`

### <a name="app-doesnt-connect"></a>應用程式未連線

應用程式建置時的資訊**工具 > 選項 > Xamarin > 即時重新載入**（主機名稱、 連接埠和加密金鑰） 會嵌入應用程式，因此，當`LiveReload.Init()`執行時，任何配對或設定為需要連接才會成功。

非一般網路問題 （防火牆，在不同的網路上的裝置），應用程式可能無法順利連線 IDE 的主要原因是因為它的組態不同於在 Visual Studio 中。 這可能會在：

* 一部電腦上編譯應用程式。
* 已編譯應用程式，並將其部署在不同的 Visual Studio 工作階段，並**自動產生加密金鑰**是簽入 （預設值）**工具 > 選項 > Xamarin > 即時重新載入**。
* Visual Studio 設定已變更 （也就是主機名稱、 連接埠或加密金鑰），但不是建置並再次部署應用程式。

這些情況下會建置並重新部署應用程式解決。

### <a name="uninstalling-preview-1"></a>解除安裝 Preview 1

如果您有較舊的預覽，而且已解除安裝的問題，請遵循下列步驟：

1. 刪除資料夾**C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (注意： 您已安裝的版本，以取代 「 企業 」 和 「 預覽 」 「 2017"如果您安裝到穩定的 VS）
2. 開啟**開發人員命令提示字元**適用於該 Visual Studio 和執行`devenv /updateconfiguration`。 

## <a name="tips--tricks"></a>秘訣與技巧

* 只要即時重新載入設定不會變更 (包括加密金鑰，例如，如果您關閉**自動產生加密金鑰**) 和您從在同一部電腦進行建置，您不需要建置及部署應用程式完成初始部署，除非您變更程式碼或相依性。 您可以只是再次啟動先前已部署的應用程式與它連接到最後一個使用的主機。

* 您可以連接到相同的 Visual Studio 工作階段的裝置數目沒有限制。 您可以部署，並在多個裝置/模擬器中啟動應用程式所需的全部查看即時重新載入工作，在相同的時間。

* 即時重新載入只會重新載入您的應用程式的使用者介面部分，但的確*不*重新建立您的頁面中，都不會它將檢視模型 （或繫結內容）。 這表示*整個*應用程式狀態一定會保留在重新載入，包括您插入的相依性。

## <a name="live-reload-server"></a>即時重新載入伺服器

在案例中從執行中應用程式連接到您的電腦 (使用表示`localhost`或`127.0.0.1`中**工具 > 選項 > Xamarin > 即時重新載入**) 不可能 （也就是防火牆，不同的網路）您可以設定遠端伺服器而是 IDE 和應用程式會連線到。

即時重新載入使用標準[MQTT 通訊協定](http://mqtt.org/)來交換訊息，並因此可以與[第三方伺服器](https://github.com/mqtt/mqtt.github.io/wiki/servers)。 甚至也有[公用伺服器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers)(也稱為*broker*) 可用，您可以使用。 即時重新載入經過`broker.hivemq.com`並`iot.eclipse.org`主機名稱，以及所提供的服務[www.cloudmqtt.com](https://www.cloudmqtt.com)並[www.cloudamqp.com](https://www.cloudamqp.com)。 您也可以如部署在雲端中，您自己 MQTT 伺服器[在 Azure 上的 HiveMQ](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud)。

您可以設定任何連接埠，但通常會使用預設 1883年的連接埠的遠端伺服器。 即時重新載入訊息使用強式端對端 AES 對稱加密，以便安全地連接到遠端伺服器。 根據預設，加密金鑰和初始化向量 (IV) 會產生每個 Visual Studio 工作階段上。

可能的最簡單方式是安裝[mosquitto](https://mosquitto.org)中空白的 Ubuntu VM，在 Azure 中的伺服器：

1. 在 Azure 入口網站中建立新的 Ubuntu Server VM
2. 1883 （預設 MQTT 連接埠） 在 [網路] 索引標籤中加入新的輸入連接埠規則
3. 開啟[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (bash 模式)
4. 輸入`ssh [USERNAME]@[PUBLIC_IP]`使用您選擇 1 中的使用者名稱) 和 VM 的 [概觀] 頁面中顯示的公用 IP
5. 執行`sudo apt-get install mosquitto`，輸入您在 1 中選擇的密碼)

現在您可以使用該 IP 來連線至 MQTT 伺服器。
