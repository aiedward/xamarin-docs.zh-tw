---
title: 什麼是 Xamarin.Forms？
description: 本文介紹 Xamarin. 表單和相關連結庫。
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: 8bd530e743330ab8058f13cb7ba09f95a30ee886
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256578"
---
# <a name="what-is-xamarinforms"></a>什麼是 Xamarin.Forms？

[![IOS 和 Android 中的範例 Xamarin. Forms 應用程式的螢幕擷取畫面](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin 是一個開放原始碼 UI 架構。 Xamarin 可讓開發人員從單一共用程式碼基底建立 Android、iOS 和 Windows 應用程式。

Xamarin 可以讓開發人員在 XAML 中建立具有程式碼後置的使用者C#介面。 這些介面會轉譯為每個平臺上的高效能原生控制項。

## <a name="who-xamarinforms-is-for"></a>Xamarin. 表單的物件

Xamarin. 表單適用于具有下列目標的開發人員：

- 跨平臺共用 UI 版面配置和設計。
- 跨平臺共用程式碼、測試及商務邏輯。
- 使用 Visual Studio，在中C#撰寫跨平臺應用程式。

## <a name="how-xamarinforms-works"></a>Xamarin. 表單的運作方式

![Xamarin. 表單架構圖表](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin 會提供一致的 API，讓您跨平臺建立 UI 元素。 這個 API 可以在 XAML 或C#中執行，並支援模型 VIEWMODEL （MVVM）等模式的資料系結。

在執行時間，Xamarin 會利用平臺轉譯器，將跨平臺 UI 元素轉換成 Android、iOS 和 UWP 上的原生控制項。 可讓開發人員取得原生外觀、風格和效能，同時實現跨平臺共用程式碼的優點。

Xamarin：表單應用程式通常是由共用 .NET Standard 程式庫和個別平臺專案所組成。 共用程式庫包含 XAML 或C# views，以及任何商務邏輯，例如服務、模型或其他程式碼。 平臺專案包含應用程式所需的任何平臺特定邏輯或套件。

Xamarin 會使用 Xamarin 在平臺上以原生方式執行 .NET 應用程式。 如需有關 Xamarin 的詳細資訊，請參閱[什麼是 xamarin？](~/get-started/what-is-xamarin.md)。

## <a name="additional-tools"></a>其他工具

Xamarin 具有許多 NuGet 套件的生態系統，可為應用程式新增各種功能。 本節說明一些常用的 NuGet 套件。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin 是一種程式庫，可提供原生裝置功能的跨平臺 Api。 就像 Xamarin 本身一樣，Xamarin 也是簡化存取原生公用程式之過程的抽象概念。 Xamarin 所提供的一些公用程式範例包括：

- 裝置資訊
- 檔案系統
- 加速計
- 電話撥號程式
- 文字轉換語音
- 螢幕鎖定

如需詳細資訊，請參閱[Xamarin. Essentials](~/essentials/index.md)。

### <a name="shell"></a>Shell

Xamarin：提供大部分應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度。 Shell 提供的一些功能範例包括：

- 一般導覽體驗
- 以 URI 為基礎的導覽配置
- 整合式搜尋處理常式

如需詳細資訊，請參閱[Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>平臺細節

Xamarin 提供通用的 API，可跨平臺呈現原生控制項，但特定平臺的功能可能不存在於其他平臺上。 例如，Android 平臺具有可在中`ListView`快速滾動的原生功能，但 iOS 則不提供。 Xamarin：表單平臺細節可讓您利用僅在特定平臺上提供的功能，而不需建立自訂轉譯器或效果。

Xamarin 包含適用于各種平臺特定功能的預先建立解決方案。 如需詳細資訊，請參閱:

- [Xamarin. 表單平臺-細節](~/xamarin-forms/platform/platform-specifics/index.md)
- [Android 平臺-細節](~/xamarin-forms/platform/android/index.md)
- [iOS 平臺-細節](~/xamarin-forms/platform/ios/index.md)
- [Windows 平臺-細節](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>質感視覺效果

[Xamarin]： [表單材質] 視覺效果是用來將材質設計規則套用至 Xamarin. 表單應用程式。 [Xamarin] 視覺效果會利用視覺屬性，選擇性地將自訂轉譯器套用至 UI，因而導致應用程式在 iOS 和 Android 上具有一致的外觀與風格。

如需詳細資訊，請參閱[Xamarin. 表單材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>相關連結

- [開始使用 Xamarin. 表單](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin. 表單材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)
