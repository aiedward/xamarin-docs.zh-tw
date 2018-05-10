---
title: 第 1 篇 – 了解 Xamarin 的行動裝置平台
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 10d1b0786d5ca0b42b6e6c3e2f2a08edb7a37591
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第 1 篇 – 了解 Xamarin 的行動裝置平台

Xamarin 平台所組成的數字的項目可讓您開發適用於 iOS 和 Android 的應用程式：

-   **C# 語言**– 可讓您使用熟悉的語法和複雜的功能，例如泛型、 LINQ 和平行工作的程式庫。
-   **單聲道的.NET framework** – 提供廣泛的功能，Microsoft.NET framework 中的跨平台實作。
-   **編譯器**– 根據平台，會產生原生應用程式 （例如。 iOS) 或整合式的.NET 應用程式和執行階段 （例如。 Android)。 編譯器也會執行許多的最佳化行動裝置的部署，例如連結抽離未使用的程式碼。
-   **IDE 工具**– Mac 和 Windows 上的 Visual Studio 可讓您建立、 建置及部署 Xamarin 專案。


此外，因為基礎的語言是 C#.NET framework，可結構化專案共用也可以部署到 Windows Phone 的程式碼。

 <a name="Under_the_Hood" />


## <a name="under-the-hood"></a>背後原理

雖然 Xamarin 可讓您撰寫 C# 中的應用程式，並跨多個平台共用相同的程式碼，每個系統上的實際實作會非常不同。

 <a name="Compilation" />


## <a name="compilation"></a>編譯

C# 原始程式檔會在每個平台上非常不同的方式進行到原生應用程式的途中：

-   **iOS** – C# 中是前時間 (AOT) 編譯為 ARM 組件語言。 .NET framework 隨附，剔除了連結，以減少應用程式大小期間未使用的類別。 Apple 不允許執行階段程式碼產生在 iOS 上，因此不可以使用某些語言功能 (請參閱[Xamarin.iOS 限制](~/ios/internals/limitations.md))。
-   **Android** – C# 編譯 il 及 MonoVM + JIT'ing 封裝。 Framework 中未使用的類別連結期間去除。 應用程式執行的並行與 Java 圖案 （Android 的執行階段） 並與其互動的原生類型透過 JNI (請參閱[Xamarin.Android 限制](~/android/internals/limitations.md))。
-   **Windows** – C# 編譯的 il 和所內建的執行階段中，執行，而且不需要 Xamarin 工具。 設計 Windows 應用程式下列 Xamarin 的指引可重複使用 iOS 和 Android 上的程式碼變得更容易。
  請注意，也有通用 Windows 平台 **.NET 原生**其運作方式類似 Xamarin.iOS' AOT 編譯選項。


連結器文件[Xamarin.iOS](~/ios/deploy-test/linker.md)和[Xamarin.Android](~/android/deploy-test/linker.md)提供有關此組件編譯程序的詳細資訊。

執行階段 'compilation'-產生程式碼以動態方式與`System.Reflection.Emit`– 應予以避免。

Apple 的核心，導致無法在 iOS 裝置上的動態程式碼產生，因此發出程式碼上即時 Xamarin.iOS 中將無法運作。 同樣地，動態語言執行階段功能不能利用 Xamarin 工具。

某些反映功能運作 （例如。 MonoTouch.Dialog 使用它來進行反映應用程式開發介面），只是無法產生程式碼。

 <a name="Platform_SDK_Access" />


## <a name="platform-sdk-access"></a>平台 SDK 存取權限

Xamarin 可讓您熟悉 C# 語法更容易存取特定平台 SDK 所提供的功能：

-   **iOS** – Xamarin.iOS 會當做您可以從 C# 參考的命名空間中公開 Apple CocoaTouch SDK 架構。 例如 UIKit 架構，其中包含所有使用者介面控制項可以包含使用簡單`using UIKit;`陳述式。
-   **Android** – Xamarin.Android 會公開 Google Android SDK 為命名空間，因此您可以參考使用支援的任何的 SDK 部分陳述式，例如`using Android.Views;`存取使用者介面控制項。
-   **Windows** – Windows 上使用 Visual Studio 建立 Windows 應用程式。 專案類型包含 Windows Forms、 WPF、 WinRT 和通用 Windows 平台 (UWP)。


 <a name="Seamless_Integration_for_Developers" />


## <a name="seamless-integration-for-developers"></a>適用於開發人員的緊密整合

Xamarin 的好處是，雖然實際上差異，Xamarin.iOS 和 Xamarin.Android （搭配 Microsoft 的 Windows Sdk） 提供了順暢的體驗，撰寫 C# 程式碼可在所有的三個平台之間重複使用。

商務邏輯、 資料庫使用量、 網路存取和其他常見函式可以寫入一次，並在每個平台上，提供基礎的平台特定的使用者介面，其外觀及執行為原生應用程式重複使用。

 <a name="Integrated_Development_Environment_(IDE)_Availability" />


## <a name="integrated-development-environment-ide-availability"></a>整合式的開發環境 (IDE) 的可用性

