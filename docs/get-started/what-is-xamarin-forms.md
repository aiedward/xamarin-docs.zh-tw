---
title: 什麼是 Xamarin.Forms？
description: 本文介紹 Xamarin.Forms 及其相關程式庫。
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374019"
---
# <a name="what-is-xamarinforms"></a>什麼是 Xamarin.Forms？

[![iOS 和 Android 中範例 Xamarin.Forms 應用程式的螢幕擷取畫面](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms 是一種開放原始碼 UI 架構。 Xamarin.Forms 可讓開發人員從單一的共用程式碼基底建置 Android、iOS，以及 Windows 應用程式。

Xamarin.Forms 可以讓開發人員使用 C# 中的程式碼後置，在 XAML 中建立使用者介面。 這些介面會於每個平台上轉譯為高效能的原生控制項。

## <a name="who-xamarinforms-is-for"></a>Xamarin.Forms 的適用對象

Xamarin.Forms 適用於具有下列目標的開發人員：

- 跨平台共用 UI 版面配置和設計。
- 跨平台共用程式碼、測試及商務邏輯。
- 使用 Visual Studio，在 C# 中撰寫跨平台應用程式。

## <a name="how-xamarinforms-works"></a>Xamarin.Forms 的運作方式

![Xamarin.Forms 架構圖表](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms 提供統一的 API，讓您可以跨平台建立 UI 元素。 此 API 可於 XAML 或 C# 中實作，並支援如 Model-View-ViewModel (MVVM) 等模式的資料繫結。

在執行階段，Xamarin.Forms 會利用平台轉譯器，將跨平台 UI 元素轉換成 Android、iOS，以及 UWP 上的原生控制項。 這可讓開發人員取得原生外觀、風格和效能，並同時實現跨平台共用程式碼的優點。

Xamarin.Forms 應用程式通常是由共用的 .NET Standard 程式庫與個別的平台專案所組成。 共用程式庫包含 XAML 或 C# 檢視，以及所有例如服務、模型或其他程式碼等商務邏輯。 平台專案則包含應用程式所需的所有平台特定邏輯或套件。

Xamarin.Forms 使用 Xamarin，在各平台間以原生方式執行 .NET 應用程式。 如需 Xamarin 的詳細資訊，請參閱[什麼是 Xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-tools"></a>其他工具

Xamarin.Forms 具有 NuGet 套件的龐大生態系統，可為應用程式新增各種功能。 此區段會描述部分常用的 NuGet 套件。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials 是一種程式庫，可為原生裝置功能提供跨平台 API。 如同 Xamarin 本身，Xamarin.Essentials 也是簡化存取原生公用程式程序的抽象概念。 Xamarin.Essentials 提供的一些公用程式範例包括：

- 裝置資訊
- 檔案系統
- 加速計
- 電話撥號程式
- 文字轉換語音
- 螢幕鎖定

如需詳細資訊，請參閱 [Xamarin.Essentials](~/essentials/index.md)。

### <a name="shell"></a>Shell

Xamarin.Forms Shell 會提供大部分應用程式需要的基本功能，藉此降低行動應用程式開發的複雜度。 Shell 提供的一些功能範例包括：

- 一般導覽體驗
- 以 URI 為基礎的導覽配置
- 整合式搜尋處理常式

如需詳細資訊，請參閱 [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平台特定功能

Xamarin 提供通用的 API，可跨平台轉譯原生控制項，但特定平台的功能可能無法於其他平台上使用。 例如，Android 平台具有原生功能，可在 `ListView` 中使用快速捲動，但 iOS 中則無此功能。 Xamarin.Forms 平台特定功能可讓您利用僅在特定平台上提供的功能，而不需建立自訂轉譯器或效果。

Xamarin.Forms 包含預先建置的方案，適用於各種平台特定功能。 如需詳細資訊，請參閱:

- [Xamarin.Forms 平台特定功能](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平台特定功能](~/xamarin-forms/platform/android/index.md)
- [iOS 平台特定功能](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台特定功能](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>質感視覺效果

Xamarin.Forms 材質視覺效果是用於將材質設計規則套用至 Xamarin.Forms 應用程式。 Xamarin.Forms 材質視覺效果會利用 [視覺效果] 屬性，選擇性地將自訂轉譯器套用至 UI，使應用程式在 iOS 和 Android 上具有一致的外觀及風格。

如需詳細資訊，請參閱 [Xamarin.Forms 材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 使用者入門](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms 材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)
