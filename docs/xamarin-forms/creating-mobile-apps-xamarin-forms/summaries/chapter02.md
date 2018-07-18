---
title: 第 2 章的摘要。 應用程式剖析
description: 使用 Xamarin.Forms 建立行動應用程式： 第 2 章摘要。 應用程式剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: d1daceba29e45adf64947c89555cc4e75a850d32
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995273"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章的摘要。 應用程式剖析

在 Xamarin.Forms 應用程式中，所佔用的空間，在螢幕上的物件稱為*視覺項目*、 由封裝[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別。 視覺項目可以分成三個類別對應至這些類別：

- [頁面](xref:Xamarin.Forms.Page)
- [版面配置](xref:Xamarin.Forms.Layout)
- [檢視](xref:Xamarin.Forms.View)

A`Page`衍生項目會佔用整個螢幕或幾乎整個螢幕。 頁面的子系，通常是`Layout`來組織子視覺元素的衍生項目。 子系`Layout`可以是其他`Layout`類別或`View`衍生項目 (通常稱為*項目*)，這是熟悉的物件，例如文字、 點陣圖、 滑桿、 按鈕、 清單方塊等等。

本章會示範如何建立應用程式著重[ `Label` ](xref:Xamarin.Forms.Label)，也就是`View`顯示文字的衍生項目。

## <a name="say-hello"></a>迎接嶄新

安裝 Xamarin 平台的情況下，您可以建立新的 Xamarin.Forms 方案在 Visual Studio 或 Visual Studio for mac。 [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解決方案會使用通用的程式碼的可攜式類別庫。 它會示範 Xamarin.Forms 方案在 Visual Studio 中建立並進行任何修改。 方案是由六個專案 （其中兩個不會使用目前的 Xamarin.Forms 方案範本建立的最後一個） 所組成：

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，由其他專案共用的可攜式類別庫 (PCL)
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，適用於 Android 的應用程式專案
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，適用於 iOS 的應用程式專案
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，適用於通用 Windows 平台 （Windows 10 和 Windows 10 行動裝置版） 的應用程式專案
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows)，Windows 8.1 應用程式專案
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone)，適用於 Windows Phone 8.1 應用程式專案

您可以進行任何這類應用程式專案啟始專案，然後建置並在裝置或模擬器上執行程式。

在許多 Xamarin.Forms 應用程式中，您將不會修改應用程式專案。 這些通常會保留小的虛設常式，只是為了啟動程式。 大部分的焦點會通用於所有應用程式的可攜式類別庫。

## <a name="inside-the-files"></a>檔案內

所顯示的視覺效果**Hello**的建構函式中所定義的程式[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)類別。 `App` 衍生自 Xamarin.Forms 類別[ `Application` ](xref:Xamarin.Forms.Application)。

**參考**一節**Hello** PCL 專案，包括下列的 Xamarin.Forms 組件：

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

**參考**五個應用程式專案的各節包含適用於個別的平台的其他組件：

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

每個應用程式專案包含呼叫靜態`Forms.Init`方法中的`Xamarin.Forms`命名空間。 這會初始化 Xamarin.Forms 程式庫。 不同版本的`Forms.Init`定義每個平台。 呼叫此方法位於下列類別：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App`類別，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App`類別，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App`類別，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

此外，每個平台必須具現化`App`類別在 PCL 中的位置。 這個問題發生在呼叫`LoadApplication`中的下列類別：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1： [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

否則，這些應用程式專案會是一般的"do nothing"程式。

## <a name="pcl-or-sap"></a>PCL 或 SAP 嗎？

您可使用可攜式類別庫 (PCL) 或共用資產專案 (SAP) 中的通用程式碼會建立 Xamarin.Forms 方案。 若要建立的 SAP 解決方案，請在 Visual Studio 中選取 [共用] 選項。 [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解決方案示範不需要修改的 SAP 範本。

PCL 方法組合的平台應用程式專案所參考的程式庫專案中的所有常見的程式都碼。 使用 SAP 方法時，常見的程式碼，有效地存在於所有平台應用程式專案，並在它們之間共用。

大部分的 Xamarin.Forms 開發人員偏好使用 PCL 方法。 在本書中，大部分的解決方案會是 PCL。 使用 SAP 包含**Sap**中的專案名稱後置字元。

若要支援所有的 Xamarin.Forms 平台，使用 PCL 的.NET 版本必須滿足下列平台：

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin.IOS （傳統）

這就是電腦設定檔 111。

使用 SAP 方法共用專案中的程式碼可以使用 C# 前置處理器指示詞執行不同的程式碼適用於各種平台 (`#if`，#`elif`，和`#endif`) 使用這些預先定義的識別項：

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`
- Windows 8.1： `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

PCL 中，如您在本章稍後所見，可以判斷您在執行階段，在執行何種平台。

## <a name="labels-for-text"></a>標籤文字

[ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解決方案示範如何將新 C# 檔案新增至**Greetings**專案。 這個檔案會定義名為類別`GreetingsPage`衍生自`ContentPage`。 在本書中，大部分的專案包含單一`ContentPage`衍生項目，其名稱是後置詞的專案名稱`Page`附加。

`GreetingsPage`建構函式具現化[ `Label` ](xref:Xamarin.Forms.Label)檢視中，也就是 [Xamarin.Forms] 檢視會顯示文字。 [ `Text` ](xref:Xamarin.Forms.Label.Text)屬性設定為所顯示的文字`Label`。 此程式設定`Label`要`Content`屬性`ContentPage`。 建構函式`App`類別接著會執行個體化`GreetingsPage`並將它設定為其`MainPage`屬性。

文字會顯示頁面的左上角。 在 iOS 上，這表示它重疊頁面的 [狀態] 列。 有數種方法解決這個問題：

### <a name="solution-1-include-padding-on-the-page"></a>解決方案 1。 包含頁面上的邊框距離

設定[ `Padding` ](xref:Xamarin.Forms.Page.Padding)頁面上的屬性。 `Padding` 屬於類型[ `Thickness` ](xref:Xamarin.Forms.Thickness)，具有四個屬性的結構：

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` 定義在網頁內排除內容的其中一個區域。 這可讓`Label`以避免覆寫 iOS 狀態列。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解決方案 2。 包含與邊框距離只適用於 iOS (只有 SAP)

只使用在 iOS 上的 SAP 以 C# 前置處理器指示詞中設定 'Padding' 屬性。 這示範於[ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)解決方案。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解決方案 3。 包含與邊框距離只適用於 iOS （PCL 或 SAP）

Xamarin.Forms 用於活頁簿，新版`Padding`可以選取 iOS 的 PCL 」 或 「 SAP 的特定屬性，使用[ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))或是[ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*)靜態方法。 這些方法現在已被取代

`Device.OnPlatform`方法用來執行平台特定程式碼，或選取平台特定的值。 就內部而言，它們會使利用[ `Device.OS` ](xref:Xamarin.Forms.Device.OS)靜態唯讀屬性，會傳回屬於[ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform)列舉型別：

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) Windows 8.1、 Windows Phone 8.1，和 UWP 的所有裝置。
- [`WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)之前用來識別 Windows Phone 8.0，但會現在未使用
- [`Other`](xref:Xamarin.Forms.TargetPlatform.Other) 未使用

`Device.OnPlatform`方法，`Device.OS`屬性，而`TargetPlatform`列舉型別都現在已過時。 請改用[ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform)屬性及比較`string`傳回具有下列靜態欄位的值：

- [`iOS`](xref:Xamarin.Forms.Device.iOS)「 iOS"的字串
- [`Android`](xref:Xamarin.Forms.Device.Android)"Android"的字串
- [`UWP`](xref:Xamarin.Forms.Device.UWP)字串"UWP"，指的 Windows 執行階段平台
- `Windows`字串"Windows"Windows 執行階段 （Windows 8.1 和 Windows Phone 8.1，已被取代）
- `WinPhone`字串"WinPhone 「 適用於 Windows Phone 8.0 （已過時）

[ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom)靜態的唯讀屬性相關。 這會傳回的成員[ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom)，其中包含這些成員：

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) 未使用

適用於 iOS 和 Android 之間截止`Tablet`和`Phone`是直向寬度為 600 的單位。 適用於 Windows 平台`Desktop`表示執行 Windows 10 UWP 應用程式`Tablet`是 Windows 8.1 的程式，和`Phone`指出 Windows 10 或 Windows Phone 8.1 應用程式執行的 UWP 應用程式。

## <a name="solution-3a-set-margin-on-the-label"></a>方案 3a。 設定邊界標籤

[ `Margin` ](xref:Xamarin.Forms.View.Margin)引進了屬性太晚要包含在活頁簿，但它也是型別`Thickness`並將它設定在`Label`來定義外部的檢視，包含在計算區域檢視表的版面配置。

`Padding`屬性上才有[ `Layout` ](xref:Xamarin.Forms.Layout)並[ `Page` ](xref:Xamarin.Forms.Page)衍生項目。 `Margin`屬性可用於所有[ `View` ](xref:Xamarin.Forms.View)衍生項目。

## <a name="solution-4-center-the-label-within-the-page"></a>解決方案 4。 在頁面中的將標籤置

您可以置`Label`內`Page`（或將它放在八個其他地方的其中一個） 藉由設定[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性`Label`值的型別[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)。 `LayoutOptions`結構會定義兩個屬性：

- [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment)屬性的型別[ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment)，含有四個成員的列舉類型： [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start)，這表示左方或上方取決於方向[ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center)， [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End)，這表示右邊緣或下根據方向，以及[ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill)。

- [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands)型別的屬性`bool`。

通常這些屬性不會直接使用。 相反地，這兩個屬性的組合所提供的八個靜態唯讀屬性的型別`LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` 並`VerticalOptions`都是最重要屬性在 Xamarin.Forms 版面配置，並且會更詳細地討論[**第 4 章。捲動堆疊**](chapter04.md)。

以下是結果與`HorizontalOptions`並`VerticalOptions`的屬性`Label`都設為`LayoutOptions.Center`:

[![問候程式的三個螢幕擷取畫面](images/ch02fg05-small.png "水平和垂直置中 標記")](images/ch02fg05-large.png#lightbox "水平和垂直置中加上標籤")

## <a name="solution-5-center-the-text-within-the-label"></a>解決方案 5。 在標籤內的文字置中

您也可以為文字置中 （或將它放在頁面上的八個其他位置） 藉由設定[ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment)並[ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment)屬性`Label`成員[ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment)列舉型別：

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start)表示左或頂端 （取決於方向）
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End)表示右方或下方 （根據方向）

這兩個屬性定義只有`Label`，而`HorizontalAlignment`並`VerticalAlignment`來定義屬性`View`，而且所有繼承`View`衍生項目。 視覺效果可能看似雷同，但它們是非常不同，如下一章中所示。



## <a name="related-links"></a>相關連結

- [第 2 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第 2 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第 2 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [開始使用 Xamarin.Forms](~/xamarin-forms/get-started/index.md)
