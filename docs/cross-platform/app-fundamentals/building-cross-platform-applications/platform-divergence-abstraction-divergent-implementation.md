---
title: 第 4-處理多個平台
description: 本文件說明如何處理根據平台或功能的應用程式分歧。 它討論螢幕大小、 瀏覽象徵物、 觸控與手勢、 推播通知和介面典範，例如清單和索引標籤。
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c00519ecc4c8a8d85e993a6002d131f227eb9764
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781604"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4-處理多個平台

## <a name="handling-platform-divergence-amp-features"></a>處理平台分歧&amp;功能

分歧不只是 '跨平台' 問題;'相同' 的平台上的裝置有不同的功能 （尤其是各種可用的 Android 裝置）。 最明顯，最基本的螢幕大小，但其他裝置屬性可以不同，而且需要應用程式檢查特定功能，並根據其目前狀態 （或不存在） 的行為。

這表示所有應用程式必須處理正常降級的功能，否則呈現的不適當、 最低公分功能集合。 Xamarin 的深度整合原生 Sdk 與每個平台可讓應用程式利用的平台專屬的功能，因此合理設計應用程式使用這些功能。

請參閱平台功能的文件如需功能的平台有何不同。

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>平台的不一致的範例

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>跨平台存在的基本項目

沒有通用的行動應用程式的某些特性。
這些是設計的較高層級的概念，通常為 true 的所有裝置，並因此可構成應用程式的基礎：

-  透過索引標籤或功能表的 特徵選取
-  清單的資料和捲動
-  資料的單一檢視
-  編輯資料的單一檢視
-  向後巡覽


設計高階螢幕資料流程時常見的使用者經驗可以根據這些概念。

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>平台專屬的屬性

除了基本項目存在於所有平台上，您必須在您設計的地址索引鍵的平台差異。 您可能要考慮 （和撰寫特別處理的程式碼） 這些差異：

-   **螢幕大小**– 某些平台 （例如 iOS 和 Windows Phone 上的舊版） 具有標準化相對而言較為簡單為目標的螢幕大小。 Android 裝置有各種螢幕尺寸，需要更多心力在支援應用程式中。
-   **瀏覽象徵物**– 跨平台 （例如的差異 硬體 [上一頁] 按鈕，全景 UI 控制項） 以及在平台 (Android 2 到 4，iPhone 與 iPad) 內。
-   **鍵盤**– 某些 Android 裝置有實體鍵盤，而有些則僅擁有軟體鍵盤。 遮蓋螢幕的螢幕鍵盤時偵測到的程式碼必須能夠區分這些差異。
-   **觸控與手勢**– 作業系統支援的筆勢辨識會有所差異，尤其是在每個作業系統的舊版本。 舊版的 Android 非常有觸控式運算，這表示支援較舊的裝置可能需要不同的程式碼的支援受到限制
-   **推播通知**– 每個平台 （例如上有不同的功能實作 動態磚在 Windows 上）。


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>裝置特定功能

判斷哪些應用程式所需的最小功能必須是;或者，當決定來利用每個平台上的其他功能。 程式碼必須偵測功能，並停用功能或提供替代項目 （例如。 地理位置的替代方式可能是要讓使用者輸入的位置，或從對應中選擇）：

-   **相機**– 在裝置之間的功能有所不同： 某些裝置不會有相機，其他兩個向前方及後方相機。 有些攝影機的視訊錄製可。
-   **地理位置 （& s) 對應**– 不存在於所有裝置上支援 GPS 連線還是 Wi-fi 連線的位置。 應用程式也需要符合不同的層級的每個方法所支援的精確度。
-   **加速計、 迴轉儀和指南針**– 讓應用程式幾乎時需要提供後援硬體不支援這些功能通常會每個平台上的裝置選取範圍中找到。
-   **Twitter 與 Facebook** – 只有 '內建' iOS5 和 iOS6 上分別。 在較早版本和其他平台上需要提供您自己的驗證功能和介面直接與每個服務的應用程式開發介面。
-   **接近欄位通訊 (NFC)** – 只 （部分） 上 （在撰寫本文時） 的 Android 手機。


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>平台的不一致的處理

若要從相同的程式碼基底，各有其各自的優點和缺點集支援多個平台有兩種不同方法。

-   **平台抽象**– 商務外觀模式中，提供統一的存取跨平台和擷取特定平台實作單一、 統一的 api。
-   **但內容不同的實作**– 透過但內容不同的實作，透過介面和繼承或條件式編譯架構工具功能的特定平台引動過程。


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>平台抽象

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>抽象類別

使用介面或基底類別中的共用程式碼定義和實作或擴充平台專屬專案中。 撰寫和擴充共用的程式碼使用類別的抽象概念是特別適合那些可攜式類別庫因為它們有可用的 framework 的有限的子集，而且不能包含編譯器指示詞，可支援平台專屬程式碼分支。

 <a name="Interfaces" />


