---
title: 什麼是 Xamarin？
description: 本文介紹 Xamarin 和相關連結庫。
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
ms.openlocfilehash: 8213eeb18ec23e79f0cc2a82c22b50d77b6d4931
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256592"
---
# <a name="what-is-xamarin"></a>什麼是 Xamarin？

[![IOS 和 Android 中範例 Xamarin 應用程式的螢幕擷取畫面](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin 是一個開放原始碼平臺，可使用 .NET 建立適用于 iOS、Android 和 Windows 的現代化和高效能應用程式。 Xamarin 是一個抽象層，用來管理共用程式碼與基礎平臺程式碼的通訊。 Xamarin 會在提供便利的 managed 環境中執行，例如記憶體配置和垃圾收集。

Xamarin 可讓開發人員在各平臺上共用其應用程式的平均 90%。 此模式可讓開發人員以單一語言（或重複使用現有的應用程式代碼）撰寫其所有商務邏輯，但在每個平臺上達到原生效能、外觀及操作。

Xamarin 應用程式可以在 PC 或 Mac 上撰寫，並編譯成原生應用程式套件（例如 Android 上的**apk**檔案），或 iOS 上的 **.ipa**檔案。

> [!NOTE]
> 編譯和部署適用于 iOS 的應用程式目前需要 MacOS 機器。 如需開發需求的詳細資訊，請參閱[系統需求](~/cross-platform/get-started/requirements.md#macos-requirements)。

## <a name="who-xamarin-is-for"></a>Xamarin 的適用物件

Xamarin 適用于具有下列目標的開發人員：

- 跨平臺共用程式碼、測試及商務邏輯。
- 使用 Visual Studio，在中C#撰寫跨平臺應用程式。

## <a name="how-xamarin-works"></a>Xamarin 的運作方式

![Xamarin 架構的圖表](what-is-xamarin-images/xamarin-architecture.png)

此圖顯示跨平臺 Xamarin 應用程式的整體架構。 Xamarin 可讓您在每個平臺上建立原生 UI，並C#在中撰寫跨平臺共用的商務邏輯。 在大部分情況下，80% 的應用程式程式碼可透過 Xamarin 來共用。

Xamarin 建置於**Mono**之上，這是以 .net ECMA 標準為基礎的開放原始碼版本 .NET Framework。 Mono 已經存在，幾乎只要 .NET Framework 本身，就會在大部分的平臺上執行，包括 Linux、Unix、FreeBSD 和 macOS。 Mono 執行環境會自動處理工作，例如記憶體配置、垃圾收集，以及與基礎平臺的互通性。

如需平臺特定架構的詳細資訊，請參閱[xamarin. Android](#xamarinandroid)和[xamarin](#xamarinios)。

### <a name="added-features"></a>新增的功能

Xamarin 結合了原生平臺的功能，並加入了一些功能，包括：

1. **基礎 sdk 的完整**系結– Xamarin 包含幾乎適用于 IOS 和 Android 中整個基礎平臺 sdk 的系結。 此外，這些繫結都是強型別，這表示巡覽及使用相當容易，並在開發期間提供強固的類型檢查。 強型別系結會導致較少的執行階段錯誤和高品質的應用程式。
1. **目標-C、 C++ JAVA、c 和 Interop** – Xamarin 提供直接叫用目標 c、java、c 和C++程式庫的功能，讓您能夠使用各式各樣的協力廠商程式碼。 這項功能可讓您使用以目標-C、JAVA 或 C/C++撰寫的現有 IOS 和 Android 程式庫。 此外，Xamarin 提供了系結專案，可讓您使用宣告式語法來系結原生的目標 C 和 JAVA 程式庫。
1. **現代化語言結構**– Xamarin 應用程式是以C#新式語言撰寫，其中包含優於目標 C 和 JAVA 的重大改進，例如動態語言功能、lambda、LINQ、parallel 之類的功能結構程式設計、泛型等等。
1. **健全的基類程式庫（BCL）** – Xamarin 應用程式使用 .net BCL，這是一組大型的類別，具有完整且有效率的功能，例如強大的 XML、資料庫、序列化、IO、字串和網路支援等等。 可以C#編譯現有的程式碼以在應用程式中使用，以提供可在 BCL 以外新增功能的上千個程式庫的存取權。
1. **現代化的整合式開發環境（IDE）** – Xamarin 使用 Visual Studio，這是現代化的 IDE，其中包含程式碼自動完成、精密的專案和解決方案管理系統、完整的專案範本庫、整合式原始檔控制等等。
1. 行動**跨平臺支援**– Xamarin 為 IOS、Android 和 Windows 的三個主要平臺提供複雜的跨平臺支援。 應用程式可以撰寫成共用最多 90% 的程式碼，而 Xamarin 則提供統一的 API 來存取所有三個平臺上的通用資源。 共用程式碼可以大幅降低行動開發人員的開發成本和上市時間。

### <a name="xamarinandroid"></a>Xamarin.Android

[![Xamarin. Android 架構圖](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin： Android 應用程式會C#從編譯成**中繼語言（IL）** ，然後在應用程式啟動時，將**即時（JIT）** 編譯成原生元件。 Xamarin 應用程式會在 Mono 執行環境中執行，並與 Android 執行時間（美工）虛擬機器並存。 Xamarin 提供 Android. * 和 JAVA. * 命名空間的 .NET 系結。 Mono 執行環境會透過受控可呼叫包裝函式 **（MCW）** 呼叫這些命名空間，並將 Android 可呼叫包裝函式 **（ACW）** 提供給藝術，讓這兩種環境都可以叫用程式碼。

如需詳細資訊，請參閱[Xamarin. Android 架構](~/android/internals/architecture.md)。

### <a name="xamarinios"></a>Xamarin.iOS

[![Xamarin. iOS 架構圖表](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Xamarin iOS 應用程式是從C#編譯成原生 ARM 元件程式碼的完整預先 **（AOT）** 。 Xamarin 會使用**選取器**向受控C#和**註冊機構**公開目標-C， C#以向目標-c 公開受控碼。 選取器和註冊機構統稱為「系結」，並允許目標C# -C 並進行通訊。

如需詳細資訊，請參閱[Xamarin. iOS 架構](~/ios/internals/architecture.md)。

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin 是一種程式庫，可提供原生裝置功能的跨平臺 Api。 就像 Xamarin 本身一樣，Xamarin 也是簡化存取原生功能程式的抽象概念。 Xamarin 提供的一些功能範例包括：

- 裝置資訊
- 檔案系統
- 加速計
- 電話撥號程式
- 文字轉換語音
- 螢幕鎖定

如需詳細資訊，請參閱[Xamarin. Essentials](~/essentials/index.md)。

### <a name="xamarinforms"></a>Xamarin.Forms

Xamarin 是一個開放原始碼 UI 架構。 Xamarin 可讓開發人員從單一共用程式碼基底建立 iOS、Android 和 Windows 應用程式。 Xamarin 可以讓開發人員在 XAML 中建立具有程式碼後置的使用者C#介面。 這些使用者介面會呈現為每個平臺上的高效能原生控制項。 Xamarin 提供的一些功能範例包括：

- XAML 使用者介面語言
- 資料繫結
- 軌跡
- 效果
- 樣式

如需詳細資訊，請參閱[Xamarin. Forms](~/xamarin-forms/index.yml)。

## <a name="get-started"></a>開始使用

下列指南可協助您使用 Xamarin 建立您的第一個應用程式：

- [開始使用 Xamarin. 表單](~/xamarin-forms/index.yml)
- [開始使用 Xamarin. Android](~/android/index.yml)
- [開始使用 Xamarin. iOS](~/ios/index.yml)
- [開始使用 Xamarin. Mac](~/mac/index.yml)
