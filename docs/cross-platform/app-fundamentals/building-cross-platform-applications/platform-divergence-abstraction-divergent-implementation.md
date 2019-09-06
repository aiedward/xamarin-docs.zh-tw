---
title: 第 4 部分 - 處理多個平台
description: 本檔說明如何根據平臺或功能來處理應用程式分歧。 它討論螢幕大小、導覽比喻、觸控和手勢、推播通知和介面範例，例如清單和索引標籤。
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: fb01d0ca56365fa95aa563ca99394dea39dc7d31
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288887"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4 部分 - 處理多個平台

## <a name="handling-platform-divergence-amp-features"></a>處理平臺分歧&amp;功能

分歧不只是「跨平臺」的問題;「相同」平臺上的裝置具有不同的功能（尤其是可用的各種 Android 裝置）。 最明顯的和基本是螢幕大小，但其他裝置屬性可能會有所不同，而且需要應用程式檢查特定功能，並根據其目前狀態（或缺少）以不同的方式運作。

這表示所有應用程式都需要處理正常的功能降低，或呈現不討喜、最低公的功能集。 Xamarin 與每個平臺的原生 Sdk 深入整合，可讓應用程式利用平臺特定的功能，因此設計應用程式以使用這些功能是合理的。

如需平臺的功能差異總覽，請參閱平臺功能檔。

## <a name="examples-of-platform-divergence"></a>平臺分歧的範例

### <a name="fundamental-elements-that-exist-across-platforms"></a>跨平臺存在的基本元素

有一些通用的行動應用程式特性。
這些是通常適用于所有裝置的較高層級概念，因此可構成應用程式設計的基礎：

- 透過索引標籤或功能表的特徵選取
- 資料和捲軸清單
- 單一資料檢視
- 編輯單一資料檢視
- 向後流覽

設計您的高階螢幕流程時，您可以根據這些概念來建立常見的使用者體驗。

### <a name="platform-specific-attributes"></a>平臺特定屬性

除了存在於所有平臺上的基本元素之外，您還必須解決設計中的主要平臺差異。 您可能需要考慮（並專門撰寫程式碼來處理）這些差異：

- **螢幕大小**–某些平臺（例如 iOS 和舊版 Windows Phone 版本）具有標準化的螢幕大小，其目標相當簡單。 Android 裝置具有各種不同的螢幕尺寸，需要在您的應用程式中提供更多的支援。
- **導覽比喻**–不同的平臺（例如 硬體 [上一頁] 按鈕、全景 UI 控制項）和平臺內（Android 2 和4、iPhone vs iPad）。
- **鍵盤**–有些 Android 裝置具有實體鍵盤，而有些則只有軟體鍵盤。 當軟鍵盤遮蔽螢幕的一部分時，會偵測到這些差異的敏感性程式碼。
- **觸控和手勢**–手勢辨識的作業系統支援會有所不同，特別是在每個作業系統的較舊版本中。 舊版的 Android 對觸控作業的支援非常有限，這表示支援較舊的裝置可能需要個別的程式碼
- **推播通知**–每個平臺上有不同的功能/執行方式（例如 Windows 上的動態磚）。

### <a name="device-specific-features"></a>裝置特定功能

判斷應用程式所需的最基本功能必須是什麼;或者，在決定要在每個平臺上利用的其他功能。 需要程式碼來偵測功能，並停用功能或供應專案替代方案（例如 地理位置的替代方式可能是讓使用者輸入位置或從對應中選擇）：

- **相機**–各裝置的功能不同：有些裝置沒有相機，有些則有前端和後端的相機。 有些攝影機能夠錄製錄影。
- **地理位置 & maps** –所有裝置上不會有 GPS 或 wi-fi 位置的支援。 應用程式也必須滿足每個方法所支援的各種精確度層級。
- **加速計、陀螺儀和羅盤**–這些功能通常只會在每個平臺上的裝置選擇中找到，因此應用程式幾乎都需要在硬體不受支援時提供回溯。
- **Twitter 和 Facebook** -僅限 IOS5 和 iOS6 上的「內建」。 在舊版和其他平臺上，您必須使用每個服務的 API 直接提供自己的驗證功能和介面。
- **近距離無線通訊（NFC）** –僅限（部分） Android 手機（在撰寫時）。

## <a name="dealing-with-platform-divergence"></a>處理平臺分歧

有兩種不同的方法可以從相同的程式碼基底支援多個平臺，每一種都有自己的優點和缺點。

- **平臺抽象**-商業面板模式，提供跨平臺的統一存取，並將特定平臺的整合抽象化成單一整合的 API。
- **分歧的實行**–透過介面和繼承或條件式編譯等架構工具，透過分歧的方式來叫用特定的平臺功能。

