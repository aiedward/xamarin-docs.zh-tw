---
title: "疑難排解"
description: "Xamarin Live 播放程式，以及如何加以修正的已知的問題。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: d7c5bedb03d7c869be65e3c704bac58a9cdfcbbd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>疑難排解

![預覽功能](~/media/shared/preview.png)

本文說明一些常見的問題，並提供的步驟來更正它們。


## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>行動裝置不會掃描條碼 （或輸入的程式碼） 之後，連線

發生於執行 Xamarin Live 播放程式的行動裝置不在相同的網路執行 IDE 的電腦上。 請參閱下列：

- 確認裝置和電腦都位於相同的 WiFi 網路上。
  - 如果電腦連接到有線網路，請嘗試拔除有線的連線。
- 網路可能會受到嚴密保護 （例如某些公司網路中），封鎖 Xamarin Live 播放程式所需的連接埠。
- 關閉 Xamarin Live 播放器應用程式，然後重新啟動它。


## <a name="error-while-trying-to-deploy-message-in-ide"></a>在 IDE 中的 「 嘗試部署時的錯誤 」 訊息

**「 IOException： 無法讀取傳輸連線的資料： 非封鎖通訊端上的作業會被封鎖 」**

執行 Xamarin Live 播放程式的行動裝置不在相同的網路執行 IDE; 的電腦上時，通常被發生這個錯誤這通常會連接到先前已成功配對的裝置。

* 請檢查裝置和電腦都位於相同的 WiFi 網路上。
* 網路可能會受到嚴密保護 （例如某些公司網路中），封鎖 Xamarin Live 播放程式所需的連接埠。 下列連接埠所需的 Xamarin Live Player:
  * 37847 – 內部網路存取權 
  * 8090 – 外部網路存取

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
