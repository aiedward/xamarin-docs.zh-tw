---
title: 第 1 篇 – 了解 Xamarin 行動裝置平台
description: 本文件說明概括而言，查看編譯程序、 平台 SDK 存取、 共用程式碼、 建立使用者介面、 視覺化設計工具，以及其他 Xamarin 平台。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c7c0f582ac4a7dc8571fbc607dba9b0ad97d49e1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674837"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第 1 篇 – 了解 Xamarin 行動裝置平台

Xamarin 平台是由數項目可讓您開發適用於 iOS 和 Android 的應用程式所組成：

-   **C#語言**– 可讓您使用熟悉的語法和複雜的功能，例如泛型、 LINQ 和平行的工作程式庫。
-   **Mono 的.NET framework** – 提供廣泛的功能，在 Microsoft.NET framework 中的跨平台實作。
-   **編譯器**– 根據平台，會產生原生應用程式 （例如。 iOS) 或整合式的.NET 應用程式和執行階段 （例如。 Android)。 編譯器也會針對行動裝置的部署，例如連結離未使用的程式碼執行許多最佳化。
-   **IDE 工具**– 在 Mac 和 Windows 上的 Visual Studio 可讓您建立、 建置和部署 Xamarin 專案。

此外，因為基礎的語言C#使用.NET framework 中，專案結構化共用也可部署到 Windows Phone 的程式碼。

## <a name="under-the-hood"></a>背後原理

雖然 Xamarin 可讓您在撰寫應用程式C#，並跨多個平台共用相同的程式碼，每個系統上的實際實作是很大的差異。

## <a name="compilation"></a>編譯

C#來源打到原生應用程式在每個平台上非常不同的方式：

-   **iOS** –C#是預先 just-in-time (AOT) 編譯成 ARM 組合語言。 .NET framework，會包含連結，以減少應用程式大小期間會去除未使用的類別。 Apple 不允許執行階段程式碼產生在 iOS 上，因此不提供某些語言功能 (請參閱[Xamarin.iOS 限制](~/ios/internals/limitations.md))。
-   **Android** –C#會編譯成 IL 並與 MonoVM + JIT'ing 一起封裝。 連結期間會移除未使用的 framework 中的類別。 應用程式執行的並行使用 Java/圖案 （Android 執行階段） 並與其互動的原生類型，透過 JNI (請參閱[Xamarin.Android 限制](~/android/internals/limitations.md))。
-   **Windows** –C#會編譯成 IL 並由內建的執行階段中，執行，而且不需要 Xamarin 工具。 設計 Windows 應用程式下 Xamarin 的指導方針能更容易重複使用 iOS 和 Android 上的程式碼。
  請注意，通用 Windows 平台也有 **.NET Native**操作方式類似 Xamarin.iOS' AOT 編譯選項。


連結器文件[Xamarin.iOS](~/ios/deploy-test/linker.md)並[Xamarin.Android](~/android/deploy-test/linker.md)提供編譯程序的這個部分的詳細資訊。

執行階段 'compilation'-產生程式碼使用動態`System.Reflection.Emit`– 應該予以避免。

Apple 的核心會防止在 iOS 裝置上的動態程式碼產生，因此發出程式碼上即時在 Xamarin.iOS 中將無法運作。 同樣地，動態語言執行平台功能無法搭配 Xamarin 工具。

某些反映功能運作 （例如。 MonoTouch.Dialog 會將它用於映射 API），只是無法產生的程式碼。

## <a name="platform-sdk-access"></a>平台 SDK 存取權限

Xamarin 可更容易存取的特定平台 SDK 所提供的功能與熟悉的C#語法：

-   **iOS** – Xamarin.iOS 會當做您可以從參考的命名空間中公開 Apple 產品 CocoaTouch SDK 架構C#。 例如，包含所有的使用者介面控制項的 UIKit 架構可能會包括在簡單`using UIKit;`陳述式。
-   **Android** – Xamarin.Android 會公開 Google 的 Android SDK 為命名空間，如此您就可以參考使用支援的任何的 SDK 部分陳述式，例如`using Android.Views;`存取使用者介面控制項。
-   **Windows** – Windows 上使用 Visual Studio 建立 Windows 應用程式。 專案類型包括 Windows Form、 WPF、 WinRT 和通用 Windows 平台 (UWP)。

## <a name="seamless-integration-for-developers"></a>適用於開發人員的無縫整合

Xamarin 的優點是，即使實際上差異，Xamarin.iOS 和 Xamarin.Android （搭配 Microsoft 的 Windows Sdk） 提供順暢的體驗，可供寫入C#可以是跨所有的三個平台重複使用的程式碼。