可以進行 Mac 或 Windows 上的 Visual Studio 中的 Xamarin 開發。 IDE 您選擇將平台會決定您想要為目標。

由於 Windows 應用程式只可以開發在 Windows 中，若要建置 iOS、 Android、_和_Windows 需要 Visual Studio for Windows。 不過，它是可以在共用專案和 Windows 和 Mac 電腦，因此可以在 Mac 上建置 iOS 和 Android 應用程式和更新版本可以共用的程式碼加入至 Windows 專案之間的檔案。

每個平台的開發需求會更詳細地討論[需求](~/cross-platform/get-started/requirements.md)指南。


<a name="iOS" />

### <a name="ios"></a>iOS

開發 iOS 應用程式需要執行 macOS 的 Mac 電腦。 您也可以使用 Visual Studio 來撰寫和部署 iOS 應用程式與 Visual Studio 的 Xamarin。 不過，進行建置和授權仍然需要 Mac。

提供編譯器和模擬器進行測試，必須安裝 Apple Xcode IDE。 您可以在您自己的裝置上測試[免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md)，但建立 （例如發佈應用程式 應用程式存放區中），您必須加入 Apple 開發人員計劃 ($99 USD 每年)。 每次您送出或更新應用程式，它必須是檢閱及核准 apple 之前，它可供下載的客戶。

使用 Visual Studio IDE 撰寫程式碼和畫面配置可以以程式設計方式建立或編輯使用 Xamarin 的 iOS 任一 IDE 中的設計工具。

請參閱[Xamarin.iOS 安裝指南](~/ios/get-started/installation/index.md)如需有關取得設定的詳細指示。

<a name="Android" />

### <a name="android"></a>Android

Android 應用程式開發需要使用 Java 和 Android Sdk 安裝。 這些資料行會提供編譯器、 模擬器和其他工具所需的建置、 部署和測試。 Java、 Google Android SDK 和 Xamarin 的工具可以所有安裝和執行下列設定：

-  Mac OS X El Capitan 和更高版本 （10.11 +） 與 Visual Studio for Mac
-  Windows 7 與上述的 Visual Studio 2015 或 2017年


Xamarin 提供了統一的安裝程式，將會設定您的系統必要條件 java，Android 和 Xamarin 工具 （包括畫面配置的視覺化設計工具）。 請參閱[Xamarin.Android 安裝指南](~/android/get-started/installation/index.md)如需詳細指示。

您可以建置和測試不需要任何授權在實際裝置上的應用程式，向 Google，不過散發透過市集應用程式 (例如 Google Play、 Amazon 或 Barnes&amp;貴族) 註冊費用可能支付給操作員。 Google Play 將立即發行應用程式，雖然其他存放區都有類似於 Apple 的核准程序。

 <a name="Windows_Phone" />


### <a name="windows"></a>Windows

