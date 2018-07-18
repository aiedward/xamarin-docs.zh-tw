---
title: 第 4 部分-處理多個平台
description: 本文件說明如何處理應用程式不一致或根據平台或功能。 它討論的螢幕大小、 瀏覽隱喻、 觸控和筆勢、 推播通知和介面思維，例如清單和索引標籤。
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 4a60c99cbc9819f07b77bfe9abe046ea92a550a5
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403321"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4 部分-處理多個平台

## <a name="handling-platform-divergence-amp-features"></a>處理平台分歧&amp;功能

不一致或不只是 '跨平台' 問題;在 '相同' 的平台上的裝置有不同的功能 （尤其是各種可用的 Android 裝置）。 最明顯且最基本的螢幕大小，但其他的裝置屬性可以不同，而且需要應用程式檢查特定功能，並根據其目前狀態 （或不存在） 的行為。

這表示所有的應用程式需要處理正常降級的功能，否則呈現不討喜、 最小公分母的功能集合。 Xamarin 的深度整合與原生 Sdk 每個平台可讓應用程式，以利用平台專屬功能，因此合理設計會使用這些功能的應用程式。

請參閱平台功能的文件的概述中功能的平台有何不同。

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>平台分歧的範例

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>跨平台存在的基本項目

有通用的行動應用程式的某些特性。
這些是設計的較高層級概念通常成立的所有裝置，因此可以形成基礎的應用程式:

-  透過索引標籤或功能表的 特徵選取
-  資料和捲動清單
-  資料的單一檢視
-  編輯資料的單一檢視
-  向後巡覽


設計您的畫面的高階流程時您可以根據這些概念來共通的使用者經驗。

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>平台特定屬性

除了存在於所有平台的基本項目，您必須在您的設計中的位址索引鍵的平台差異。 您可能要考慮 （和撰寫特別處理的程式碼） 這些差異：

-   **螢幕大小**– 某些平台 （例如 iOS 和 Windows Phone 上的舊版） 已標準化為目標相當簡單的螢幕大小。 Android 裝置有各式各樣不同的螢幕尺寸，需要更多的工作，以支援您的應用程式中。
-   **瀏覽隱喻**-跨平台 （例如的差異 硬體 [上一頁] 按鈕，全景 UI 控制項），並在平台 (Android 2 到 4，iPhone 與 iPad) 內。
-   **鍵盤**– 某些 Android 裝置有實體鍵盤，而有些則僅有軟體鍵盤。 遮蓋了螢幕的螢幕鍵盤時偵測到的程式碼必須容易受到這些差異。
-   **觸控和筆勢**– 作業系統支援筆勢辨識各有不同，特別是在較舊版本的每個作業系統。 舊版的 Android 非常有觸控式作業，這表示，支援較舊的裝置可能需要不同的程式碼的支援有限
-   **推播通知**– 每個平台 （例如有不同的功能實作。 動態磚在 Windows 上）。


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>裝置特有的功能

判斷哪些應用程式所需的最小功能必須是;或者，當決定要利用每個平台上的哪些其他功能。 程式碼必須偵測功能停用功能，或提供替代方案 （例如。 地理位置的替代項目可以讓使用者輸入的位置，或選擇從對應）：

-   **相機**– 跨裝置與不同的功能： 某些裝置沒有攝影機，其他人有兩個前端和後端面向相機。 有些攝影機所支援的視訊錄製。
-   **地理位置 & 對應**– 支援 GPS 或 Wi-fi 位置不存在的所有裝置上。 應用程式也需要符合不同的層級的每個方法所支援的精確度。
-   **加速計、 陀螺儀和羅盤**– 這些功能通常位於只有選取的裝置上每個平台，讓應用程式幾乎一律必須提供後援，當硬體不支援。
-   **Twitter 與 Facebook** – 只有 '內建' iOS5 和 iOS6 分別。 較早的版本和其他平台上必須提供您自己的驗證功能，並直接與每個服務的 API。
-   **接近欄位通訊 (NFC)** – 只是在 （部分） 上 Android 手機 （在撰寫本文時）。


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>處理平台分歧

若要從相同的程式碼基底，各有其各自的優點和缺點集支援多個平台有兩種不同方法。

-   **平台抽象**– 商務外觀模式中，提供跨平台的統一的存取，並擷取到單一、 統一的 API 的特定平台實作。
-   **但內容不同的實作**– 引動過程的特定平台功能透過但內容不同的實作，透過架構工具，例如介面和繼承或條件式編譯。


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>平台抽象

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>類別的抽象

使用介面或基底類別中的共用程式碼定義和實作或擴充平台專屬專案中。 撰寫和擴充的類別的抽象概念的共用程式碼特別適合用於可攜式類別庫，因為它們提供給他們的架構的有限的子集而且不能包含編譯器指示詞，以支援平台特定程式碼分支。

 <a name="Interfaces" />


