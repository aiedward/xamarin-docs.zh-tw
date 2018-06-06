---
title: Xamarin 即時播放程式的限制
description: 本文件說明 Xamarin Live 播放程式的限制。 它討論裝置需求、 功能，適用於專案類型，且其他的其他主題。
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: ea71391382f9e1ecb80cbf5f2d5bf127e0d6d1be
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793671"
---
# <a name="limitations-of-xamarin-live-player"></a>Xamarin 即時播放程式的限制

![預覽功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>裝置需求
Xamarin Live 播放器應用程式支援下列裝置：

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 或更新版本。
- ARM-v7a，ARM v8a、 ARM64 v8a，x86 或 x86_64 處理器。

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 或更新版本。
- ARM64 處理器。

-----

## <a name="limitations"></a>限制

有一些限制 Xamarin Live Player 可以執行，包括以下項目中的項目：

### <a name="xamarinforms"></a>Xamarin.Forms

- 不支援的自訂轉譯器。
- 不支援效果。
- 不支援自訂控制項和自訂繫結屬性。
- 不支援內嵌的資源 (ie。 在 PCL 中內嵌影像或其他資源)。
- 第三方 MVVM 架構不支援 （即角柱、 Mvvm 跨、 Mvvm Light 等）。
- 不支援在 iOS 上的資產目錄。

### <a name="other-project-types"></a>其他專案類型

- 原生 Android 或 iOS 專案 （使用者介面，請使用 Android 的 XML 或分鏡腳本） 不是即時的播放程式。

### <a name="misc"></a>其他

- 反映的支援受到限制 （目前會影響某些常用的 NuGets SQLite 和 Json.NET 等）。 其他 NuGets 可能仍會支援。
- 無法覆寫某些系統類別 （例如，您不能實作子類別）。
- 需要佈建的平台功能無法運作 （不過它已經設定的一般作業，像是相片圖庫存取） 的 Xamarin Live 播放器應用程式中。
- 自訂的目標與建置步驟都會被忽略。 例如，無法納入 Fody、 Refit、 AutoFac 和 AutoMapper 等工具。
- F # 專案不支援 Android 和 iOS 上的支援有限
- 不支援進階的案例，以自訂的泛型類別和介面。

請將任何其他的問題報告上[bugzilla](https://aka.ms/live-player-report-issue)。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
- [安裝](~/tools/live-player/install.md)
