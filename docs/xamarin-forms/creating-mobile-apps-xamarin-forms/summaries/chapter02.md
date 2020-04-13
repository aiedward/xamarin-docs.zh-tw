---
title: 第 2 章摘要。 應用程式剖析
description: 使用 Xamarin.表單創建行動應用程式:第 2 章摘要。 應用程式剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291823"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章摘要。 應用程式剖析

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

在 Xamarin.Forms 應用程式中,佔據螢幕上空間的對象稱為*可視元素*,由[`VisualElement`](xref:Xamarin.Forms.VisualElement)類封裝。 可視元素可以分為三個類別,對應於這些類:

- [網頁](xref:Xamarin.Forms.Page)
- [配置](xref:Xamarin.Forms.Layout)
- [檢視](xref:Xamarin.Forms.View)

`Page`衍生物佔據整個螢幕,或幾乎整個螢幕。 通常,頁面的子級是`Layout`組織子視覺元素的派生。 的`Layout`子項可以`Layout`是 其他`View`類或 衍生物(通常稱為*元素*),它們是熟悉的物件,如文本、位圖、滑塊、按鈕、清單框等。

本章演示如何通過關注 來創建[`Label`](xref:Xamarin.Forms.Label)應用程式 ,該`View`派生 項是顯示文本的導數。

## <a name="say-hello"></a>問好

安裝 Xamarin 平臺後,您可以在 Visual Studio 或適用於 Mac 的可視化工作室中創建新的 Xamarin.Forms 解決方案。 [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解決方案使用便攜式類庫進行通用代碼。

> [!NOTE]
> 便攜式類庫已被 .NET 標準庫替換。 書中的所有範例代碼已轉換為使用 .NET 標準庫。

此示例演示了在 Visual Studio 中創建的 Xamarin.Forms 解決方案,無需修改。 該解決方案由四個項目組成:

- [**您好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello),由其他項目共用的便攜式類庫 (PCL)
- [**你好.Droid,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)安卓應用程式專案
- [**Hello.iOS,iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)的應用程式專案
- [**Hello.UWP,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)通用 Windows 平臺(Windows 10 和 Windows 10 移動版)的應用程式專案

> [!NOTE]
> Xamarin.Forms 不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 移動版,但 Xamarin.Forms 應用程式確實在 Windows 10 桌面上運行。

可以使這些應用程式中的任何一個項目啟動項目,然後在設備或模擬器上生成和運行該程式。

在許多 Xamarin.Forms 程式中,您不會修改應用程式專案。 這些通常仍然是微小的存根,只是為了啟動程式。 您的大部分焦點將是所有應用程式共有的庫。

## <a name="inside-the-files"></a>檔案內部

**Hello**程序顯示的可視物件[`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)在 類的構造函數中定義。 `App`衍生 Xamarin.Forms 類別[`Application`](xref:Xamarin.Forms.Application)。

> [!NOTE]
> Xamarin.Forms 的可視化工作室解決方案範本創建一個包含 XAML 檔的頁面。 直到[第7章](chapter07.md),本書才涵蓋XAML。

**Hello** PCL 專案的 **"參考"** 部分包括以下 Xamarin.窗體程式集:

- **Xamarin.Forms.核心**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.平臺**

五個應用程式項目的**參考**部分包括適用於各個平台的其他程式集:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.平板電腦**
- **Xamarin.Forms.Platform.WinRT.電話**

> [!NOTE]
> 這些專案的 **「引用」** 部分不再列出程式集。 相反,專案檔包含引用 Xamarin.Forms NuGet 包的**包參考**標記。 Visual Studio 中的 **「參考」** 部分列出了**Xamarin.Forms**包,而不是 Xamarin.Forms 程式集。

每個應用程式專案都包含對命名空間中靜態`Forms.Init`方法`Xamarin.Forms`的調用。 這將初始化 Xamarin.Forms 庫。 為每個平臺定義了不同的`Forms.Init`版本。 對此方法的調用可以在以下類中找到:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP:[`App`類別`OnLaunched`, 方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

此外,每個平台必須實例化共用庫中的`App`類位置。 這將在以下類`LoadApplication`中的調用中發生:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP:[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

否則,這些應用程式專案是正常的"不執行任何操作"的程式。

## <a name="pcl-or-sap"></a>PCL 還是 SAP?

可以在便攜式類庫 (PCL) 或共享資產專案 (SAP) 中使用通用代碼創建 Xamarin.Forms 解決方案。 要創建 SAP 解決方案,請在可視化工作室中選擇「共用」選項。 [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解決方案展示 SAP 範本,無需修改。

> [!NOTE]
> 便攜式類庫已被 .NET 標準庫替換。 書中的所有範例代碼已轉換為使用 .NET 標準庫。 否則,PCL 和 .NET 標準庫在概念上非常相似。

庫方法捆綁了平臺應用程式專案引用的庫專案中的所有通用代碼。 使用 SAP 方法,所有平台應用程式專案中都有效地存在通用代碼,並在它們之間共用。

大多數 Xamarin.窗體開發人員更喜歡庫方法。 在這本書中,大多數解決方案使用庫。 使用 SAP 的 SAP 在專案名稱中包括一個**Sap**後綴。

使用 SAP 方法,分享項目中的代碼可以使用 C# 預先定義識別碼的 C# 預處理器指令 (、`#if``elif`與`#endif`) 為各種平台執行不同的代碼:

- Ios:`__IOS__`
- Android：`__ANDROID__`
- UWP:`WINDOWS_UWP`

在共用庫中,您可以確定運行時運行的平臺,如本章後面看到的。

## <a name="labels-for-text"></a>文字標籤

[**問候語**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解決方案簡報如何向**問候文**專案添加新的 C# 檔。 此檔定義派生自`GreetingsPage``ContentPage`的名為的類。 在本書中,大多數專案包含一個`ContentPage`派生器,其名稱是附加後`Page`綴 的專案的名稱。

建構`GreetingsPage`函數實例化檢視[`Label`](xref:Xamarin.Forms.Label),即顯示文本的 Xamarin.Forms 視圖。 屬性[`Text`](xref:Xamarin.Forms.Label.Text)設定為 顯示`Label`的文字 。 此程式將`Label`集`Content`到`ContentPage`的屬性。 然後,類的`App`構造函數實例化`GreetingsPage`並將其設置到`MainPage`其 屬性。

文字顯示在頁面的左上角。 在 iOS 上,這意味著它與頁面的狀態欄重疊。 此問題有多種解決方案:

### <a name="solution-1-include-padding-on-the-page"></a>解決方案 1. 在頁面上包含填充

在頁面上[`Padding`](xref:Xamarin.Forms.Page.Padding)設置屬性。 `Padding`類型[`Thickness`](xref:Xamarin.Forms.Thickness),具有四個屬性的結構:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`定義頁面內排除內容的區域。 這允許避免`Label`覆蓋 iOS 狀態列。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解決方案 2。 只包含 iOS 的填充(僅限 SAP)

僅在 iOS 上使用帶有 C# 預處理器指令的 SAP 設置「填充」屬性。 這體現在[**問候薩解決方案**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)中。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解決方案 3. 只包括適用於 iOS(PCL 或 SAP)的填充

在用於本書的 Xamarin.窗體版本中,可以`Padding`[`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))使用[`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*)或靜態方法選擇 PCL 或 SAP 中特定於 iOS 的屬性。 這些方法現已棄用

這些方法`Device.OnPlatform`用於運行特定於平臺的代碼或選擇特定於平臺的值。 在內部,它們使用[`Device.OS`](xref:Xamarin.Forms.Device.OS)靜態唯讀屬性,該屬性返回枚舉的成員[`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform):

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)用於UWP設備。