使用 Visual Studio 所建置 （WinForms、 WPF 或 UWP） 的 Windows 應用程式。 它們不直接使用 Xamarin。 不過，C# 程式碼可以共用 Windows、 iOS 和 Android 跨。
請瀏覽 Microsoft[開發人員中心](https://developer.microsoft.com/)若要了解所需的 Windows 開發的工具。

 <a name="Creating_the_User_Interface_(UI)" />


## <a name="creating-the-user-interface-ui"></a>建立使用者介面 (UI)

使用 Xamarin 的主要優點就是應用程式使用者介面會使用原生控制項，每個平台上，建立區別 OBJECTIVE-C 或 Java 撰寫的應用程式的應用程式 (適用於 iOS 和 Android 分別)。

應用程式中建置螢幕，您可以配置控制項中的程式碼，或建立完成每個平台使用所提供的設計工具的螢幕。

 <a name="Programmatically_Create_Controls" />


### <a name="create-controls-programmatically"></a>以程式設計方式建立控制項

每個平台可讓使用者介面控制項加入至螢幕，使用程式碼。 這可以是非常耗費時間可能很難視覺化完成的設計時硬式編碼的像素的座標控制項的位置和大小。

以程式設計方式建立控制項沒有好處，特別是在 iOS 建置調整大小或跨 iPhone 和 iPad 的螢幕大小以不同的方式呈現的檢視。

 <a name="Visual_Designer" />


### <a name="visual-designer"></a>視覺化設計工具

每個平台都有不同的方法，來以視覺方式配置螢幕：

-   **iOS** – Xamarin 的 iOS 設計工具可協助建立檢視表中使用拖放功能和屬性欄位。 這些檢視共同構成分鏡腳本，並可於**。分鏡腳本**隨附於您的專案中的檔案。
-   **Android** – Xamarin for Visual Studio 提供的 Android 拖放 UI 設計工具。 Android 的畫面配置會儲存為**。AXML**檔案時使用 Xamarin 工具。
-   **Windows** – Microsoft 提供在 Visual Studio 和 Blend 中的拖放 UI 設計工具。 畫面配置會儲存為。XAML 檔案。


這些螢幕擷取畫面顯示每個平台可用的視覺化畫面設計工具：

 [ ![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "這些螢幕擷取畫面顯示每個平台上可用的視覺化畫面設計工具")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情況下，可在程式碼中參考您以視覺化方式建立的項目。

 <a name="User_Interface_Considerations" />


### <a name="user-interface-considerations"></a>使用者介面考量

使用 Xamarin 建置跨平台應用程式的主要優點是，它們可以利用原生的 UI 工具套件，來向使用者呈現熟悉的介面。 UI 也可以為任何其他原生應用程式快速執行。

某些 UI 象徵物跨多個平台 （例如，所有三個平台使用類似的捲動清單控制項），但為了讓您的應用程式 '感覺' 右 UI 應該利用平台專屬的使用者介面項目在適當的情況。 特定平台 UI 象徵物的範例包括：

-   **iOS** – 軟上一頁按鈕，以階層式導覽索引標籤上螢幕的底部。
-   **Android** – 硬體/系統-軟體將按鈕、 [動作] 功能表、 在螢幕頂端的索引標籤。
-   **Windows** – Windows 應用程式可以在桌上型電腦、 （例如 Microsoft Surface) 平板電腦和手機上執行。 Windows 10 裝置可能有例如回按鈕動態磚，硬體。


建議您先閱讀適用於您的目標平台的設計指導方針：

-   **iOS** – [Apple 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
-   **Android** – [Google 使用者介面指導方針](http://developer.android.com/guide/practices/ui_guidelines/index.html)
-   **Windows** –[適用於 Windows 的使用者經驗設計指導方針](https://developer.microsoft.com/windows/design)


 <a name="Library_and_Code_Re-use" />


## <a name="library-and-code-re-use"></a>程式庫和程式碼重複使用

Xamarin 平台允許重複使用現有的 C# 程式碼的跨所有平台，以及撰寫每個平台的原生程式庫的整合。

 <a name="C#_Source_and_Libraries" />


### <a name="c-source-and-libraries"></a>C# 原始程式檔和程式庫

因為 Xamarin 產品使用 C# 和.NET framework，許多現有的程式碼 （兩者都開啟來源和內部專案） 可能會在 Xamarin.iOS 或 Xamarin.Android 專案中重複使用。 通常來源可以只是加入至 Xamarin 方案，而且它會立即生效。 如果已使用不支援的.NET framework 功能，則可能需要一些做調整。

C# 原始程式檔可以用於 Xamarin.iOS 或 Xamarin.Android 的範例包括： SQLite NET、 NewtonSoft.JSON 和 SharpZipLib。

 <a name="Objective-C_Bindings_+_Binding_Projects" />


### <a name="objective-c-bindings--binding-projects"></a>Objective C 繫結 + 繫結的專案

Xamarin 提供一個工具，稱為*btouch*幫您建立允許 Objective C 程式庫使用 Xamarin.iOS 專案中的繫結。 請參閱[繫結 Objective C 類型的文件](~/cross-platform/macios/binding/binding-types-reference.md)的執行方式的詳細資訊。

可以用於 Xamarin.iOS Objective C 程式庫的範例包括： RedLaser 條碼掃描，Google Analytics 和 PayPal 整合。 上的開放原始碼 Xamarin.iOS 繫結可用[github](https://github.com/mono/monotouch-bindings)。

 <a name=".jar_Bindings_+_Binding_Projects" />


### <a name="jar-bindings--binding-projects"></a>d 繫結 + 繫結的專案

Xamarin 支援使用現有的 Java 文件庫中 Xamarin.Android。 請參閱[繫結的 Java 程式庫文件集](~/android/platform/binding-java-library/index.md)如需使用方式詳細資料。從 Xamarin.Android JAR 檔案。

上的開放原始碼 Xamarin.Android 繫結可用[github](https://github.com/mono/monodroid-bindings)。

 <a name="C_via_PInvoke" />


### <a name="c-via-pinvoke"></a>透過 PInvoke C

「 平台叫用 」 技術 (P/Invoke) 可讓 managed 程式碼 (C#) 來呼叫原生程式庫中的方法，以及支援原生程式庫呼叫，回 managed 程式碼。

例如， [SQLite NET](https://github.com/praeclarum/sqlite-net)程式庫會使用如下的陳述式：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

此繫結至 iOS 和 Android 的原生 C 語言 SQLite 實作。
現有的 C API 熟悉的開發人員可以建構一組對應至原生 API，並利用現有的平台程式碼的 C# 類別。 文件[連結原生程式庫](~/ios/platform/native-interop.md)Xamarin.iOS，類似的原則會套用到 Xamarin.Android。

 <a name="C++_via_Cxxi" />


### <a name="c-via-cppsharp"></a>C + + 透過 CppSharp

Miguel 說明 CXXI (現在稱為[CppSharp](https://github.com/mono/CppSharp)) 上他[部落格](http://tirania.org/blog/archive/2011/Dec-19.html)。 繫結至 c + + 程式庫直接的替代方式是建立的 C 包裝函式，透過 P/Invoke 的繫結到的。

