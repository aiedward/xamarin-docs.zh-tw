---
title: 第2章的摘要。 應用程式剖析
description: 建立 Mobile Apps Xamarin.Forms ：第2章的摘要。 應用程式剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 49b7a049444fe87f9237afb18935b74638fa9ebf
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370500"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第2章的摘要。 應用程式剖析

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

在 Xamarin.Forms 應用程式中，會佔用畫面空間的物件稱為 *視覺元素* ，由 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 類別封裝。 視覺元素可以分割成三個對應至這些類別的類別：

- [頁面](xref:Xamarin.Forms.Page)
- [版面配置](xref:Xamarin.Forms.Layout)
- [檢視](xref:Xamarin.Forms.View)

衍生的會 `Page` 佔用整個畫面，或幾乎整個螢幕。 頁面的子系通常是用 `Layout` 來組織子視覺元素的衍生專案。 的子系 `Layout` 可以是其他 `Layout` 類別或 `View` 衍生 (通常稱為 *元素* ) ，也就是像是文字、點陣圖、滑杆、按鈕、清單方塊等的熟悉物件。

本章將示範如何建立應用程式，方法是將焦點放在上 [`Label`](xref:Xamarin.Forms.Label) ，也就是 `View` 顯示文字的衍生。

## <a name="say-hello"></a>問好

