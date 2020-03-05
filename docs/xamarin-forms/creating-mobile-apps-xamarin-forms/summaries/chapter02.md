---
title: 第2章的摘要。 應用程式的剖析
description: 使用 Xamarin 建立 Mobile Apps：第2章的摘要。 應用程式的剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291823"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第2章的摘要。 應用程式的剖析

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

在 [Xamarin] 應用程式中，佔用螢幕空間的物件稱為「*視覺元素*」（ [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別所封裝）。 視覺元素可以分割成與這些類別對應的三個類別：

- [頁面](xref:Xamarin.Forms.Page)
- [版面配置](xref:Xamarin.Forms.Layout)
- [檢視](xref:Xamarin.Forms.View)

`Page` 的衍生會佔用整個螢幕，或幾乎全螢幕。 通常，頁面的子系是組織子視覺元素的 `Layout` 衍生。 `Layout` 的子系可以是其他 `Layout` 類別或 `View` 衍生專案（*通常稱為專案*），這是很熟悉的物件，例如文字、點陣圖、滑杆、按鈕、清單方塊等等。

本章示範如何藉由將焦點放在[`Label`](xref:Xamarin.Forms.Label)（這是顯示文字的 `View` 衍生）來建立應用程式。

## <a name="say-hello"></a>假設您好

安裝 Xamarin 平臺之後，您就可以在 Visual Studio 或 Visual Studio for Mac 中建立新的 Xamarin 表單方案。 [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解決方案會針對通用程式碼使用可移植的類別庫。

> [!NOTE]
> 可移植的類別庫已由 .NET Standard 程式庫取代。 本書中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

這個範例會示範在 Visual Studio 中建立的 Xamarin Forms 方案，而不進行任何修改。 解決方案包含四個專案：

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，另一個專案所共用的可移植類別庫（PCL）
- [**Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，適用于 Android 的應用程式專案
- 您[**好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，iOS 是 ios 的應用程式專案
- 適用于通用 Windows 平臺（Windows 10 和 Windows 10 行動裝置版）的應用程式專案（ [**UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)）

> [!NOTE]
> Xamarin 不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 行動裝置版，但是 Xamarin. Forms 應用程式會在 Windows 10 桌面上執行。

您可以將這些應用程式中的任何一個專案設為啟始專案，然後在裝置或模擬器上建立並執行程式。

在許多 Xamarin 程式中，您都不會修改應用程式專案。 這些通常會保持小型的存根，以啟動程式。 您的焦點大部分都是所有應用程式通用的程式庫。

## <a name="inside-the-files"></a>檔案內部

**Hello**程式所顯示的視覺效果是在[`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)類別的函式中定義。 `App` 衍生自[`Application`](xref:Xamarin.Forms.Application)的 [Xamarin] 類別。

> [!NOTE]
> 適用于 Xamarin 的 Visual Studio 解決方案範本會建立包含 XAML 檔案的頁面。 在[第7章](chapter07.md)之前，這本書不涵蓋 XAML。

**Hello** PCL 專案的**References**區段包含下列的 Xamarin 元件：

- **Xamarin. Forms. Core**
- **Xamarin. Forms. Xaml**
- **Xamarin. 表單平臺**

五個應用程式專案的 [**參考**] 區段包含適用于個別平臺的其他元件：

- **Xamarin. Forms. Android**
- **Xamarin. Forms. iOS**
- **Xamarin. Forms. UWP**
- **Xamarin。 WinRT**
- **[Xamarin]。**
- **Xamarin. node.js. Phone**

> [!NOTE]
> 這些專案的 [**參考**] 區段不會再列出元件。 相反地，專案檔包含參考 Xamarin. Forms NuGet 套件的**PackageReference**標記。 Visual Studio 中的 [**參考**] 區段會列出 [ **xamarin.** form] 套件，而不是 [xamarin] 元件。

每個應用程式專案都包含呼叫 `Xamarin.Forms` 命名空間中的靜態 `Forms.Init` 方法。 這會初始化 Xamarin. 表單程式庫。 針對每個平臺定義了不同版本的 `Forms.Init`。 您可以在下列類別中找到這個方法的呼叫：

- iOS： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP： [`App` 類別，`OnLaunched` 方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

此外，每個平臺都必須具現化共用程式庫中的 `App` 類別位置。 這會在呼叫下列類別中的 `LoadApplication` 時發生：

- iOS： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP： [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

否則，這些應用程式專案是正常的「不執行任何動作」程式。

## <a name="pcl-or-sap"></a>PCL 或 SAP？

您可以在可移植的類別庫（PCL）或共用的資產專案（SAP）中，使用通用程式碼來建立 Xamarin 表單方案。 若要建立 SAP 解決方案，請在 Visual Studio 中選取 [共用] 選項。 [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解決方案會示範不進行修改的 SAP 範本。

> [!NOTE]
> 可移植的類別庫已由 .NET Standard 程式庫取代。 本書中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。 否則，PCL 和 .NET Standard 程式庫在概念上非常類似。

程式庫方法會將平臺應用程式專案所參考之程式庫專案中的所有通用程式碼組合在一起。 使用 SAP 方法時，通用程式碼實際上存在於所有平臺應用程式專案中，並在其中共用。

大部分的 Xamarin。表單開發人員偏好使用程式庫方法。 在本書中，大部分的解決方案都會使用程式庫。 使用 SAP 的系統會在專案名稱中包含**sap**尾碼。

有了 SAP 方法，共用專案中的程式碼就可以使用C#預處理器指示詞（`#if`、#`elif`和 `#endif`）搭配這些預先定義的識別碼，針對各種平臺執行不同的程式碼：

- iOS： `__IOS__`
- Android： `__ANDROID__`
- UWP： `WINDOWS_UWP`

在共用程式庫中，您可以在執行時間判斷您正在執行的平臺，如本章稍後所見。

## <a name="labels-for-text"></a>文字標籤

[**問候語**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解決方案會示範如何將新C#檔案加入至**問候語**專案。 這個檔案會定義一個衍生自 `ContentPage`的類別，名為 `GreetingsPage`。 在本書中，大部分專案都包含一個 `ContentPage` 的衍生，其名稱為尾碼 `Page` 附加的專案名稱。

`GreetingsPage` 的函式會具現化[`Label`](xref:Xamarin.Forms.Label)視圖，這是顯示文字的 [Xamarin] 表單檢視。 [ [`Text`](xref:Xamarin.Forms.Label.Text) ] 屬性會設定為 `Label`所顯示的文字。 此程式會將 `Label` 設定為 `ContentPage`的 `Content` 屬性。 `App` 類別的函式會接著具現化 `GreetingsPage`，並將其設定為其 `MainPage` 屬性。

文字會顯示在頁面的左上角。 在 iOS 上，這表示它會與頁面的狀態列重迭。 此問題有幾個解決方案：

### <a name="solution-1-include-padding-on-the-page"></a>解決方案1。 在頁面上包含填補

在頁面上設定[`Padding`](xref:Xamarin.Forms.Page.Padding)屬性。 `Padding` 屬於[`Thickness`](xref:Xamarin.Forms.Thickness)類型，這是具有四個屬性的結構：

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` 會定義頁面內排除內容的區域。 這可讓 `Label` 避免覆寫 iOS 狀態列。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解決方案2。 包含僅適用于 iOS 的填補（僅限 SAP）

僅在 iOS 上使用具有C#預處理器指示詞的 SAP 來設定 ' 填補 ' 屬性。 這會在[**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)解決方案中示範。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解決方案3。 僅針對 iOS （PCL 或 SAP）包含填補

在本書所使用的 Xamarin 版本中，可以使用[`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))或[`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*)的靜態方法，來選取 PCL 或 SAP 中 iOS 專屬的 `Padding` 屬性。 這些方法現在已被取代

`Device.OnPlatform` 方法是用來執行平臺特定的程式碼，或選取平臺特定的值。 就內部而言，它們會使用[`Device.OS`](xref:Xamarin.Forms.Device.OS)靜態唯讀屬性，它會傳回[`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform)列舉的成員：

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- 適用于 UWP 裝置的[`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 。

`Device.OnPlatform` 方法、`Device.OS` 屬性和 `TargetPlatform` 列舉現在已被取代。 請改用[`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform)屬性，並將 `string` 傳回值與下列靜態欄位進行比較：

- [`iOS`](xref:Xamarin.Forms.Device.iOS)，字串 "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android)，字串 "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP)，字串 "UWP"，參考通用 Windows 平臺

[`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom)靜態唯讀屬性相關。 這會傳回[`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)的成員，其中具有下列成員：

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- 未使用[`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)

針對 iOS 和 Android，`Tablet` 和 `Phone` 之間的截止是600單位的直向寬度。 對於 Windows 平臺，`Desktop` 表示在 Windows 10 底下執行的 UWP 應用程式，而 `Phone` 表示在 Windows 10 應用程式下執行的 UWP 應用程式。

## <a name="solution-3a-set-margin-on-the-label"></a>解決方案3a。 設定標籤的邊界

[`Margin`](xref:Xamarin.Forms.View.Margin)屬性的引進次數太晚，而無法包含在書籍中，但它也是 `Thickness` 類型，而且您可以在 `Label` 上設定它，以定義該視圖以外的區域，並包含在其版面配置的計算中。

`Padding` 屬性僅適用于[`Layout`](xref:Xamarin.Forms.Layout)和[`Page`](xref:Xamarin.Forms.Page)衍生。 `Margin` 屬性適用于所有[`View`](xref:Xamarin.Forms.View)衍生。

## <a name="solution-4-center-the-label-within-the-page"></a>解決方案4。 將標籤置中在頁面中

您可以藉由將 `Label` 的[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)屬性設定為[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)類型的值，將 `Page` 中的 `Label` 置中（或將它放在八個其他位置的其中一個）。 `LayoutOptions` 結構會定義兩個屬性：

- 類型為[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)的[`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)屬性，具有四個成員的列舉： [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)，這表示根據方向， [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)， [`End`](xref:Xamarin.Forms.LayoutAlignment.End)，這表示靠右或向下（視方向而定），而[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)。

- `bool`類型的[`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)屬性。

通常不會直接使用這些屬性。 相反地，這兩個屬性的組合是由 `LayoutOptions`類型的八個靜態唯讀屬性所提供：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` 和 `VerticalOptions` 是 Xamarin. 表單版面配置中最重要的屬性，第4章會詳細討論[ **。滾動堆疊**](chapter04.md)。

以下是 `Label` 的 `HorizontalOptions` 和 `VerticalOptions` 屬性都設定為 `LayoutOptions.Center`的結果：

[![問候語程式的三向螢幕擷取畫面](images/ch02fg05-small.png "水準和垂直中央標籤")](images/ch02fg05-large.png#lightbox "水準和垂直中央標籤")

## <a name="solution-5-center-the-text-within-the-label"></a>解決方案5。 將標籤內的文字置中

您也可以將文字置中（或將它放在頁面上的其他八個位置），方法是將 `Label` 的[`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment)和[`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)屬性設定為[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)列舉的成員：

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start)，表示靠左或靠上（視方向而定）
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End)，表示靠右或靠下（視方向而定）

這兩個屬性只會由 `Label`定義，而 `HorizontalAlignment` 和 `VerticalAlignment` 屬性是由 `View` 所定義，並由所有 `View` 衍生的繼承。 視覺效果的結果看起來似乎很類似，但與下一章所示範的不同。

## <a name="related-links"></a>相關連結

- [第2章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第2章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第 2 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [使用 Xamarin 的消費者入門](~/get-started/index.yml)
