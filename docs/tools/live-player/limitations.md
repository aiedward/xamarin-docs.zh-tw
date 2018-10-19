---
title: Xamarin Live Player 的限制
description: 本文件說明 Xamarin Live Player 的限制。 它討論裝置需求、 功能它適用於專案類型，且其他其他主題。
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: aff6990df1b710190f11c2d7fa09c8399e94f8af
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "40251166"
---
# <a name="limitations-of-xamarin-live-player"></a>Xamarin Live Player 的限制

![預覽功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>裝置需求

Xamarin Live Player 應用程式支援下列 Android 裝置：

- Android 4.2 或更新版本。
- ARM-v7a，ARM-v8a、 ARM64-v8a arm、x86 或 x86_64 處理器。

## <a name="limitations"></a>限制

有一些限制可以執行 Xamarin Live Player，包括以下項目中的項目：

### <a name="ios"></a>iOS

Live Player 不適用於 iOS。

### <a name="xamarinforms"></a>Xamarin.Forms

- 不支援自訂轉譯器。
- 不支援效果。
- 不支援使用自訂繫結屬性的自訂控制項。
- 不支援內嵌的資源 (亦即。 PCL 中內嵌影像或其他資源)。
- （即不支援協力廠商 MVVM 架構Prism、 Mvvm 跨、 Mvvm Light 等）。

### <a name="other-project-types"></a>其他專案類型

- Live Player 不是原生的 Android 專案 （供 Android XML 的使用者介面）。

### <a name="misc"></a>其他

- 反映的支援有限 （目前會影響某些受歡迎的 Nuget，SQLite 和 Json.NET 等）。 可能會繼續支援其他的 Nuget。
- 無法覆寫某些系統類別 （例如，您不能實作子類別）。
- 一些需要佈建的平台功能無法運作 （不過它已設定一般的作業，例如相片圖庫存取） Xamarin Live Player 應用程式中。
- 自訂目標與建置步驟會被忽略。 例如，無法納入 Fody、 Refit、 AutoFac 和 AutoMapper 等工具。
- 不支援 F # 專案
- 可能不支援使用自訂的泛型類別和介面的進階的案例。

使用**回報的問題**中[Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio-2017)或是[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem)報告與 Xamarin Live Player 的任何問題。

## <a name="related-links"></a>相關連結

- [疑難排解](~/tools/live-player/troubleshooting.md)
- [安裝](~/tools/live-player/install.md)