## <a name="platform-abstraction"></a>平台抽象

### <a name="class-abstraction"></a>類別抽象

使用在共用程式碼中定義的介面或基類，並在平臺特定專案中執行或擴充。 使用類別抽象來撰寫和擴充共用程式碼，特別適用于可移植的類別庫，因為它們有一組有限的架構子集可供使用，而且不能包含編譯器指示詞來支援平臺特定的程式碼分支。

#### <a name="interfaces"></a>介面

使用介面可讓您執行平臺特定的類別，這些類別仍然可以傳遞至您的共用程式庫，以利用通用程式碼。

介面是在共用程式碼中定義，並以參數或屬性的形式傳遞至共用程式庫。

然後平臺特定的應用程式就可以執行介面，並繼續利用共用程式碼來處理它。

 **優點**

此執行可以包含平臺特定的程式碼，甚至是參考平臺特定的外部程式庫。

 **缺點**

必須建立共用程式碼並將其傳入。 如果在共用程式碼中使用介面，則最後會透過多個方法參數傳遞，或以其他方式向下推送至呼叫鏈。 如果共用程式碼使用許多不同的介面，就必須在共用程式碼的某處建立和設定它們。

#### <a name="inheritance"></a>繼承

共用程式碼可以在一或多個平臺特定專案中，執行可延伸的抽象或虛擬類別。 這類似于使用介面，但已實作為一些行為。 介面或繼承是否為較佳的設計選擇，有不同的視點：特別是C#因為只允許單一繼承，所以可以決定您的 api 未來的設計方式。 請小心使用繼承。

介面的優點和缺點同樣適用于繼承，還有另一個優點，就是基類可以包含某些實作為程式碼（可能是可選擇性擴充的整個平臺不可知的實作為）。

## <a name="xamarinforms"></a>Xamarin.Forms

請參閱[Xamarin. 表單](~/get-started/index.yml)檔。

### <a name="other-cross-platform-libraries"></a>其他跨平臺程式庫

這些程式庫也提供適用于開發人員C#的跨平臺功能：

- [**Xamarin**](~/essentials/index.md) -適用于一般功能的跨平臺 api。
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) –跨平臺2d 圖形。

## <a name="conditional-compilation"></a>條件式編譯

在某些情況下，您的共用程式碼仍需要在每個平臺上以不同的方式運作，可能會存取行為不同的類別或功能。 條件式編譯最適合與共享資產專案搭配使用，其中相同的原始程式檔會在已定義不同符號的多個專案中被參考。

Xamarin 專案一律會`__MOBILE__`定義 iOS 和 Android 應用程式專案的 true （請注意這些符號的雙底線前置和後置修正）。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

[Xamarin] 定義`__IOS__`可用來偵測 iOS 裝置的。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

還有一些監看式和 TV 特定的符號：

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

只應編譯成 Xamarin 的程式碼。 Android 應用程式可以使用下列

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每個 API 版本也會定義新的編譯器指示詞，因此，這類程式碼可讓您在較新的 Api 為目標時新增功能。 每個 API 層級都包含「較低」層級的符號。 這項功能對於支援多個平臺而言並不實用;`__ANDROID__`符號通常就已足夠。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

目前不會有 Xamarin 的內建符號，但是您可以在 Mac 應用程式專案選項中新增自己的符號，> 在 [**定義符號**] 方塊中**建立 > 編譯器**，或編輯 **.csproj**檔案並在該處`__MAC__`新增（例如）

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

使用 `WINDOWS_UWP`。 字串周圍沒有任何底線，例如 Xamarin 平臺符號。

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>使用條件式編譯

條件式編譯的簡單案例研究範例是設定 SQLite 資料庫檔案的檔案位置。 這三個平臺在指定檔案位置方面有些許不同的需求：

- **iOS** – Apple 偏好將非使用者資料放在特定位置（程式庫目錄），但此目錄沒有系統常數。 需要平臺特定的程式碼，才能建立正確的路徑。
- **Android** –所傳回`Environment.SpecialFolder.Personal`的系統路徑是可接受的位置，可儲存資料庫檔案。
- **Windows Phone** –隔離儲存機制不允許指定完整路徑，只是相對路徑和檔案名。
- **通用 Windows 平臺**–使用`Windows.Storage` api。

下列程式碼會使用條件式編譯， `DatabaseFilePath`確保每個平臺的都是正確的：

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

結果是可以在所有平臺上建立和使用的類別，並將 SQLite 資料庫檔案放在每個平臺上的不同位置。
