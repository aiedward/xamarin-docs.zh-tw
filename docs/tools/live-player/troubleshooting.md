---
title: 疑難排解 Xamarin Live Player
description: 本文件說明 Xamarin Live Player 和可能的修正的已知的問題。 它討論的連線問題、 設定問題，和更多功能。
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: eb9d758d72febe0fc0b705d66246c99ade1fc80f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109688"
---
# <a name="troubleshooting-xamarin-live-player"></a>疑難排解 Xamarin Live Player

![預覽功能](~/media/shared/preview.png)

這篇文章會說明一些常見的問題，並提供更正它們的步驟。

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>行動裝置不會連線之後掃描的條碼 （或輸入的程式碼）

發生於執行 Xamarin Live Player 的行動裝置不在相同的網路執行 IDE 之電腦上。 請參閱下列：

- 確認裝置和電腦都位於相同的 Wi-fi 網路。
  - 如果電腦也會連線到有線網路，請嘗試拔除有線的連線。
- 網路可能會受到嚴密保護 （例如，有些公司網路），封鎖連接埠所需的 Xamarin Live Player。
- 關閉 Xamarin Live Player 應用程式，然後重新啟動它。

## <a name="error-while-trying-to-deploy-message-in-ide"></a>在 IDE 中的 「 嘗試部署時發生錯誤 」 訊息

**「 IOException： 無法從傳輸連線讀取資料： 在非封鎖通訊端上的作業會被封鎖 」**

行動裝置執行 Xamarin Live Player 未在執行 Visual Studio; 的電腦相同的網路時，通常被發生此錯誤這通常會連接到先前成功配對的裝置。

* 請檢查裝置和電腦都位於相同的 Wi-fi 網路。
* 網路可能會受到嚴密保護 （例如，有些公司網路），封鎖連接埠所需的 Xamarin Live Player。 下列連接埠所需的 Xamarin Live Player 的：
  * 37847 – 內部網路存取權 
  * 8090 – 外部網路存取

## <a name="manually-configure-device"></a>手動設定裝置

如果您無法透過 Wi-fi 連線到您的裝置可以嘗試手動設定您的裝置設定檔中，透過下列步驟：

**步驟 1： 開啟組態檔**

前往您的應用程式儲存資料夾：

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

在此資料夾中，您會發現**PlayerDeviceList.xml**不存在，如果您必須建立一個。

**步驟 2： 取得 IP 位址**

在 Xamarin Live Player 應用程式中，移至**關於 > 連線測試 > 測試啟動的連線**。

記下的 IP 位址，則需要列出當您設定您的裝置的 IP 位址。

**步驟 3： 取得配對的程式碼**

在 Xamarin Live Player 點選**配對**或**再次配對**，然後按**手動輸入**。 數字的程式碼將會顯示，您必須更新組態檔。

**步驟 4： 產生的 GUID**

移至： https://www.guidgenerator.com/online-guid-generator.aspx 並產生新的 guid，並確定在大寫。

**步驟 5： 設定裝置**

開啟**PlayerDeviceList.xml**例如 Visual Studio 或 Visual Studio 程式碼編輯器中。 您需要手動設定您的裝置，此檔案中。 根據預設，此檔案應包含下列空白`Devices`XML 項目：

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
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

**關閉並重新開啟 Visual Studio。** 您的裝置應該會顯示在清單中。

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>在 IDE 中的 「 型別或命名空間無法找到 」 訊息

您已選取的核取**啟始專案**符合您的裝置類型 （例如。 Android) 和設定與該裝置類型 （例如。 **偵錯**適用於 Android)。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>在 Player 中的 「 型別 'InterpretedXamarin.Forms.Button' 找不到上的建構函式 」 訊息

某些系統類別無法覆寫，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>「 MainActivity.cs: 'Resource.Layout' 不包含 'Main' 的定義 」

使用 AXML 檔案中定義的使用者介面，Android 專案會發生此錯誤。
AXML 檔案目前不支援在 Xamarin Live Player。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 工具列及索引標籤會轉譯不正確地使用 Xamarin.Forms

Xamarin.Forms Android 專案必須使用 「 Toolbar.axml"和"Tabbar.axml 」 相關的版面配置檔案的名稱。 預設範本會使用這些名稱;重新命名會造成轉譯問題。

請上回報的任何其他問題[bugzilla](https://aka.ms/live-player-report-issue)。

## <a name="related-links"></a>相關連結

- [限制](~/tools/live-player/limitations.md)
- [安裝](~/tools/live-player/install.md)
