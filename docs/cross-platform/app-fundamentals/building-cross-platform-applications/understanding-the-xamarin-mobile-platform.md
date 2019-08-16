---
title: 第1部分–瞭解 Xamarin Mobile 平臺
description: 本檔說明 Xamarin 平臺的高層級, 並探討編譯器、平臺 SDK 存取、程式碼共用、使用者介面建立、視覺化設計工具等等。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e3a9afa2780f9cf5816427d282644fb2ac3c8dde
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526765"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第1部分–瞭解 Xamarin Mobile 平臺

Xamarin 平臺包含幾個專案, 可讓您開發適用于 iOS 和 Android 的應用程式:

- language –可讓您使用熟悉的語法和複雜的功能, 例如泛型、LINQ 和平行工作程式庫。 **C#**
- **Mono .net framework** –提供 Microsoft .net framework 中廣泛功能的跨平臺執行。
- **編譯器**–視平臺而定, 會產生原生應用程式 (例如 iOS) 或整合式 .NET 應用程式和執行時間 (例如 Android)。 編譯器也會針對行動部署執行許多優化, 例如連結掉未使用的程式碼。
- **IDE 工具**– Mac 和 Windows 上的 Visual Studio 可讓您建立、建立及部署 Xamarin 專案。

此外, 由於基礎語言是C#使用 .net framework, 因此可以結構化專案來共用程式碼, 也可以部署到 Windows Phone。

## <a name="under-the-hood"></a>幕後

雖然 Xamarin 可讓您在中撰寫C#應用程式, 並在多個平臺上共用相同的程式碼, 但每個系統上的實際實作為非常不同。

## <a name="compilation"></a>編譯

C#來源會在每個平臺上以非常不同的方式進入原生應用程式:

- **iOS** – C#是預先編譯為 ARM 元件語言的時間 (AOT)。 包含 .NET framework, 並在連結期間去除未使用的類別, 以減少應用程式大小。 Apple 不允許在 iOS 上產生執行時間程式碼, 因此有些語言功能無法使用 (請參閱「 [Xamarin IOS 限制](~/ios/internals/limitations.md)」)。
- **Android** – C#會編譯為 IL, 並使用 MonoVM + JIT'ing 進行封裝。 在連結期間, 會去除架構中未使用的類別。 應用程式會與 JAVA/美工 (Android 執行時間) 並存執行, 並透過 JNI 與原生類型互動 (請參閱「 [Xamarin Android 限制](~/android/internals/limitations.md)」)。
- **Windows** – C#會編譯為 IL 並由內建執行時間執行, 而且不需要 Xamarin 工具。 依照 Xamarin 的指導方針設計 Windows 應用程式, 可讓您更輕鬆地在 iOS 和 Android 上重複使用程式碼。
  請注意, 通用 Windows 平臺也有一個 [ **.NET Native** ] 選項, 其行為類似于 [Xamarin] 的 [AOT 編譯]。


適用于[xamarin](~/ios/deploy-test/linker.md)和[xamarin](~/android/deploy-test/linker.md)的連結器檔集提供此部分編譯器的詳細資訊。

執行時間 ' 編譯 ' –以動態方式`System.Reflection.Emit`產生程式碼–應該避免。

Apple 的核心可防止在 iOS 裝置上產生動態程式碼, 因此在 Xamarin 中即時發出程式碼將無法運作。 同樣地, 動態語言執行時間的功能無法搭配 Xamarin 工具使用。