`Device.OnPlatform`方法、`Device.OS`屬性和`TargetPlatform`枚 舉現在都已棄用。 相反,請使用[`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform)屬性`string`並將傳回值與以下靜態欄位進行比較:

- [`iOS`](xref:Xamarin.Forms.Device.iOS),字串"iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android),字串"安卓"
- [`UWP`](xref:Xamarin.Forms.Device.UWP),字串"UWP",指通用 Windows 平臺

靜態[`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom)唯讀屬性與此相關。 這將返回 具有以下[`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)成員 的的成員。

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)未使用

對於 iOS 和`Tablet``Phone`Android,和之間的截止寬度為 600 個單位。 對於 Windows`Desktop`平臺 ,指示在 Windows`Phone`10 下運行的 UWP 應用程式,並指示在 Windows 10 應用程式下運行的 UWP 應用程式。

## <a name="solution-3a-set-margin-on-the-label"></a>解決方案 3a. 在分頁上設定邊界

引入[`Margin`](xref:Xamarin.Forms.View.Margin)該屬性太晚,無法包含在書中,但它也是`Thickness`類型 ,您可以在 上`Label`設置該屬性,以定義檢視外部包含在檢視佈局中的區域。

該`Padding`屬性僅適用於[`Layout`](xref:Xamarin.Forms.Layout)[`Page`](xref:Xamarin.Forms.Page)和衍生工具。 該`Margin`屬性適用於[`View`](xref:Xamarin.Forms.View)所有衍生工具。

## <a name="solution-4-center-the-label-within-the-page"></a>解決方案 4. 將標籤居中置於頁面中

使用`Label`與[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)屬性`Label`設定為[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)類型的值,`Page`[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)可以將居中(或將其放在其他八個位置之一) 中。 結構`LayoutOptions`定義兩個屬性:

- 類型的[`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)屬性 ,具有四個成員的[`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)枚舉 :,表示左或上,[`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)[`End`](xref:Xamarin.Forms.LayoutAlignment.End)具體取決於方向 ,表示 右或下,[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)具體取決於方向,和 。

- 類型的[`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)`bool`屬性 。

通常,這些屬性不會直接使用。 相反,這兩個屬性的組合由以下 8 個`LayoutOptions`靜態 唯讀屬性提供:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`是`VerticalOptions`Xamarin.表單佈局中最重要的屬性,第 4 章將更詳細地討論[**。捲動堆疊**](chapter04.md)。

以下結果,`HorizontalOptions`兩者的`VerticalOptions``Label`屬性 都`LayoutOptions.Center`設定為 :

[![問候程式的三重螢幕截圖](images/ch02fg05-small.png "水平與垂直的中分頁")](images/ch02fg05-large.png#lightbox "水平與垂直的中分頁")

## <a name="solution-5-center-the-text-within-the-label"></a>解決方案 5. 將文字置中分頁

還可以[`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment)透過與[`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)`Label`屬性設定為[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)枚舉的成員,將文字居中(或將其放置在頁面上的八個其他位置):

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start),表示左側或頂部(取決於方向)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End),表示右或下(取決於方向)

這兩個屬性僅`Label`由`HorizontalAlignment`定義`VerticalAlignment`, 而`View`和`View`屬性由所有導數定義並繼承。 視覺結果可能看起來相似,但如下一章所示,它們非常不同。

## <a name="related-links"></a>相關連結

- [第二章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第二章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第二章 F# 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [開始使用 Xamarin.Forms](~/get-started/index.yml)