安裝 Xamarin platform 之後，您就可以 Xamarin.Forms 在 Visual Studio 或 Visual Studio for Mac 中建立新的解決方案。 [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解決方案針對通用程式碼使用可移植的類別庫。

> [!NOTE]
> 便攜類別庫已由 .NET Standard 程式庫取代。 書籍中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

這個範例會示範 Xamarin.Forms 在 Visual Studio 中建立的方案，而不進行修改。 解決方案包含四個專案：

- 您 [**好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，) 其他專案共用的便攜類別庫 (PCL
- [**>droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，適用于 Android 的應用程式專案
- 您 [**好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，ios 的應用程式專案
- [**嗨**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，通用 Windows 平臺 (Windows 10 和 Windows 10 行動裝置版的應用程式專案) 

> [!NOTE]
> Xamarin.Forms 不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 行動裝置版，但 Xamarin.Forms 應用程式會在 Windows 10 桌面上執行。

您可以讓這些應用程式中的任何一個專案成為啟始專案，然後在裝置或模擬器上建立並執行程式。

在許多程式中 Xamarin.Forms ，您不會修改應用程式專案。 這些通常只是用來啟動程式的小存根。 您大部分的焦點都是所有應用程式通用的程式庫。

## <a name="inside-the-files"></a>檔案內

**Hello** 程式所顯示的視覺效果會定義在類別的函式中 [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) 。 `App` 衍生自 Xamarin.Forms 類別 [`Application`](xref:Xamarin.Forms.Application) 。

> [!NOTE]
> 用來建立具有 XAML 檔案之頁面的 Visual Studio 方案範本 Xamarin.Forms 。 除非 [第7章](chapter07.md)，否則本書未涵蓋 XAML。

**Hello** PCL 專案的 [ **參考** ] 區段包含下列 Xamarin.Forms 元件：

- **Xamarin.Forms.核心**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.平臺**

五個應用程式專案的 [ **參考** ] 區段包含適用于個別平臺的其他元件：

- **Xamarin.Forms.Platform. Android**
- **Xamarin.Forms.Platform. iOS**
- **Xamarin.Forms.Platform. UWP**
- **Xamarin.Forms.Platform. WinRT**
- **Xamarin.Forms.平臺：平板電腦**
- **Xamarin.Forms.Platform. Phone**

> [!NOTE]
> 這些專案的 **參考** 區段不會再列出元件。 相反地，專案檔會包含參考 NuGet 套件的 **PackageReference** 標記 Xamarin.Forms 。 Visual Studio 中的 [ **參考** ] 區段會列出 **Xamarin.Forms** 封裝，而不是 Xamarin.Forms 元件。

每個應用程式專案都包含 `Forms.Init` 命名空間中靜態方法的呼叫 `Xamarin.Forms` 。 這會初始化連結 Xamarin.Forms 庫。 `Forms.Init`針對每個平臺定義了不同的版本。 您可以在下列類別中找到這個方法的呼叫：

- Ios： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP： [ `App` class、 `OnLaunched` method](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

此外，每個平臺都必須具現化 `App` 共用程式庫中的類別位置。 這會發生在下列類別的呼叫中 `LoadApplication` ：

- Ios： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

否則，這些應用程式專案是正常的「不執行任何動作」程式。

## <a name="pcl-or-sap"></a>PCL 或 SAP？

您可以 Xamarin.Forms 使用可攜類別庫中的通用程式碼 (PCL) 或 (SAP) 的共用資產專案來建立解決方案。 若要建立 SAP 解決方案，請選取 Visual Studio 中的共用選項。 [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解決方案會示範沒有修改的 SAP 範本。

> [!NOTE]
> 便攜類別庫已由 .NET Standard 程式庫取代。 書籍中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。 否則，PCL 和 .NET Standard 程式庫在概念上非常類似。

程式庫方法會將平臺應用程式專案所參考的程式庫專案中的所有通用程式碼組合在一起。 利用 SAP 方法，常見的程式碼實際上存在於所有平臺應用程式專案中，並在這些專案之間共用。

大部分 Xamarin.Forms 的開發人員偏好使用程式庫方法。 在本書中，大部分的解決方案都使用程式庫。 使用 SAP 的系統會在專案名稱中包含 **SAP** 尾碼。

利用 SAP 方法，共用專案中的程式碼可以使用 c # 預處理器指示詞， (`#if` 、# `elif` 和 `#endif`) 搭配這些預先定義的識別碼，為各種平臺執行不同的程式碼：

- iOS：`__IOS__`
- Android：`__ANDROID__`
- UWP `WINDOWS_UWP`

在共用的程式庫中，您可以在執行時間判斷您正在執行的平臺，如本章稍後所示。

## <a name="labels-for-text"></a>文字標籤

[**問候語**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解決方案示範如何將新的 c # 檔案新增至 **問候語** 專案。 這個檔案會定義一個名為 `GreetingsPage` 的類別，該類別會衍生自 `ContentPage` 。 在本書中，大部分的專案都包含單一的 `ContentPage` 衍生專案，其名稱是附加尾碼的專案名稱 `Page` 。

此函式 `GreetingsPage` [`Label`](xref:Xamarin.Forms.Label) 會具現化視圖，也就是 Xamarin.Forms 顯示文字的視圖。 [`Text`](xref:Xamarin.Forms.Label.Text)屬性會設定為所顯示的文字 `Label` 。 此程式會將設定 `Label` 為的 `Content` 屬性 `ContentPage` 。 然後，類別的函式會將它具現 `App` 化 `GreetingsPage` ，並將它設定為其 `MainPage` 屬性。

文字會顯示在頁面的左上角。 在 iOS 上，這表示它會與頁面的狀態列重迭。 有幾個解決此問題的解決方案：

### <a name="solution-1-include-padding-on-the-page"></a>方案1。 在頁面上包含填補

設定 [`Padding`](xref:Xamarin.Forms.Page.Padding) 頁面上的屬性。 `Padding` 的類型是 [`Thickness`](xref:Xamarin.Forms.Thickness) 具有四個屬性的結構：

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` 在頁面內定義要排除內容的區域。 這可讓 `Label` 避免覆寫 iOS 狀態列。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解決方案2。 只針對 iOS (SAP 包含填補) 

在 iOS 上使用具有 c # 預處理器指示詞的 SAP，來設定「填補」屬性。 這會在 [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) 方案中示範。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解決方案3： 只針對 iOS (PCL 或 SAP) 包含填補

在本書使用的版本中 Xamarin.Forms ， `Padding` 可以使用 [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 或靜態方法選取 PCL 或 SAP 中的 iOS 特定屬性 [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) 。 這些方法現在已淘汰

這些 `Device.OnPlatform` 方法可用來執行平臺特定的程式碼，或選取平臺特定的值。 就內部而言，它們會使用 [`Device.OS`](xref:Xamarin.Forms.Device.OS) 靜態的唯讀屬性，此屬性會傳回列舉的成員 [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) ：

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 適用于 UWP 裝置。

`Device.OnPlatform`方法、 `Device.OS` 屬性和 `TargetPlatform` 列舉現在都已被取代。 相反地，請使用 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) 屬性，並比較傳回 `string` 值與下列靜態欄位：

- [`iOS`](xref:Xamarin.Forms.Device.iOS)，字串 "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android)，字串 "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP)，"UWP" 字串，參考通用 Windows 平臺

[`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom)靜態唯讀屬性是相關的。 這會傳回 [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) 具有下列成員的成員：

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) 未使用

針對 iOS 和 Android，和之間的 `Tablet` 截止 `Phone` 是600單位的直向寬度。 針對 Windows 平臺， `Desktop` 表示在 Windows 10 下執行的 uwp 應用程式，並 `Phone` 指出在 Windows 10 應用程式下執行的 uwp 應用程式。

## <a name="solution-3a-set-margin-on-the-label"></a>方案3a。 設定標籤的邊界

這個 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性的引入時間太晚而無法包含在書中，但也是型別， `Thickness` 而且您可以在上將它設定 `Label` 為，以定義包含在視圖的版面配置計算中的區域以外的區域。

`Padding`屬性只適用于 [`Layout`](xref:Xamarin.Forms.Layout) 和 [`Page`](xref:Xamarin.Forms.Page) 衍生。 `Margin`屬性可用於所有衍生的 [`View`](xref:Xamarin.Forms.View) 。

## <a name="solution-4-center-the-label-within-the-page"></a>解決方案4。 在頁面中將標籤置中

您可以在 (中置中， `Label` `Page` 或將的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定 `Label` 為類型的值，將它放在其他八個位置的其中一個) [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 。 `LayoutOptions`結構會定義兩個屬性：

- 型別為的 [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) 屬性 [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) （具有四個成員的列舉）： [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) ，這表示根據方向，，，表示靠左或靠上，根據方向 [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill) 。

- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)型別的屬性 `bool` 。

這些屬性通常不會直接使用。 相反地，這兩個屬性的組合是由下列類型的八個靜態唯讀屬性提供 `LayoutOptions` ：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` 和 `VerticalOptions` 是配置中最重要的屬性 Xamarin.Forms ，並將在 [**第4章中更詳細地討論。滾動堆疊**](chapter04.md)。

以下是將 `HorizontalOptions` 和 `VerticalOptions` 屬性設定為的結果 `Label` `LayoutOptions.Center` ：

[![問候節目的三重螢幕擷取畫面](images/ch02fg05-small.png "水準和垂直置中標籤")](images/ch02fg05-large.png#lightbox "水準和垂直置中標籤")

## <a name="solution-5-center-the-text-within-the-label"></a>解決方案5。 將標籤中的文字置中

您也可以將文字置中 (，或將它放在頁面) 的其他其他位置，方法是將 [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) 的和屬性設定 `Label` 為 [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) 列舉的成員：

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start)，表示靠左或上 (，視方向而定) 
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End)，視方向而定，表示右邊或下 () 

這兩個屬性僅由定義 `Label` ，而 `HorizontalAlignment` 和 `VerticalAlignment` 屬性是由 `View` 所有衍生的定義和繼承 `View` 。 視覺效果看起來可能很類似，但與下一章所示範的不同。

## <a name="related-links"></a>相關連結

- [第2章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第2章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第2章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [消費者入門 Xamarin.Forms](~/get-started/index.yml)