商務邏輯、 資料庫使用量、 網路存取，以及其他常見的函式可以寫入一次，並在每個平台，提供將基礎平台特定的使用者介面的外觀和原生應用程式中執行，重複使用。

## <a name="integrated-development-environment-ide-availability"></a>整合式的開發環境 (IDE) 的可用性

Xamarin 開發可在 Mac 或 Windows 上的 Visual Studio 中使用。 您選擇取決於平台的 IDE，您想要為目標。

因為 Windows 應用程式只可以開發在 Windows 中，若要建置適用於 iOS、 Android、_和_Windows 需要 Visual Studio for Windows。 不過，您已可共用的專案和 Windows 和 Mac 電腦，因此可以在 Mac 上建置 iOS 和 Android 的應用程式，並共用程式碼可以稍後新增至 Windows 專案之間的檔案。

更詳細地討論每個平台的開發需求[需求](~/cross-platform/get-started/requirements.md)指南。

### <a name="ios"></a>iOS

開發 iOS 應用程式需要執行 macOS 的 Mac 電腦。 您也可以使用 Visual Studio 來撰寫和部署使用 Visual Studio 中的 Xamarin iOS 應用程式。 不過，Mac 仍然需要組建和授權用途。

提供編譯器和模擬器進行測試，必須安裝 Apple 的 Xcode IDE。 您可以在您自己的裝置上進行測試[免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md)，但針對發佈 （例如建立應用程式。 App Store)，您必須加入 Apple Developer Program （價值 $99 美元每年）。 每次您送出或更新的應用程式，它必須是審閱及核 Apple 之前，它可供客戶下載。

使用 Visual Studio IDE 撰寫程式碼，並可以以程式設計方式建立或使用 Xamarin iOS 設計工具在任一 IDE 中編輯畫面版面配置。

請參閱[Xamarin.iOS 安裝指南](~/ios/get-started/installation/index.md)如需有關取得設定的詳細指示。

### <a name="android"></a>Android

Android 應用程式開發需要 Java 和 Android Sdk 安裝。 這些提供的編譯器、 模擬器和其他建置、 部署和測試所需的工具。 Java、 Google 的 Android SDK 和 Xamarin 的工具可以全部安裝並在 Windows 和 macOS 上執行。 建議使用下列設定：

- 使用 Visual Studio 2019 的 Windows 10
- macOS Mojave （10.11 +） 與 Visual Studio 2019 for Mac

Xamarin 提供統一的安裝程式將您的系統以設定必要的 Java、 Android 和 Xamarin 工具 （包括畫面版面配置的視覺化設計工具）。 請參閱[Xamarin.Android 安裝指南](~/android/get-started/installation/index.md)如需詳細指示。

您可以建置並測試而不需要任何授權在實際裝置上的應用程式，向 Google，不過將透過市集的應用程式散發 (例如 Google Play、 Amazon 或 Barnes &amp; Noble) 的註冊費可能支付給操作員。 Google Play 將會立即發行應用程式，而其他存放區有類似於 Apple 的核准程序。

### <a name="windows"></a>Windows

使用 Visual Studio 所建置 （WinForms、 WPF 或 UWP） 的 Windows 應用程式。 它們不直接使用 Xamarin。 不過，C#可以跨 Windows、 iOS 和 Android 共用程式碼。
請瀏覽 Microsoft[開發人員中心](https://developer.microsoft.com/)若要深入了解 Windows 開發所需的工具。

## <a name="creating-the-user-interface-ui"></a>建立使用者介面 (UI)

使用 Xamarin 的主要優點是，應用程式使用者介面會使用每個平台上，建立區別以 OBJECTIVE-C 或 Java 撰寫的應用程式的應用程式的原生控制項 (適用於 iOS 和 Android 分別)。

建置您的應用程式中的畫面，您可以配置程式碼中的控制項，或建立每個平台使用可用的設計工具的完成畫面。

### <a name="create-controls-programmatically"></a>以程式設計方式建立控制項

每個平台可讓使用者介面控制項加入至螢幕，使用程式碼。 這可以是非常耗費時間可能很難視覺化完成的設計控制項的位置和大小的硬式編碼的像素座標時。

以程式設計方式建立控制項沒有好處，尤其是在 iOS 上對於建置可調整大小，或跨 iPhone 和 iPad 螢幕大小的方式呈現的檢視。

### <a name="visual-designer"></a>視覺化設計工具

每個平台都有不同的方法，用來以視覺方式配置畫面：

- **iOS** – Xamarin iOS 設計工具可協助建置使用拖放 [功能] 和 [屬性] 欄位的檢視。 這些檢視共同構成分鏡腳本，並可於 **。分鏡腳本**隨附於您的專案中的檔案。
- **Android** – Xamarin 提供適用於 Visual Studio 的 Android 的拖放 UI 設計工具。 Android 畫面版面配置會儲存為 **。AXML**檔案時使用 Xamarin 工具。
- **Windows** – Microsoft 提供的 Visual Studio 和 Blend 中的拖放 UI 設計工具。 畫面配置會儲存為。XAML 檔案。

這些螢幕擷取畫面會顯示可用的 visual 螢幕設計工具，每個平台：

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "這些螢幕擷取畫面顯示每個平台上的可用 visual 螢幕設計工具")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情況下，可在您的程式碼中參考您以視覺化方式建立的項目。

