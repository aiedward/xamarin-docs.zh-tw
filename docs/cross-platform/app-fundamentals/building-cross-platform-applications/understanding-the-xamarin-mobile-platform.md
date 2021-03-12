---
title: 第1部分-瞭解 Xamarin 行動平臺
description: 本檔將概要說明 Xamarin 平臺，查看編譯器、平臺 SDK 存取、程式碼共用、使用者介面建立、視覺化設計工具等等。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: f68f1efe952d86e05fe1d874e94a64b590ad43d7
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602342"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第1部分-瞭解 Xamarin 行動平臺

Xamarin 平臺包含許多元素，可讓您開發適用于 iOS 和 Android 的應用程式：

- **C # 語言** –可讓您使用熟悉的語法和複雜的功能，例如泛型、LINQ 和平行工作程式庫。
- **Mono .net framework** -提供 Microsoft .net framework 中廣泛功能的跨平臺執行。
- **編譯器** -視平臺而定，會產生原生應用程式 (例如 iOS) 或整合式 .net 應用程式和執行時間 (例如 Android) 。 編譯器也會針對行動部署執行許多優化，例如連結到未使用的程式碼。
- **IDE 工具** -Mac 和 Windows 上的 Visual Studio 可讓您建立、建立及部署 Xamarin 專案。

此外，因為基礎語言是使用 .NET framework 的 c #，所以可以將專案結構化以共用也可部署到 Windows Phone 的程式碼。

## <a name="under-the-hood"></a>本質上

雖然 Xamarin 可讓您以 c # 撰寫應用程式，並在多個平臺之間共用相同的程式碼，但每個系統上的實際實值也不同。

## <a name="compilation"></a>編譯

C # 來源會在每個平臺上以不同的方式來進入原生應用程式：

- **iOS** – c # 是預先 (AOT) 編譯成 ARM 元件語言。 包含 .NET framework，並在連結期間移除未使用的類別，以減少應用程式的大小。 Apple 不允許在 iOS 上產生執行時間程式碼，因此無法使用某些語言功能 (請參閱  [Xamarin. IOS 限制](~/ios/internals/limitations.md) ) 。
- **Android** – c # 會編譯成 IL 並封裝 MonoVM + JIT'ing。 在連結期間會移除架構中未使用的類別。 應用程式會以 JAVA/藝術 (Android 執行時間) ，並透過 JNI 與原生類型互動 (請參閱  [Xamarin. Android 限制](~/android/internals/limitations.md) ) 。
- **Windows** – c # 會編譯成 IL 並由內建執行時間執行，且不需要 Xamarin 工具。 遵循 Xamarin 指導方針設計 Windows 應用程式，可讓您更輕鬆地在 iOS 和 Android 上重複使用程式碼。
  通用 Windows 平臺也有 **.Net 原生** 選項，其行為類似于 XAMARIN 的 AOT 編譯。

適用于 [xamarin](~/ios/deploy-test/linker.md) 和 [xamarin](~/android/deploy-test/linker.md) 的連結器檔會提供有關此部分編譯器的詳細資訊。

執行時間 ' 編譯 ' –應避免以動態方式產生程式碼 `System.Reflection.Emit` 。

Apple 的核心可防止在 iOS 裝置上產生動態程式碼，因此在 Xamarin 中，即時發出程式碼將無法運作。 同樣地，「動態語言執行時間」功能無法搭配 Xamarin 工具使用。

某些反映功能可以 (例如，Monotouch.dll 會將它用於反映 API) ，但不會產生程式碼。

## <a name="platform-sdk-access"></a>Platform SDK 存取

Xamarin 讓平臺專屬 SDK 所提供的功能可以使用熟悉的 c # 語法輕鬆存取：

- **ios** – Xamarin 會將 Apple 的 CocoaTouch SDK 架構公開為您可以從 c # 參考的命名空間。 例如，包含所有使用者介面控制項的 UIKit 架構可以包含在簡單的  `using UIKit;` 語句中。
- **Android** – Xamarin 會將 Google 的 android SDK 公開為命名空間，因此您可以使用 using 語句參考受支援 SDK 的任何部分，例如  `using Android.Views;` 存取使用者介面控制項。
- **Windows** -windows 應用程式是使用 Visual Studio 在 windows 上建立的。 專案類型包括 Windows Forms、WPF、WinRT 和通用 Windows 平臺 (UWP) 。

## <a name="seamless-integration-for-developers"></a>適用于開發人員的無縫整合

Xamarin 的優點是儘管幕後、Xamarin 和 Xamarin (與 Microsoft 的 Windows Sdk 之間的差異，) 為撰寫可在所有三個平臺上重複使用的 c # 程式碼提供順暢的體驗。

商務邏輯、資料庫使用方式、網路存取和其他一般函數可以撰寫一次，並在每個平臺上重複使用，為平臺特定的使用者介面提供基礎，以原生應用程式的形式執行。