有些反映功能可正常執行 (例如 MonoTouch 會將它用於反映 API, 而不是程式碼產生。

## <a name="platform-sdk-access"></a>平臺 SDK 存取

Xamarin 讓平臺特定 SDK 所提供的功能可輕鬆地透過熟悉C#的語法來存取:

- **ios** – Xamarin 會將 Apple 的 CocoaTouch SDK 架構公開為可供您參考的命名C#空間。 例如, 包含所有使用者介面控制項的 UIKit 架構都可以包含在簡單`using UIKit;`的語句中。
- **Android** – Xamarin 會公開 Google 的 Android SDK 做為命名空間, 因此您可以使用 using 語句 (例如`using Android.Views;` ) 來參考支援的 SDK 的任何部分, 以存取使用者介面控制項。
- **Windows** – windows 應用程式是使用 Visual Studio 在 windows 上建立的。 專案類型包括 Windows Forms、WPF、WinRT 和通用 Windows 平臺 (UWP)。

## <a name="seamless-integration-for-developers"></a>開發人員的順暢整合

Xamarin 的優點是, 雖然本質上的差異, 但 Xamarin. iOS 和 Xamarin (結合 Microsoft 的 Windows Sdk) 提供了一種順暢的程式碼撰寫C#體驗, 可以在所有三個平臺重複使用。

商務邏輯、資料庫使用量、網路存取和其他一般函式都可以撰寫一次, 然後在每個平臺上重複使用, 為平臺特定的使用者介面提供基礎, 讓您以原生應用程式的形式來執行。

## <a name="integrated-development-environment-ide-availability"></a>整合式開發環境 (IDE) 可用性

Xamarin 開發可以在 Mac 或 Windows 上的 Visual Studio 中完成。 您選擇的 IDE 將取決於您想要做為目標的平臺。

因為 Windows 應用程式只能在 Windows 上開發, 若要建立 iOS、Android_和_windows, 則需要 windows Visual Studio。 不過, 您可以在 Windows 與 Mac 電腦之間共用專案和檔案, 如此一來, 就能在 Mac 上建立 iOS 和 Android 應用程式, 並可在稍後將共用程式碼新增至 Windows 專案。

[需求](~/cross-platform/get-started/requirements.md)指南中會更詳細地討論每個平臺的開發需求。

### <a name="ios"></a>iOS

開發 iOS 應用程式需要 Mac 電腦, 執行 macOS。 您也可以使用 Visual Studio, 在 Visual Studio 中以 Xamarin 撰寫和部署 iOS 應用程式。 不過, 針對組建和授權用途, 仍然需要 Mac。

必須安裝 Apple 的 Xcode IDE, 以提供用於測試的編譯器和模擬器。 您可以免費在自己的裝置上[進行](~/ios/get-started/installation/device-provisioning/free-provisioning.md)測試, 但要建立用於散發的應用程式 (例如 App Store) 您必須加入 Apple 的開發人員計畫 (每年 $99 美元)。 每次提交或更新應用程式時, Apple 都必須經過審查並核准, 才能供客戶下載。

撰寫程式碼時, 會使用 Visual Studio IDE 和螢幕佈局, 以程式設計方式或在任一 IDE 中透過 Xamarin 的 iOS 設計工具進行編輯。

如需有關如何設定的詳細指示, 請參閱《 [Xamarin 安裝指南》](~/ios/get-started/installation/index.md) 。

### <a name="android"></a>Android

Android 應用程式開發需要安裝 JAVA 和 Android Sdk。 這些工具提供建立、部署和測試所需的編譯器、模擬器和其他工具。 JAVA、Google 的 Android SDK 和 Xamarin 的工具都可以在 Windows 和 macOS 上安裝及執行。 建議使用下列設定:

- 具有 Visual Studio 2019 的 Windows 10
- macOS Mojave (10.11 +) 搭配適用于 Mac 的 Visual Studio 2019

Xamarin 提供整合的安裝程式, 可使用必要的 JAVA、Android 和 Xamarin 工具 (包括螢幕佈局的視覺化設計工具) 來設定您的系統。 如需詳細指示, 請參閱《 [Xamarin 安裝指南》](~/android/get-started/installation/index.md) 。

您可以在實際裝置上建立和測試應用程式, 而不需要 Google 的授權, 不過若要透過商店散發您的應用程式 (例如 Google Play &amp; 、Amazon 或 barnes and Noble), 可以向操作員收取註冊費用。 Google Play 會立即發佈您的應用程式, 而其他存放區的核准程式與 Apple 的類似。

### <a name="windows"></a>Windows

Windows 應用程式 (WinForms、WPF 或 UWP) 是以 Visual Studio 建立。 它們不會直接使用 Xamarin。 不過, C#程式碼可以在 Windows、IOS 和 Android 之間共用。
請造訪 Microsoft 的[開發人員中心](https://developer.microsoft.com/), 瞭解 Windows 開發所需的工具。

## <a name="creating-the-user-interface-ui"></a>建立使用者介面 (UI)

使用 Xamarin 的主要優點是應用程式使用者介面會在每個平臺上使用原生控制項, 建立與以目標 C 或 JAVA 撰寫的應用程式不區分的應用程式 (分別適用于 iOS 和 Android)。

在您的應用程式中建立畫面時, 您可以在程式碼中配置控制項, 或使用適用于每個平臺的設計工具來建立完整的畫面。

### <a name="create-controls-programmatically"></a>以程式設計方式建立控制項

每個平臺都可讓使用者介面控制項使用程式碼加入至螢幕。 這可能非常耗時, 因為在控制位置和大小的硬式編碼圖元座標時, 將已完成的設計視覺化會變得很困難。

不過, 以程式設計方式建立控制項的確有其優點, 特別是在 iOS 上, 在 iPhone 和 iPad 螢幕大小上以不同方式調整大小或轉譯的各種觀點。

### <a name="visual-designer"></a>視覺化設計工具

每個平臺都有不同的方法可讓您以視覺化方式配置螢幕:

- **ios** – Xamarin 的 ios 設計工具使用拖放功能和屬性欄位, 協助建立 Views。 這些 Views 會共同組成一個分鏡腳本, 並可在中存取 **。** 包含在專案中的分鏡腳本檔案。
- **Android** – Xamarin 提供適用于 Visual Studio 的 android 拖放 UI 設計工具。 Android 螢幕佈局會另存為 **。** 使用 Xamarin 工具時 AXML 檔案。
- **Windows** – Microsoft 在 Visual Studio 和 Blend 中提供拖放 UI 設計工具。 螢幕佈局會儲存為。XAML 檔案。

這些螢幕擷取畫面顯示每個平臺上可用的視覺化螢幕設計工具:

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "這些螢幕擷取畫面顯示每個平臺上可用的視覺效果螢幕設計工具")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情況下, 您可以在程式碼中參考您以視覺方式建立的元素。

### <a name="user-interface-considerations"></a>使用者介面考慮

使用 Xamarin 來建立跨平臺應用程式的主要優點是, 他們可以利用原生 UI 工具組來向使用者呈現熟悉的介面。 UI 也會像任何其他原生應用程式一樣快速地執行。

某些 UI 形式可以跨多個平臺執行 (例如, 所有三個平臺都使用類似的滾動清單控制項), 但為了讓您的應用程式能夠「感覺」, UI 應該在適當時利用平臺特定的使用者介面元素。 平臺特定 UI 形式的範例包括:

- **iOS** –具有 [軟返回] 按鈕的階層式導覽、畫面底部的索引標籤。
- **Android** – [硬體/系統-軟體] [上一頁] 按鈕、[動作] 功能表、畫面頂端的索引標籤。
- **Windows** – windows 應用程式可以在桌上型、平板電腦 (例如 Microsoft Surface) 和手機上執行。 例如, Windows 10 裝置可能會有硬體 [上一頁] 按鈕和動態磚。

建議您閱讀與您的目標平臺相關的設計指導方針:

- **iOS** – [Apple 的人類介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [Google 的使用者介面指導方針](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** –[適用于 Windows 的使用者體驗設計指導方針](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>程式庫和程式碼重複使用

Xamarin 平臺允許在所有平臺上重複使用C#現有的程式碼, 以及整合以原生方式為每個平臺撰寫的程式庫。

### <a name="c-source-and-libraries"></a>C#來源和程式庫

因為 Xamarin 產品使用C#和 .net framework, 所以許多現有的原始程式碼 (開放原始碼和內部專案) 都可以在 Xamarin 或 xamarin 專案中重複使用。 來源通常可以直接新增至 Xamarin 解決方案, 而且會立即生效。 如果使用了不支援的 .NET framework 功能, 可能需要進行一些調整。

可在C# Xamarin 或 xamarin 中使用的來源範例包括:SQLite-NET、NewtonSoft 和 SharpZipLib。

### <a name="objective-c-bindings--binding-projects"></a>目標-C 系結 + 系結專案

Xamarin 提供名為*btouch*的工具, 可協助建立可讓您在 Xamarin iOS 專案中使用目標 C 程式庫的系結。 如需如何完成此作業的詳細資訊, 請參閱[Binding 目標-C 類型檔](~/cross-platform/macios/binding/binding-types-reference.md)。

可在 Xamarin 中使用的目標-C 程式庫範例包括:RedLaser 條碼掃描、Google Analytics 和 PayPal 整合。 開放原始碼的 Xamarin。 iOS 系結可在[github](https://github.com/mono/monotouch-bindings)上取得。

### <a name="jar-bindings--binding-projects"></a>.jar 系結 + 系結專案

Xamarin 支援在 Xamarin 中使用現有的 JAVA 程式庫。 如需如何使用的詳細資訊, 請參閱系結[JAVA 程式庫檔](~/android/platform/binding-java-library/index.md)。來自 Xamarin. Android 的 JAR 檔案。

開放原始碼 Xamarin。 Android 系結可在[github](https://github.com/mono/monodroid-bindings)上取得。

### <a name="c-via-pinvoke"></a>C via PInvoke

「平台叫用」技術 (P/Invoke) 可讓 managed 程式C#代碼 () 呼叫原生程式庫中的方法, 以及支援原生程式庫來回呼 managed 程式碼。

例如, [SQLITE 網路](https://github.com/praeclarum/sqlite-net)程式庫會使用如下的語句:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

這會系結至 iOS 和 Android 中的原生 C 語言 SQLite 執行。
熟悉現有 C API 的開發人員可以建立一組C#類別來對應至原生 API, 並利用現有的平臺程式碼。 有檔可連結 Xamarin 中的[原生程式庫](~/ios/platform/native-interop.md), 類似的原則適用于 Xamarin. Android。

### <a name="c-via-cppsharp"></a>C++via CppSharp

Miguel 會在他的[blog](https://tirania.org/blog/archive/2011/Dec-19.html)上說明 CXXI (現在稱為[CppSharp](https://github.com/mono/CppSharp))。 直接系結至連結C++庫的替代方法是建立 C 包裝函式, 並透過 P/Invoke 系結至。