### <a name="user-interface-considerations"></a>使用者介面考量

使用 Xamarin 建置跨平台應用程式的主要優點是，它們可以利用原生的 UI 工具組，以熟悉的介面呈現給使用者。 UI 也會為任何其他原生應用程式快速執行。

某些 UI 象徵物作用於多個平台 （例如，所有的三個平台使用類似的捲動清單控制項），但為了讓您的應用程式 '也會覺得很' UI 應該利用平台特定的使用者介面項目中，使用適當的時候。 特定平台 UI 隱喻的範例包括：

- **iOS** – 軟性的返回按鈕，以階層式導覽索引標籤上螢幕的底部。
- **Android** – 系統-軟後按鈕、 [動作] 功能表、 在螢幕上方的索引標籤。
- **Windows** – Windows 應用程式可以在桌上型電腦、 平板電腦 （例如 Microsoft Surface) 和手機上執行。 Windows 10 裝置可能會有硬體上一步 按鈕和動態磚，例如。

建議您先閱讀與您的目標平台的設計指導方針：

- **iOS** – [Apple 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [Google 的使用者介面指導方針](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [Windows 的使用者經驗設計指導方針](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>程式庫和重複使用程式碼

Xamarin 平台可讓重複使用現有的C#跨所有平台，以及每個平台以原生方式撰寫的程式庫整合的程式碼。

### <a name="c-source-and-libraries"></a>C#來源和程式庫

因為 Xamarin 產品使用C#和.NET framework 中，大量 （兩者都開啟來源和內部的專案） 的現有來源程式碼可重複使用 Xamarin.iOS 或 Xamarin.Android 專案中。 通常來源可以直接新增至 Xamarin 方案，並會立即生效。 如果已使用不支援的.NET framework 功能，則可能需要一些調整。

範例的C#可以用於 Xamarin.iOS 或 Xamarin.Android 的來源包括：SQLite NET、 NewtonSoft.JSON 和 SharpZipLib。

### <a name="objective-c-bindings--binding-projects"></a>OBJECTIVE-C 繫結 + 繫結專案

Xamarin 提供工具，稱為*btouch*幫您建立允許 Objective C 程式庫，以 Xamarin.iOS 專案中使用的繫結。 請參閱[繫結 Objective C 類型文件](~/cross-platform/macios/binding/binding-types-reference.md)如需有關如何做到這點。

可以在 Xamarin.iOS 中使用的 Objective C 程式庫的範例包括：RedLaser 條碼掃描，Google Analytics 和 PayPal 整合。 開放原始碼 Xamarin.iOS 繫結位於[github](https://github.com/mono/monotouch-bindings)。

### <a name="jar-bindings--binding-projects"></a>.jar 繫結 + 繫結專案

使用現有的 Java 程式庫，在 Xamarin.Android 中的 Xamarin 支援。 請參閱[繫結 Java 程式庫文件](~/android/platform/binding-java-library/index.md)如需使用方式詳細資料。從 Xamarin.Android 的 JAR 檔案。

開放原始碼 Xamarin.Android 繫結位於[github](https://github.com/mono/monodroid-bindings)。

### <a name="c-via-pinvoke"></a>透過 PInvoke C

「 平台叫用 「 技術 (P/Invoke) 可讓 managed 程式碼 (C#) 來呼叫原生程式庫中的方法，以及支援原生程式庫來回呼至 managed 程式碼。

例如， [SQLite NET](https://github.com/praeclarum/sqlite-net)程式庫會使用如下的陳述式：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

這會在 iOS 和 Android 原生的 C 語言 SQLite 實作繫結。
熟悉現有 C API 的開發人員可以建構一組C#類別來對應至原生 API，並利用現有的平台程式碼。 文件[連結原生程式庫](~/ios/platform/native-interop.md)Xamarin.iOS，以類似的原則亦適用於 Xamarin.Android。

### <a name="c-via-cppsharp"></a>C++透過 CppSharp

Miguel 說明 CXXI (現在稱為[CppSharp](https://github.com/mono/CppSharp)) 上他[部落格](https://tirania.org/blog/archive/2011/Dec-19.html)。 繫結至的替代項目C++程式庫直接是建立 C 包裝函式，並經由 P/Invoke 的繫結至的。
