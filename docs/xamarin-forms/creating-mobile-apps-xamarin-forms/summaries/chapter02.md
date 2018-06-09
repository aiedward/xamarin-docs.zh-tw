---
title: 第 2 章的摘要。 應用程式的結構
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 第 2 章的摘要。 應用程式的結構
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 208cf28341ceaa43d1c56b4f5086dc98febee6be
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242743"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章的摘要。 應用程式的結構

Xamarin.Forms 應用程式中所佔用的空間，在螢幕上的物件稱為*視覺項目*、 由封裝[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)類別。 視覺項目可以分成三個類別對應至這些類別：

- [頁面](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [版面配置](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [檢視](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

A`Page`衍生項目佔據整個螢幕或幾乎整個螢幕。 網頁的子系，通常是`Layout`來組織子視覺元素的衍生項目。 子系`Layout`可以是其他`Layout`類別或`View`衍生項目 (通常稱為*元素*)，這是很熟悉的物件，例如文字、 點陣圖、 滑桿、 按鈕、 清單方塊等。

本章示範如何建立應用程式將焦點放在[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)，也就是`View`顯示文字的衍生項目。

## <a name="say-hello"></a>看看

安裝 Xamarin 平台的情況下，您可以建立新的 Xamarin.Forms 方案在 Visual Studio 或 Visual Studio for mac。 [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解決方案使用可攜式類別庫的通用程式碼。 它會示範 Visual Studio 中，不需修改建立 Xamarin.Forms 方案。 解決方案包含六個專案 （其中兩個不會建立目前 Xamarin.Forms 方案範本的最後一個）：

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，其他專案共用可攜式類別程式庫 (PCL)
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，適用於 Android 的應用程式專案
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，適用於 iOS 的應用程式專案
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，針對通用 Windows 平台 （Windows 10 和 Windows 10 行動裝置版） 的應用程式專案
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows)，Windows 8.1 的應用程式專案
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone)，適用於 Windows Phone 8.1 的應用程式專案

您可以進行任何這類應用程式專案啟動專案，然後建置並在裝置或模擬器上執行程式。

在許多 Xamarin.Forms 應用程式中，您將不會修改應用程式專案。 這些通常會保留小的虛設常式，只是為了啟動程式。 大部分的焦點會可攜式類別庫通用於所有應用程式。

## <a name="inside-the-files"></a>檔案內

所顯示的視覺效果**Hello**程式定義的建構函式中[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)類別。 `App` 衍生自 Xamarin.Forms 類別[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)。

**參考**區段**Hello** PCL 專案，包括下列 Xamarin.Forms 組件：

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

**參考**的五個應用程式專案的各節包含適用於個別的平台的其他組件：

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

每個應用程式的專案包含呼叫靜態`Forms.Init`方法中的`Xamarin.Forms`命名空間。 這會初始化 Xamarin.Forms 程式庫。 不同版本的`Forms.Init`定義每個平台。 呼叫此方法位於下列類別：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App`類別`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App`類別`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App`類別`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

此外，每個平台必須具現化`App`類別 PCL 中的位置。 這個問題發生在呼叫`LoadApplication`中的下列類別：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1： [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

否則，這些應用程式專案是一般的 「 執行任何動作執行 」 程式。

## <a name="pcl-or-sap"></a>PCL 或 SAP 嗎？

您可使用可攜式類別程式庫 (PCL) 或共用資產專案 (SAP) 中常見的程式碼會建立 Xamarin.Forms 方案。 若要建立 SAP 的解決方案，請在 Visual Studio 中選取 [共用] 選項。 [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解決方案示範 SAP 具有的範本進行任何修改。

平台應用程式專案所參考的程式庫專案中的所有通用程式都碼 PCL 方法的組合。 使用 SAP 方法時，一般程式碼，有效地存在於所有平台應用程式專案，並在它們之間共用。

大部分的 Xamarin.Forms 開發人員偏好使用 PCL 方法。 這個活頁簿中的大多數解決方案是 PCL。 使用 SAP 包含**Sap**專案名稱中的後置詞。

若要支援所有 Xamarin.Forms 平台，使用 PCL 的.NET 版本必須配合下列平台：

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin.IOS （傳統）

這稱為電腦設定檔 111。

SAP 方法使用共用專案中的程式碼可以使用 C# 前置處理器指示詞執行不同的程式碼用於各種平台 (`#if`，#`elif`，和`#endif`) 使用這些預先定義的識別項：

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`
- Windows 8.1： `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

PCL 中，您會發現在本章稍後，可以判斷您在執行階段，在執行何種平台。

## <a name="labels-for-text"></a>標籤文字

[ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)方案會示範如何加入新 C# 檔案**Greetings**專案。 此檔案會定義名為類別`GreetingsPage`衍生自`ContentPage`。 在這個活頁簿中，大部分的專案包含單一`ContentPage`其名稱是後置詞，專案的名稱衍生作品`Page`附加。

`GreetingsPage`建構函式具現化[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)檢視，即會顯示文字的 Xamarin.Forms 檢視。 [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性設定為顯示之文字`Label`。 此程式會設定為`Label`至`Content`屬性`ContentPage`。 建構函式`App`類別接著會執行個體化`GreetingsPage`並將它設定為其`MainPage`屬性。

頁面的左上角會顯示文字。 在 iOS 上，這表示它會重疊在頁面的 [狀態] 列。 有數種方法解決這個問題：

### <a name="solution-1-include-padding-on-the-page"></a>解決方案 1。 包含頁面上的邊框距離

設定[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/)頁面上的屬性。 `Padding` 型別[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)，具有四個屬性的結構：

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` 定義在網頁內的區域內容排除的位置。 這可讓`Label`以避免覆寫 iOS 狀態列。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解決方案 2。 包含與邊框距離只適用於 iOS (只有 SAP)

只有在使用 C# 前置處理器指示詞中使用 SAP iOS 上設定 '填補' 屬性。 這示範於[ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)方案。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>3 的方案。 包含與邊框距離只適用於 iOS （PCL 或 SAP）

在版本的活頁簿中，使用 Xamarin.Forms `Padding` PCL 或 SAP 中的 iOS 專屬的屬性可以使用選取[ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)或[ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/)靜態方法。 這些方法現在已被取代

`Device.OnPlatform`方法可以用來執行平台專屬程式碼，或選取特定平台值。 就內部而言，它們會使使用[ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/)靜態唯讀屬性，它會傳回的成員[ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/)列舉型別：

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) Windows 8.1、 Windows Phone 8.1，和所有 UWP 裝置。
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/)先前用來識別 Windows Phone 8.0，而是立即未使用
- [`Other`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Other/) 未使用

`Device.OnPlatform`方法`Device.OS`屬性，而`TargetPlatform`列舉型別都現在已被取代。 請改用[ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/)屬性並進行比較`string`傳回具有下列的靜態欄位的值：

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/)、 [iOS] 的字串
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/)"Android"的字串
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/)字串"UWP"，參考到 Windows 執行階段平台
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/)字串"Windows"為 Windows 執行階段 （Windows 8.1 和 Windows Phone 8.1）
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/)字串"WinPhone"適用於 Windows Phone 8.0

[ `Device.Idiom` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.Idiom/)相關靜態的唯讀屬性。 這會傳回的成員[ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/)，其中有這些成員：

- [`Desktop`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Desktop/)
- [`Tablet`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Tablet/)
- [`Phone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Phone/)
- [`Unsupported`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Unsupported/) 未使用

適用於 iOS 和 Android 之間截止`Tablet`和`Phone`是直向寬度為 600 的單位。 針對 Windows 平台`Desktop`表示執行 Windows 10 UWP 應用程式`Tablet`是 Windows 8.1 的程式，和`Phone`指出 Windows 10 或 Windows Phone 8.1 應用程式下執行的 UWP 應用程式。

## <a name="solution-3a-set-margin-on-the-label"></a>方案 3a。 設定邊界上的標籤

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)屬性引進太晚要包含在活頁簿，但是它也是型別`Thickness`並將它設定在`Label`以定義外檢視包含在計算區域檢視的版面配置。

`Padding`屬性上才有[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)和[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)衍生項目。 `Margin`屬性是供所有[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)衍生項目。

## <a name="solution-4-center-the-label-within-the-page"></a>4 的方案。 在頁面內將標籤置

您可以置`Label`內`Page`（或將其放在八個其他地方的其中一個） 藉由設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性`Label`值的型別[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)。 `LayoutOptions`結構會定義兩個屬性：

- [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/)型別的屬性[ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/)，具有四個成員的列舉： [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/)，這表示向左或取決於最上層方向[ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/)， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/)，右側或底端的方向，而定，這表示和[ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)。

- [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/)型別的屬性`bool`。

通常這些屬性都不會直接使用。 相反地，這兩個屬性的組合所提供的八個唯讀的靜態屬性型別`LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` 和`VerticalOptions`最重要的屬性，在 Xamarin.Forms 配置中，而且會更詳細地討論[**第 4 章。捲動堆疊**](chapter04.md)。

以下是結果與`HorizontalOptions`和`VerticalOptions`屬性`Label`都設定為`LayoutOptions.Center`:

[![三個螢幕擷取畫面的 greetings 程式](images/ch02fg05-small.png "水平及垂直置中加上標籤")](images/ch02fg05-large.png#lightbox "水平及垂直置中加上標籤")

## <a name="solution-5-center-the-text-within-the-label"></a>5 的方案。 在標籤內文字置中對齊

您也可以文字置中 （或將它放在其他八個頁面上的位置） 藉由設定[ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/)和[ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/)屬性`Label`成員[ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)列舉型別：

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/)表示 left 或 top （取決於方向）
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.End/)表示右側或底端 （取決於方向）

這兩個屬性定義只有`Label`，而`HorizontalAlignment`和`VerticalAlignment`定義的內容`View`，而且所有繼承`View`衍生項目。 視覺效果看起來類似，但因為下一章示範非常不同。



## <a name="related-links"></a>相關連結

- [第 2 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第 2 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第 2 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [開始使用 Xamarin.Forms](~/xamarin-forms/get-started/index.md)