## <a name="integrated-development-environment-ide-availability"></a>整合式開發環境 (IDE) 可用性

您可以在 Mac 或 Windows 上的 Visual Studio 中完成 Xamarin 開發。 您所選擇的 IDE 取決於您想要設為目標的平臺。

因為 Windows 應用程式只能在 Windows 上開發，所以若要建立 iOS、Android _和_ windows，則需要 Visual Studio for windows。 不過，您可以在 Windows 和 Mac 電腦之間共用專案和檔案，因此可以在 Mac 上建立 iOS 和 Android 應用程式，並在稍後將共用程式碼新增至 Windows 專案。

[需求](~/cross-platform/get-started/requirements.md)指南會更詳細地討論每個平臺的開發需求。

### <a name="ios"></a>iOS

開發 iOS 應用程式需要執行 macOS 的 Mac 電腦。 您也可以使用 Visual Studio，在 Visual Studio 中使用 Xamarin 來撰寫和部署 iOS 應用程式。 不過，基於組建和授權目的，仍需要 Mac。

您必須安裝 Apple 的 Xcode IDE，以提供用於測試的編譯器和模擬器。 您可以免費在自己的裝置上 [進行](~/ios/get-started/installation/device-provisioning/free-provisioning.md)測試，但若要建立散發的應用程式 (例如，應用程式商店) 您必須加入 Apple 開發人員計畫 (每年 $99 美元) 。 每次提交或更新應用程式時，都必須先審核並核准 Apple，才能讓客戶下載。

程式碼是使用 Visual Studio IDE 所撰寫，而螢幕佈局可以用程式設計的方式建立，也可以使用 Xcode 在 Mac 上進行編輯。

如需有關如何設定的詳細指示，請參閱《 [Xamarin IOS 安裝指南》](~/ios/get-started/installation/index.md) 。

### <a name="android"></a>Android

Android 應用程式開發需要安裝 JAVA 和 Android Sdk。 Sdk 提供建立、部署和測試所需的編譯器、模擬器和其他工具。 JAVA、Google 的 Android SDK 和 Xamarin 的工具都可在 Windows 和 macOS 上安裝及執行。 建議使用下列設定：

- 使用 Visual Studio 2019 的 Windows 10
- macOS Mojave (10.11 +) 與 Visual Studio 2019 for Mac 搭配使用

Xamarin 提供統一的安裝程式，可使用必要的 JAVA、Android 和 Xamarin 工具來設定您的系統， (包括適用于螢幕佈局的視覺化設計工具) 。 如需詳細指示，請參閱「 [Xamarin Android 安裝指南](~/android/get-started/installation/index.md) 」。

您可以在實際裝置上建立及測試應用程式，而不需使用 Google 的任何授權，不過，若要透過商店 (（例如 Google Play、Amazon 或 Barnes and Noble）散發您的應用程式 &amp;) 註冊費用可能會收取給操作員。 Google Play 會立即發佈您的應用程式，而其他商店則會有類似 Apple 的核准流程。

### <a name="windows"></a>Windows

