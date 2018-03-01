---
title: "限制"
description: "Xamarin Live 播放程式的某些限制"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: d551c0a82fb8f970ce5a00ec9e64ac7f49c81a44
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>限制

![預覽功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>裝置需求
Xamarin Live 播放器應用程式支援下列裝置：

### <a name="ios"></a>iOS
- iOS 9.0 或更新版本。
- ARM64 處理器。
- 請檢查[App Store](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?mt=8)如需支援的裝置的清單。

### <a name="android"></a>Android
- Android 4.2 或更新版本。
- ARM-v7a，ARM v8a、 ARM64 v8a，x86 或 x86_64 處理器。


## <a name="limitations"></a>限制

有一些限制 Xamarin Live Player 可以執行，包括以下項目中的項目：

### <a name="android"></a>Android
- Android 使用者介面設計 AXML 檔案目前不支援。

### <a name="ios"></a>iOS
- 不支援某些 iOS 分鏡腳本功能。
- 不支援 iOS XIB 檔案。

### <a name="xamarinforms"></a>Xamarin.Forms
- 不支援的自訂轉譯器。
- 不支援效果。
- 不支援自訂控制項和自訂繫結屬性。
- 不支援內嵌的資源 (ie。 在 PCL 中內嵌影像或其他資源)。

### <a name="misc"></a>其他
- 反映的支援受到限制 （目前會影響某些常用的 NuGets SQLite 和 Json.NET 等）。 其他 NuGets 仍會支援。
- 無法覆寫某些系統類別 （例如，您不能實作子類別）。
- 需要佈建的平台功能無法運作 （不過它已經設定的一般作業，像是相片圖庫存取） 的 Xamarin Live 播放器應用程式中。
- 自訂的目標與建置步驟都會被忽略。 例如，無法納入 Fody 等工具。

> [!WARNING]
> **注意：** Xamarin Live Player 無法搭配 Xamarin Studio

請將任何其他的問題報告上[bugzilla](https://aka.ms/live-player-report-issue)。


## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
- [安裝](~/tools/live-player/install.md)