#### <a name="interfaces"></a>介面

使用介面，可讓您實作仍可以傳遞到您的共用程式庫利用通用程式碼的平台特定類別。

介面定義中共用的程式碼，並傳遞至共用的程式庫做為參數或屬性。

特定平台應用程式可以接著實作的介面，同時仍然利用的共用程式碼，'' 加以處理。

 **優點**

實作可以包含平台專屬程式碼，即使參考特定平台外部程式庫。

 **缺點**

不必建立和傳遞到共用的程式碼實作。 如果深度內的共用程式碼會使用此介面然後它最終會被傳遞多個方法的參數，或透過呼叫鏈結否則推。 如果共用的程式碼會使用許多不同的介面然後它們必須所有會建立並設定在共用程式碼中某處。

 <a name="Inheritance" />


#### <a name="inheritance"></a>繼承

共用的程式碼可以在一或多個平台專屬專案中實作無法擴充的抽象或虛擬類別。 這是類似於使用介面但已經實作一些行為。 於或繼承介面是否比較好的設計選擇有不同的觀點： 特別是因為 C# 只允許單一繼承它可以利用聽寫往後可以設計您的應用程式開發介面的方式。 小心使用繼承。

有哪些優缺點的介面同樣適用於繼承的基底類別可包含某些實作程式碼 （或許是整個平台無從驗證的實作，可以選擇性地擴充） 的額外好處。

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

請參閱[Xamarin.Forms](~/xamarin-forms/get-started/index.md)文件。


### <a name="plug-in-cross-platform-functionality"></a>外掛程式的跨平台功能

您也可以擴充跨平台應用程式一致的方式使用外掛程式。

連結從我們[外掛程式 github](https://github.com/xamarin/plugins)，大部分的外掛程式是開放原始碼專案 （通常適用於透過 Nuget 安裝），協助您實作各種不同的設定和電池狀態從特定平台功能一般很容易在 Xamarin 平台與 Xamarin.Forms 應用程式中使用的 API。


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>跨平台的其他程式庫

有數第 3 個合作對象庫可提供跨平台功能：

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **日常用語**（適用於當地語系化）：  [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** （適用於 XNA 遊戲）-  [http://monogame.codeplex.com/](http://monogame.codeplex.com/)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics)和其前兆 [https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>但內容不同的實作

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>條件式編譯

有某些情況下，共用程式碼仍然需要每個平台上，可能存取類別或行為不同的功能的運作方式不同。 條件式編譯最適合搭配共用資產專案中，相同的原始程式檔中有不同的符號定義的多個專案正在參考位置。

Xamarin 專案始終`__MOBILE__`這適用於 iOS 和 Android 的應用程式專案 （請注意雙底線前置和後置修正這些符號）。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Xamarin.iOS 定義`__IOS__`可用來偵測 iOS 裝置。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

另外還有監看式及電視專用符號：

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

只應該編譯為 Xamarin.Android 應用程式的程式碼可以使用下列程式碼

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每個應用程式開發介面版本也會定義新的編譯器指示詞，因此類似的程式碼可讓您將功能加入如果較新的 API 為目標。 每個應用程式開發介面層級包含所有 '低' 的層級符號。 這項功能不是真的適用於支援多個平台。通常`__ANDROID__`符號就已足夠。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

目前沒有內建符號 Xamarin.Mac，但是您可以加入您自己的 Mac 應用程式專案**選項 > 建置 > 編譯器**中**定義符號**方塊中，或編輯 **.csproj**檔案，並在該處加入 (例如`__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

Windows Phone 應用程式會定義兩個符號 –`WINDOWS_PHONE`和`SILVERLIGHT`–，可用來為目標平台程式碼。 這些不需要執行的底線像 Xamarin 平台符號周圍的地方。


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>使用條件式編譯

條件式編譯的簡單案例研究的範例設定 SQLite 資料庫檔案的檔案位置。 三個平台都有稍微不同的需求，針對指定的檔案位置：

-   **iOS** – Apple 慣用非使用者資料放在特定位置 （程式庫目錄），但沒有任何系統常數做為這個目錄。 平台專屬的程式碼，才能建立正確路徑。
-   **Android** – 所傳回的系統路徑`Environment.SpecialFolder.Personal`是可接受的位置來儲存資料庫檔案。
-   **Windows Phone** – 隔離儲存區儲存機制不允許完整路徑來指定，只要相對路徑和檔案名稱。
-   **通用 Windows 平台**– 使用`Windows.Storage`應用程式開發介面。

下列程式碼以確保使用條件式編譯`DatabaseFilePath`是正確的每個平台：

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

結果是類別，可建立且用於所有平台，將 SQLite 資料庫檔案放在每個平台上的不同位置。