#### <a name="interfaces"></a>介面

使用介面，可讓您實作仍可以傳遞給您共用的程式庫，以利用通用程式碼的平台特定類別。

介面是共用的程式碼中定義，並傳遞至共用的程式庫做為參數或屬性。

特定平台應用程式可以再實作的介面，同時仍然利用 '處理' 的共用程式碼。

 **優點**

實作可以包含平台特定程式碼，並參照特定平台外部程式庫。

 **缺點**

不必建立，並傳入共用程式碼中的實作。 如果深層的共用程式碼會使用此介面再它最後會在透過多個方法參數傳遞，或透過呼叫鏈結否則往下推送。 如果共用的程式碼會使用許多不同的介面則他們必須所有建立及共用的程式碼位置中設定。

 <a name="Inheritance" />


#### <a name="inheritance"></a>繼承

共用程式碼可以在一或多個平台專屬專案中實作抽象或虛擬可延伸的類別。 這是類似使用介面，但具有已經實作一些行為。 是否在介面或繼承的較佳的設計選擇有不同的觀點： 特別是因為 C# 只允許單一繼承它可以決定接下來就可以設計您的 Api 的方式。 請小心使用繼承。

介面的優缺點同樣適用於繼承的基底類別可以包含一些實作程式碼 （也許是整個平台無從驗證的實作，可以選擇性地擴充） 的額外好處。

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

請參閱[Xamarin.Forms](~/xamarin-forms/get-started/index.md)文件。


### <a name="plug-in-cross-platform-functionality"></a>外掛程式的跨平台功能

您也可以擴充跨平台應用程式一致的方式使用外掛程式。

連結從我們[外掛程式 github](https://github.com/xamarin/plugins)，大部分的外掛程式是開放原始碼專案 （通常適用於透過 Nuget 安裝），幫助您實作各式各樣的平台專屬功能從電池狀態與設定常見的 API 可輕鬆地在 Xamarin 平台與 Xamarin.Forms 應用程式中使用。


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>其他跨平台程式庫

有幾個的第 3 個可提供跨平台功能的廠商程式庫：

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **清楚**（適用於當地語系化）：  [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** （適用於 XNA 遊戲）-  [http://www.monogame.net](http://www.monogame.net)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics)和其先驅 [https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>但內容不同的實作

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>條件式編譯

有某些情況下，共用程式碼仍然需要在每個平台，可能存取類別或行為不同的功能上的運作方式不同。 條件式編譯最適合搭配共用資產專案中，相同的原始程式檔中有不同的符號定義的多個專案正在參考位置。

Xamarin 專案一律定義`__MOBILE__`這是適用於 iOS 和 Android 應用程式專案 （請注意雙底線前置和後置的修正程式，這些符號），則為 true。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Xamarin.iOS 定義`__IOS__`可用來偵測的 iOS 裝置。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

也有特定監看式及電視符號：

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

<a name="Android" />

##### <a name="android"></a>Android

可以使用下列方法才會編譯至 Xamarin.Android 應用程式的程式碼

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每個 API 版本也會定義新的編譯器指示詞，因此這類的程式碼可讓您將功能加入如果較新的 API 為目標。 每個 API 層級包含所有 '低' 的層級符號。 這項功能不支援多個平台，真正有用通常`__ANDROID__`符號就已足夠。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

目前並沒有內建符號 xamarin.mac，但您可以加入您自己的 Mac 中的應用程式專案**選項 > 建置 > 編譯器**中**定義的符號**方塊中，或編輯 **.csproj**檔案，並在該處加入 (例如`__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式定義兩個符號 –`WINDOWS_PHONE`和`SILVERLIGHT`– 可用來為目標平台的程式碼。 這些不含底線像是 Xamarin 平台符號周圍的地方執行。


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>使用條件式編譯

條件式編譯的簡單案例研究範例設定 SQLite 資料庫檔案的檔案位置。 三個平台會有稍微不同的需求，針對指定的檔案位置：

-   **iOS** – Apple 偏好非使用者資料放在特定位置 （程式庫目錄），但沒有任何系統常數做為此目錄。 平台特定程式碼，才能建置正確的路徑。
-   **Android** – 所傳回的系統路徑`Environment.SpecialFolder.Personal`是可接受的位置，來儲存資料庫檔案。
-   **Windows Phone** – 隔離儲存區機制不允許完整的路徑來指定，只是相對路徑和檔案名稱。
-   **通用 Windows 平台**– 使用`Windows.Storage`Api。

下列程式碼會使用條件式編譯，以確保`DatabaseFilePath`是正確的每個平台：

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

結果是可供建置及使用 SQLite 資料庫檔案放在不同的位置，每個平台上的所有平台上的類別。

