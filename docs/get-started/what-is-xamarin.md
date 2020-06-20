---
title: 什麼是 Xamarin？
description: 本文介紹 Xamarin 平臺和相關連結庫。
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 708a310ea015f9e678d534898fde18abc3848120
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198325"
---
# <a name="what-is-xamarin"></a>什麼是 Xamarin？

[![IOS 和 Android 中範例 Xamarin 應用程式的螢幕擷取畫面](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin 是一個開放原始碼平台，可使用 .NET 建立適用於 iOS、Android 和 Windows 的現代化且高效能應用程式。 Xamarin 是一種抽象層，用來管理共用程式碼與基礎平台程式碼之間的通訊。 Xamarin 可在擁有便利功能 (例如記憶體配置和記憶體回收) 的受控環境中執行作業。

Xamarin 可讓開發人員在各平台上，共用其平均 90% 的應用程式。 此模式可讓開發人員以單一語言 (或重複使用現有的應用程式代碼) 撰寫其所有商務邏輯，同時在每個平台上達到原生效能、外觀及風格。

Xamarin 應用程式可於 PC 或 Mac 上撰寫，並編譯成原生應用程式套件，例如 Android 上的 **.apk** 檔案，或 iOS 上的 **.ipa** 檔案。

> [!NOTE]
> 編譯及部署適用於 iOS 的應用程式目前仍需要 MacOS 電腦。 如需開發需求的詳細資訊，請參閱[系統需求](~/cross-platform/get-started/requirements.md#macos-requirements)。

## <a name="who-xamarin-is-for"></a>Xamarin 的適用對象

Xamarin 適用於具有下列目標的開發人員：

- 跨平台共用程式碼、測試及商務邏輯。
- 使用 Visual Studio，在 C# 中撰寫跨平台應用程式。

## <a name="how-xamarin-works"></a>Xamarin 的運作方式

![Xamarin 架構的圖表](what-is-xamarin-images/xamarin-architecture.png)

此圖表顯示跨平台 Xamarin 應用程式的整體架構。 Xamarin 可讓您在每個平台上建立原生 UI，並於 C# 中撰寫跨平台共用的商務邏輯。 在大部分情況下，80% 應用程式的程式碼可透過 Xamarin 來共用。

Xamarin 建置於 .NET 之上，它會自動處理工作，例如記憶體配置、垃圾收集，以及與基礎平臺的互通性。

如需平台特定架構的詳細資訊，請參閱 [Xamarin.Android](#xamarinandroid) 與 [Xamarin.iOS](#xamarinios)。

### <a name="added-features"></a>新增的功能

Xamarin 除了結合原生平台的所有功能之外，也新增了一些功能，包括：

1. **針對基礎 SDK 的完整繫結** – Xamarin 包含適用於近乎整個 iOS 及 Android 基礎平台 SDK 的繫結。 此外，這些繫結都是強型別，這表示巡覽及使用相當容易，並在開發期間提供強固的類型檢查。 強型別繫結有助於減少執行階段錯誤，以開發品質更優良的應用程式。
1. **Objective-C、Java、C 及 C++ Interop** – Xamarin 提供直接叫用 Objective-C、Java、C 及 C++ 程式庫的機能，賦予您廣泛使用第三方程式碼的能力。 藉由這項功能，您就可以使用以 Objective-C、Java 或 C/C++ 所撰寫的現有 iOS 與 Android 程式庫。 此外，Xamarin 還提供繫結專案，其可讓您使用宣告式的語法來繫結原生 Objective-C 與 Java 程式庫。
1. **現代化語言建構** – Xamarin 應用程式是使用 C# 所撰寫，相較於 Objective-C 與 Java ，C# 為擁有顯著改善的現代化語言，例如動態語言功能，以及例如 Lambda、LINQ、平行程式設計、泛型等功能建構。
1. **健全的基底類別庫 (BCL)** – Xamarin 應用程式使用 .NET BCL，其為包含完整且簡化功能的大型類別集合，例如強大的 XML、資料庫、序列化、IO、字串以及網路支援等。 現有的 C# 程式碼也能編譯並在應用程式中使用，其提供上千個程式庫的存取權，這些程式庫包含 BCL 未涵蓋的新增功能。
1. **現代化整合式開發環境 (IDE)** – Xamarin 使用 Visual Studio，其為現代化的 IDE，包含例如程式碼自動完成、複雜的專案及解決方案管理系統、完整的專案範本程式庫、整合式原始檔控制等功能。
1. **行動裝置跨平台支援** – Xamarin 針對三個主要平台 (iOS、Android，以及 Windows) 提供複雜的跨平台支援。 應用程式可以撰寫成共用最多90% 的程式碼，並 Xamarin.Essentials 提供統一的 API 來存取所有三個平臺上的一般資源。 共用程式碼可以大幅降低行動裝置開發人員的開發成本以及上市所需時間。

### <a name="xamarinandroid"></a>Xamarin.Android

[![Xamarin. Android 架構圖](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin.Android 應用程式會將程式碼從 C# 編譯為**中繼語言 (IL)**，並接著在應用程式啟動時透過 **Just-in-Time (JIT)** 編譯為原生組譯碼。 Xamarin.Android 應用程式會在 Mono 執行環境中執行，並與 Android Runtime (ART) 虛擬機器並存。 Xamarin 提供對 Android.* 與 JAVA.* 命名空間的 .NET 繫結。 Mono 執行環境會透過**受控可呼叫包裝函式 (MCW)**，呼叫這些名稱空間，並提供 ** Android 可呼叫包裝函式 (ACW) ** 至 ART，讓這兩種環境都可以相互叫用程式碼。

如需詳細資訊，請參閱 [Xamarin.Android 架構](~/android/internals/architecture.md)。

### <a name="xamarinios"></a>Xamarin.iOS

[![Xamarin. iOS 架構圖表](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Xamarin.iOS 應用程式為完全透過**預先編譯 (AOT)**，將程式碼從 C# 編譯為 ARM 原生組譯碼。 Xamarin 會使用**選取器**將 Objective-C 公開給受控 C#，並使用**登錄器**將受控 C# 程式碼公開給 Objective-C。 選取器與登錄器統稱為「繫結」，可允許 Objective-C 與 C# 進行通訊。

如需詳細資訊，請參閱 [Xamarin.iOS 架構](~/ios/internals/architecture.md)。

### Xamarin.Essentials

Xamarin.Essentials是一種程式庫，可提供原生裝置功能的跨平臺 Api。 就像 Xamarin 本身一樣， Xamarin.Essentials 是簡化存取原生功能的程式的抽象概念。 提供的一些功能範例 Xamarin.Essentials 包括：

- 裝置資訊
- 檔案系統
- 加速計
- 電話撥號程式
- 文字轉換語音
- 螢幕鎖定

如需詳細資訊，請參閱 [Xamarin.Essentials](~/essentials/index.md)。

### Xamarin.Forms

Xamarin.Forms是一個開放原始碼 UI 架構。 Xamarin.Forms可讓開發人員從單一共用程式碼基底建立 Xamarin. iOS、Xamarin 和 Windows 應用程式。 Xamarin.Forms可讓開發人員在 XAML 中使用 c # 的程式碼後置來建立使用者介面。 這些使用者介面會於每個平台上轉譯為高效能的原生控制項。 所提供的一些功能範例 Xamarin.Forms 包括：

- XAML 使用者介面語言
- 資料繫結
- 軌跡
- 效果
- 樣式

如需詳細資訊，請參閱 [Xamarin.Forms](~/xamarin-forms/index.yml)。

## <a name="get-started"></a>開始使用

下列指南可協助您使用 Xamarin 建置第一個應用程式：

- [開始使用Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Android 使用者入門](~/android/index.yml)
- [Xamarin.iOS 使用者入門](~/ios/index.yml)
- [Xamarin.Mac 使用者入門](~/mac/index.yml)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
