---
title: 疑難排解 Xamarin 即時 Player
description: 本文件說明 Xamarin Live Player 和可能的修正方法的已知的問題。 它討論的連線問題、 組態問題，以及更多。
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: 3db14db2c64e024ef1c04275661f610f9407dfb7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793751"
---
# <a name="troubleshooting-xamarin-live-player"></a>疑難排解 Xamarin 即時 Player

![預覽功能](~/media/shared/preview.png)

本文說明一些常見的問題，並提供的步驟來更正它們。

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>行動裝置不會掃描條碼 （或輸入的程式碼） 之後，連線

發生於執行 Xamarin Live 播放程式的行動裝置不在相同的網路執行 IDE 的電腦上。 請參閱下列：

- 確認裝置和電腦都位於相同的 Wi-fi 網路。
  - 如果電腦連接到有線網路，請嘗試拔除有線的連線。
- 網路可能會受到嚴密保護 （例如某些公司網路中），封鎖 Xamarin Live 播放程式所需的連接埠。
- 關閉 Xamarin Live 播放器應用程式，然後重新啟動它。

## <a name="error-while-trying-to-deploy-message-in-ide"></a>在 IDE 中的 「 嘗試部署時的錯誤 」 訊息

**「 IOException： 無法讀取傳輸連線的資料： 非封鎖通訊端上的作業會被封鎖 」**

執行 Xamarin Live 播放程式的行動裝置不在執行 Visual Studio; 的電腦相同的網路上時經常遇到這個錯誤這通常會連接到先前已成功配對的裝置。

* 請檢查裝置和電腦都位於相同的 Wi-fi 網路。
* 網路可能會受到嚴密保護 （例如某些公司網路中），封鎖 Xamarin Live 播放程式所需的連接埠。 下列連接埠所需的 Xamarin Live Player:
  * 37847 – 內部網路存取權 
  * 8090 – 外部網路存取

## <a name="manually-configure-device"></a>手動設定裝置

如果您無法透過 Wi-fi 連線到您的裝置可以嘗試手動設定您的裝置，透過組態檔中，執行下列步驟：

**步驟 1： 開啟組態檔**

前往您的應用程式儲存資料夾：

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

在這個資料夾中，您會發現**PlayerDeviceList.xml**不存在，如果您必須建立一個。

**步驟 2： 取得 IP 位址**

在 Xamarin Live 播放器應用程式中，移至**有關 > 連線測試 > 啟動連線測試**。

記下 IP 位址，您必須列出當您設定您的裝置的 IP 位址。

**步驟 3： 取得配對的程式碼**

Xamarin Live Player 點選內**組**或**再次組**，然後按下**手動輸入**。 數字代碼將會顯示，您必須更新組態檔。

**步驟 4： 產生的 GUID**

移至：https://www.guidgenerator.com/online-guid-generator.aspx並產生新的 guid，並確定在大寫。

**步驟 5： 設定裝置**

開啟  **PlayerDeviceList.xml**向上例如 Visual Studio 或 Visual Studio Code 編輯器中。 您需要此檔案中手動設定您的裝置。 根據預設，此檔案應該包含下列空白`Devices`XML 項目：

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**加入 iOS 裝置：**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>iPhone Player</Name>
<Platform>iOS</Platform>
<AndroidApiLevel>0</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:36:03.9492291Z</LastConnectTimeUtc>
</PlayerDevice>
```

**新增 Android 裝置：**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**關閉並重新開啟 Visual Studio。** 您的裝置應該顯示在清單中。

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>「 型別或命名空間無法找 」 訊息在 IDE 中

您已選取的核取**啟始專案**符合您的裝置類型 （iOS 或 Android） 和組態符合該裝置類型 （例如。 **偵錯 | iPhone 模擬器**適用於 iOS)。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>在 Player 中的 「 型別 'InterpretedXamarin.Forms.Button' 找不到建構函式 」 訊息

有些系統類別不能覆寫，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>「 Weatherapp: 'Resource.Layout' 不包含定義的 'Main' 」

具有 AXML 檔案中定義的使用者介面，針對 Android 專案會發生這個錯誤。
AXML 檔案目前不支援 Xamarin Live 播放程式中。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 的工具列和索引標籤呈現不正確地使用 Xamarin.Forms

Xamarin.Forms Android 專案必須使用 「 Toolbar.axml"和"Tabbar.axml 」 相關的配置檔案的名稱。 預設範本會使用這些名稱。重新命名會造成呈現問題。

請將任何其他的問題報告上[bugzilla](https://aka.ms/live-player-report-issue)。

## <a name="related-links"></a>相關連結

- [限制](~/tools/live-player/limitations.md)
- [安裝](~/tools/live-player/install.md)
