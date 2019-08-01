---
title: 在 Xamarin 中的執行時間載入 XAML
description: 您可以在執行時間使用 LoadFromXaml 擴充方法來載入和剖析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: e253d2ba949a94637d7773fdc50b479679fd3f41
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657246"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>在 Xamarin 中的執行時間載入 XAML

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

命名空間包含兩[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)種擴充方法, 可在執行時間用來載入和剖析 XAML。 [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)

## <a name="background"></a>背景

當構造了 Xamarin XAML 類別時, [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)會間接呼叫方法。 之所以會發生這種情況, 是因為 XAML 類別的程式碼`InitializeComponent`後置檔案會從它的「函式」呼叫方法:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

當 Visual Studio 建立包含 XAML 檔案的專案時, 它會剖析 xaml 檔案來產生程式C#代碼檔案 (例如, **MainPage.xaml.g.cs**), 其中包含`InitializeComponent`方法的定義:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法會[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)呼叫方法, 從 .NET Standard 程式庫中解壓縮 XAML 檔案 (或其編譯的二進位檔)。 解壓縮之後, 它會初始化 XAML 檔案中定義的所有物件, 並將它們全部連接在父子式關聯性中, 將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件, 並將物件的結果樹狀結構設定為的內容本頁.

## <a name="loading-xaml-at-runtime"></a>在執行時間載入 XAML

這些[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法都`public`是, 因此可以從 Xamarin 應用程式呼叫來載入, 並在執行時間剖析 XAML。 這會允許應用程式從 web 服務下載 XAML、從 XAML 建立必要的視圖, 並在應用程式中顯示這類案例。

> [!WARNING]
> 在執行時間載入 XAML 具有顯著的效能成本, 通常應該避免。

下列程式碼範例顯示簡單的使用方式:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此範例中, [`Button`](xref:Xamarin.Forms.Button)會建立實例, 並從中[`Text`](xref:Xamarin.Forms.Button.Text) `string`定義的 XAML 設定其屬性值。 接著會加入[`StackLayout`](xref:Xamarin.Forms.StackLayout)至已在該頁面的 XAML 中定義的。 `Button`

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)擴充方法可讓您指定泛型型別引數。 不過, 很少需要指定型別引數, 因為它會從其操作所在的實例型別推斷而來。

方法可以用來擴充任何 XAML, 下列範例會[`ContentPage`](xref:Xamarin.Forms.ContentPage)因而誇大並流覽至它: [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>存取元素

使用[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法在執行時間載入 xaml 時, 不允許對具有指定執行時間物件名稱 (使用`x:Name`) 之 XAML 專案的強型別存取。 不過, 您可以使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法來抓取這些 XAML 元素, 然後視需要存取:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此範例中, 的 XAML [`ContentPage`](xref:Xamarin.Forms.ContentPage)已放大。 此 XAML 包含[`Label`](xref:Xamarin.Forms.Label)名為`monkeyName`的, 它會使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法來抓取, 然後才[`Text`](xref:Xamarin.Forms.Label.Text)設定其屬性。

## <a name="related-links"></a>相關連結

- [LoadRuntimeXAML (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