Windows 應用程式 (WinForms、WPF 或 UWP) 是使用 Visual Studio 所建立。 它們不會直接使用 Xamarin。 不過，您可以在 Windows、iOS 和 Android 之間共用 c # 程式碼。
請造訪 Microsoft 開發 [人員中心](https://developer.microsoft.com/) ，以瞭解 Windows 開發所需的工具。

## <a name="creating-the-user-interface-ui"></a>建立使用者介面 (UI)

使用 Xamarin 的主要優點是，應用程式使用者介面會使用每個平臺上的原生控制項，建立與以目標 C 或 JAVA (分別針對 iOS 和 Android 所撰寫的應用程式不區分的應用程式) 。

在您的應用程式中建立畫面時，您可以在程式碼中配置控制項，或使用適用于每個平臺的設計工具來建立完整的畫面。

### <a name="create-controls-programmatically"></a>以程式設計方式建立控制項

每個平臺都允許使用程式碼，將使用者介面控制項新增至畫面。 這可能相當耗時，因為在對控制項位置和大小進行硬式編碼的圖元座標時，可能很難將完成的設計視覺化。

不過，以程式設計方式建立控制項有其優點，特別是在 iOS 上建立可在 iPhone 和 iPad 螢幕大小中以不同方式轉譯的視圖。

### <a name="visual-designer"></a>視覺化設計工具

每個平臺都有不同的方法，可讓您以視覺化方式配置畫面：

- **iOS** –分鏡腳本包含多個視圖和控制項，而且可以在中存取 **。** 包含在專案中的分鏡腳本檔案。
- **Android** – Xamarin 提供適用于 Visual Studio 的 android 拖放 UI 設計工具。
- **Windows** -Microsoft 提供 Visual Studio 和 Blend 中的拖放 UI 設計工具。 畫面版面配置會儲存為。XAML 檔案。

這些螢幕擷取畫面顯示每個平臺上可用的視覺效果畫面設計工具：

 [![這些螢幕擷取畫面顯示每個平臺上可用的視覺效果畫面設計工具](understanding-the-xamarin-mobile-platform-images/designer-all1.png)](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情況下，您以視覺化方式建立的元素都可以在程式碼中參考。

### <a name="user-interface-considerations"></a>使用者介面考慮

使用 Xamarin 建立跨平臺應用程式的主要優點是，它們可以利用原生 UI 工具組，向使用者呈現熟悉的介面。 UI 也會像任何其他原生應用程式一樣快速執行。

某些 UI 比喻適用于多個平臺 (例如，所有三個平臺都使用類似的滾動清單控制項) 但為了讓您的應用程式能夠「感覺」，UI 應該在適當的情況下充分利用平臺特定的使用者介面元素。 平臺特定 UI 的形式範例包括：

- **iOS** –具有軟返回按鈕的階層式導覽、畫面底部的索引標籤。
- **Android** – [硬體/系統-軟體上一頁] 按鈕、[動作] 功能表、畫面頂端的索引標籤。
- **Windows** -windows 應用程式可以在桌上型電腦、平板電腦 (（例如 Microsoft Surface) 和手機）上執行。 例如，Windows 10 裝置可能會有硬體 [上一頁] 按鈕和動態磚。

建議您閱讀與您的目標平臺相關的設計指導方針：

- **iOS** –  [Apple 的人類介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** –  [Google 的使用者介面指導方針](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** –  [Windows 的使用者體驗設計指導方針](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-reuse"></a>程式庫和程式碼重複使用

Xamarin 平臺允許在所有平臺上重複使用現有的 c # 程式碼，並整合以原生方式為每個平臺撰寫的程式庫。

### <a name="c-source-and-libraries"></a>C # 來源與程式庫

因為 Xamarin 產品使用 c # 和 .NET framework，所以有許多現有的原始程式碼 (開放原始碼和內部專案) 可以在 Xamarin 或 Xamarin. Android 專案中重複使用。 來源通常可以直接新增至 Xamarin 解決方案，而且會立即運作。 如果已使用不支援的 .NET framework 功能，可能需要進行一些調整。

可以在 Xamarin 或 Xamarin 中使用的 c # 來源範例包括： SQLite-NET、NewtonSoft.JSON 及 SharpZipLib。

### <a name="objective-c-bindings--binding-projects"></a>目標-C 系結 + 系結專案

Xamarin 提供一個稱為 *b c h* 的工具，可協助建立可在 Xamarin. iOS 專案中使用目標 C 程式庫的系結。 如需如何完成這項操作的詳細資訊，請參閱系結 [目標-C 類型檔](~/cross-platform/macios/binding/binding-types-reference.md) 。

可在 Xamarin 中使用的目標 C 程式庫範例包括： RedLaser 條碼掃描、Google Analytics 分析和 PayPal 整合。 開放原始碼 Xamarin. iOS 系結可在 [GitHub](https://github.com/mono/monotouch-bindings)上取得。

### <a name="jar-bindings--binding-projects"></a>.jar 系結 + 系結專案

Xamarin 支援在 Xamarin 中使用現有的 JAVA 程式庫。 如需如何使用的詳細資訊，請參閱系結 [JAVA 程式庫檔](~/android/platform/binding-java-library/index.md) 。來自 Xamarin 的 JAR 檔案。

開放原始碼 Xamarin. Android 系結可在 [GitHub](https://github.com/mono/monodroid-bindings)上取得。

### <a name="c-via-pinvoke"></a>C via PInvoke

「平台叫用」技術 (P/Invoke) 可讓 managed 程式碼 (c # ) 呼叫原生程式庫中的方法，以及支援原生程式庫以回呼 managed 程式碼。

例如， [SQLITE .net](https://github.com/praeclarum/sqlite-net) 程式庫會使用如下的語句：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

這會系結至 iOS 和 Android 中的原生 C 語言 SQLite 實作為。
熟悉現有 C API 的開發人員可以建立一組 c # 類別，以對應至原生 API 並利用現有的平臺程式碼。 有檔可連結 Xamarin 中的 [原生程式庫](~/ios/platform/native-interop.md) ，類似的原則適用于 Xamarin. Android。

### <a name="c-via-cppsharp"></a>C + + via CppSharp

Miguel 說明 CXXI (現在在他的[blog](https://tirania.org/blog/archive/2011/Dec-19.html)上稱為[CppSharp](https://github.com/mono/CppSharp)) 。 直接系結至 c + + 程式庫的替代方法是建立 C 包裝函式，並透過 P/Invoke 系結至該包裝函式。
