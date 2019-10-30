---
title: 疑難排解 Xamarin Live Player
description: 本檔說明 Xamarin Live Player 和可能的修正程式的已知問題。 其中討論連線問題、設定問題等等。
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d51241bee5f4ddc06032006071fa8296be37f2fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005937"
---
# <a name="troubleshooting-xamarin-live-player"></a>疑難排解 Xamarin Live Player

![預覽功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 預覽已結束。 應用程式已無法再使用。 下列指示會提供給繼續使用預覽的客戶 Visual Studio 2017。

> [!TIP]
> 您可以使用 Visual Studio 2019 或 Visual Studio for Mac 中的[XAML 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)程式來查看您在編輯時的畫面設計。

本文說明 Live Player 的限制，以及修正這些問題的一些常見問題。

## <a name="limitations-of-xamarin-live-player"></a>Xamarin Live Player 的限制

### <a name="ide-requirements"></a>IDE 需求

Live Player 預覽僅適用于 Visual Studio 2017。

### <a name="device-requirements"></a>裝置需求

Xamarin Live Player 應用程式支援下列 Android 裝置：

- Android 4.2 或更新版本。
- ARM-armeabi-v7a、ARM-arm64-v8a、ARM64-arm64-v8a、x86 或 x86_64 處理器。

### <a name="ios-limitations"></a>iOS 限制

IOS 無法使用 Live Player。

### <a name="xamarinforms-limitations"></a>Xamarin. 表單限制

- 不支援自訂轉譯器。
- 不支援效果。
- 不支援具有自訂可系結屬性的自訂控制項。
- 不支援內嵌資源（即在 PCL 中嵌入影像或其他資源）。
- 不支援協力廠商 MVVM 架構（ie）。Prism、Mvvm Cross、Mvvm Light 等等）。

### <a name="other-project-type-limitations"></a>其他專案類型限制

- Live Player 不適用於原生 Android 專案（針對使用者介面使用 Android XML）。

### <a name="miscellaneous-limitations"></a>其他限制

- 反映的有限支援（目前會影響一些熱門的 Nuget，例如 SQLite 和 Json.NET）。 可能仍然支援其他 Nuget。
- 某些系統類別無法覆寫（例如，您無法執行子類別）。
- 某些需要布建的平臺功能無法在 Xamarin Live Player 應用程式中運作（但已針對相片圖庫存取之類的一般作業設定）。
- 已忽略自訂目標和組建步驟。 例如，Fody、重新調整、AutoFac 和 AutoMapper 等工具無法合併。
- F#不支援專案
- 可能不支援具有自訂泛型類別和介面的 Advanced 案例。

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>行動裝置在掃描條碼（或輸入程式碼）後不會連接

當執行 Xamarin Live Player 的行動裝置與執行 IDE 的電腦不在相同的網路上時發生。 查看下列內容：

- 確認裝置和電腦都在相同的 Wi-fi 網路上。
  - 如果電腦也連接到有線網路，請嘗試撥出有線連線。
- 網路可能會受到嚴密保護（例如某些公司網路），而封鎖 Xamarin Live Player 所需的埠。
- 關閉 Xamarin Live Player 應用程式，然後將它重新開機。

## <a name="error-while-trying-to-deploy-message-in-ide"></a>IDE 中的「嘗試部署時發生錯誤」訊息

**「IOException：無法從傳輸連線讀取資料：非封鎖通訊端上的作業會封鎖」**

當執行 Xamarin Live Player 的行動裝置與執行 Visual Studio 的電腦不在相同的網路上時，通常會發生此錯誤;這通常會在連接到先前已成功配對的裝置時發生。

- 檢查裝置和電腦都在相同的 Wi-fi 網路上。
- 網路可能會受到嚴密保護（例如某些公司網路），而封鎖 Xamarin Live Player 所需的埠。 Xamarin Live Player 需要下列埠：
  - 37847–內部網路存取 
  - 8090–外部網路存取

## <a name="manually-configure-device"></a>手動設定裝置

如果您無法透過 Wi-fi 連線到您的裝置，您可以透過下列步驟，嘗試透過設定檔案手動設定您的裝置：

**步驟1：開啟設定檔案**

前往您的應用程式資料檔案夾：

- Windows： **%userprofile%\AppData\Roaming**
- macOS： **~/Users/$USER/.config**

在此資料夾中，您會發現 PlayerDeviceList 不存在，您必須建立一個 **.xml** 。

**步驟2：取得 IP 位址**

在 Xamarin Live Player 應用程式中，移至 [**關於 > 連線測試] > [啟動連線測試**]。

記下 IP 位址，您將需要在設定裝置時所列出的 IP 位址。

**步驟3：取得配對程式碼**

在 Xamarin Live Player 再按**一次**[**配對**] 或 [配對]，然後**手動按 enter**鍵。 將會顯示數值代碼，您必須更新設定檔。

**步驟4：產生 GUID**

移至： https://www.guidgenerator.com/online-guid-generator.aspx 並產生新的 guid，並確定已開啟大寫的大小寫。

**步驟5：設定裝置**

在編輯器中開啟**PlayerDeviceList** ，例如 Visual Studio 或 Visual Studio Code。 您需要在此檔案中手動設定您的裝置。 根據預設，檔案應該包含下列空的 `Devices` XML 元素：

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

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>IDE 中的「找不到類型或命名空間」訊息

請確認您選取的**啟始專案**符合您的裝置類型（例如 Android），而且設定符合該裝置類型（例如 適用于 Android 的**Debug** ）。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Player 中的 "InterpretedXamarin" 類型上的函式「找不到」訊息

某些系統類別無法覆寫，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs： ' 資源。版面配置 ' 不包含 ' Main ' 的定義

在 AXML 檔中定義了使用者介面的 Android 專案，就會發生此錯誤。
Xamarin Live Player 目前不支援 AXML 檔案。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 工具列和索引標籤使用 Xamarin 呈現錯誤

如需相關版面配置檔案的名稱，請使用 "axml" 和 "Tabbar" 格式的 [Xamarin]。 預設範本會使用這些名稱;將它們重新命名會導致轉譯問題。

## <a name="related-links"></a>相關連結

- [安裝](~/tools/live-player/install.md)
