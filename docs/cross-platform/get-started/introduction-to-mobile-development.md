---
title: 什麼是 Xamarin？
description: 本文件提供行動裝置開發的簡介、討論 Xamarin、它的運作方式，以及它輸出的應用程式。
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: conceptdev
ms.author: crdun
ms.date: 07/16/2019
ms.openlocfilehash: 372aee9d48866ac49f34b9550fdfa37b7cc0b646
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526675"
---
# <a name="what-is-xamarin"></a>什麼是 Xamarin？

建置行動裝置應用程式非常簡單，您只需要開啟 IDE、撰寫及測試應用程式，並提交到 App Store 即可，只要一個下午即可全部完成。 或者也可以是涉及下列操作的極端複雜流程：嚴苛的預先設計、可用性測試、在數以千計的裝置上進行 QA 測試、完整的 Beta 生命週期；再透過許多不同的方式進行部署。

此文件介紹 Xamarin 平台的概念。 若要深入了解從設計到測試的行動裝置應用程式建置*程序*，請參閱[行動軟體開發生命週期簡介](~/cross-platform/get-started/introduction-to-mobile-sdlc.md)。

請參閱[系統需求](~/cross-platform/get-started/requirements.md#macos-requirements)以確認您的系統。

## <a name="introduction-to-xamarin"></a>Xamarin 簡介

當考慮如何建置 Android 與 iOS 應用程式時，許多人會以為原生語言 (例如 Objective-C、Swift、Java 與 Kotlin) 是唯一的選擇。

Xamarin 允許您使用可跨許多平台 (iOS、Android 與 Windows) 執行的類別庫與執行階段來使用 C# 開發，同時編譯即使對遊戲而言效能都極佳的原生 (非解譯式) 應用程式。

Xamarin 結合原生平台的所有功能，並加上自己的一些強大功能，包括：

1. **針對基礎 SDK 的完整繫結** – Xamarin 包含針對幾乎整個 iOS 及 Android 基礎平台 SDK 的繫結。 此外，這些繫結都是強型別，這表示巡覽及使用相當容易，並在開發期間提供強固的類型檢查。 這有助於減少執行階段錯誤及品質更優良的應用程式。
1. **Objective-C、Java、C 及 C++ Interop** – Xamarin 提供直接叫用 Objective-C、Java、C 及 C++ 程式庫的設施，賦予您使用範圍更廣、已經存在之協力廠商程式碼的能力。 這可讓您利用使用 Objective-C、Java 或 C/C++ 撰寫的現有 iOS 與 Android 程式庫。 此外，Xamarin 提供繫結專案，允許您輕鬆使用宣告式的語法來繫結原生 Objective-C 及 Java 程式庫。
1. **現代化語言建構** – Xamarin 應用程式是使用 C# 撰寫的，即一種相較於 Objective-C 和 Java 包含重大改善的現代化語言，例如「動態語言功能」  ；「功能建構」，例如 Lambda、LINQ、「平行程式設計」  功能、複雜的「泛型」等。
1. **令人驚豔的基底類別庫 (BCL)** – Xamarin 應用程式使用 .NET BCL，它是包含完整且簡化功能的一個大型類別集合，例如強大的 XML、資料庫、序列化、IO、字串及網路支援等。 現有 C# 程式碼也能編譯並在應用程式中使用，這提供數以千計的程式庫，使您能夠執行 BCL 未涵蓋的動作。
1. **現代化的整合式開發環境 (IDE)** – Xamarin 在 macOS 上使用 Visual Studio for Mac，在 Windows 上則使用 Visual Studio。 兩者皆是現代化的 IDE，包含例如程式碼自動完成、複雜的專案及解決方案管理系統、完整的專案範本程式庫、整合式原始檔控制等功能。
1. **行動裝置跨平台支援** – Xamarin 針對三個主要的行動平台 (iOS、Android 與 Windows) 提供複雜的跨平台支援。 撰寫的應用程式可共用高達 90% 的程式碼，而我們的 Xamarin.Mobile 程式庫更提供統一的 API 以存取三個平台中常見的資源。 這可以大幅降低開發成本及以三個熱門行動平台為目標之行動裝置應用程式開發人員進行行銷的時間。

因為 Xamarin 強大及完整的功能組，它正好滿足想要使用現代化語言及平台開發跨平台行動應用程式之應用程式開發人員的需求。

> [!NOTE]
> 本「使用者入門」系列著重於 iOS 和 Android 應用程式的建置入門。 Microsoft 提供適用於平板電腦和桌上型電腦的[通用 Windows 平台 (UWP) 開發](https://docs.microsoft.com/windows/uwp/develop/)相關資訊。 若要深入了解使用 Xamarin 的跨平台開發 (包括適用於 Windows 的 UWP 應用程式)，請參閱[建置跨平台應用程式指南](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)。

## <a name="how-does-xamarin-work"></a>Xamarin 如何運作？

Xamarin 提供兩個商業產品：Xamarin.iOS 和 Xamarin.Android。 它們都是以 *Mono* – 一個基於已發佈之 .NET ECMA 標準的開放原始碼版本 .NET Framework – 作為基礎建置的。 Mono 存在的時間幾乎跟 .NET Framework 一樣長，並且可在任何想像得到的平台上執行，包含 Linux、Unix、FreeBSD 和 macOS。

在 iOS 上，Xamarin 的「預先」  (*AOT*) 編譯器會將 Xamarin.iOS 應用程式直接編譯成原生 ARM 組譯碼。 在 Android 上，Xamarin 的編譯器會將程式碼編譯為「中繼語言」  (*IL*)，並會在應用程式啟動時使用 *Just-in-Time* (*JIT*) 的方式編譯成原生組譯碼。

在這兩種案例下，Xamarin 應用程式會利用可自動處理，例如記憶體配置、記憶體回收、基礎平台 Interlop 等工作的執行階段。

### <a name="xamariniosdll-and-monoandroiddll"></a>Xamarin.iOS.dll 和 Mono.Android.dll

Xamarin 應用程式是針對名為 Xamarin 行動設定檔的 .NET BCL 子集所建置。 此設定檔是專為行動應用程式所建立，並封裝在 Xamarin.iOS.dll 與 Mono.Android.dll 中 (分別適用於 iOS 與 Android)。 這與 Silverlight (及 Moonlight) 應用程式是針對 Silverlight/Moonlight .NET 設定檔建置的情況相似。 事實上，Xamarin Mobile Profile 等同於新增大量 BCL 類別的 Silverlight 4.0 設定檔。

如需可用組件及類別的完整清單，請參閱 [Xamarin.iOS 組件清單](~/cross-platform/internals/available-assemblies.md?context=xamarin/ios)及 [Xamarin.Android 組件清單](~/cross-platform/internals/available-assemblies.md?context=xamarin/android)

除 BCL 之外，這些 .dll 也包含幾乎整個 iOS SDK 和 Android SDK 的包裝函式，允許直接從 C# 叫用基礎 SDK API。

### <a name="application-output"></a>應用程式輸出

當 Xamarin 應用程式編譯完成時，其結果便是一個應用程式套件 (iOS 為 .app 檔案，Android 則為 .apk 檔案)。 這些檔案與使用平台之預設 IDE 所建置的應用程式套件之間無法區別，其部署的方式也完全相同。

## <a name="next-steps"></a>後續步驟

現在您已初步了解 Xamarin 如何運作，下一步就要開始使用下列其中一個指南建置應用程式：

- [**Xamarin.Forms 使用者入門**](~/get-started/index.yml)
- [**Xamarin.iOS 使用者入門**](~/ios/get-started/hello-ios/index.md)
- [**Xamarin.Android 使用者入門**](~/android/get-started/hello-android/index.md)
- [**Xamarin.Mac 使用者入門**](~/mac/get-started/hello-mac.md)
