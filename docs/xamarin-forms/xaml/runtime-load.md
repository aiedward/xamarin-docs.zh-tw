---
title: 在執行時間載入 XAML Xamarin.Forms
description: 您可以使用 LoadFromXaml 擴充方法，在執行時間載入和剖析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c027ef35462e6d2d43acf4ea5241a38abe15d41f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374273"
---
# <a name="loading-xaml-at-runtime-in-no-locxamarinforms"></a>在執行時間載入 XAML Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

[`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)命名空間包含兩種 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 可在執行時間用來載入和剖析 XAML 的擴充方法。

## <a name="background"></a>背景

當 Xamarin.Forms XAML 類別已建立時， [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 會間接呼叫該方法。 發生這種情況是因為 XAML 類別的程式碼後端檔案 `InitializeComponent` 從其函式呼叫方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

當 Visual Studio 建立包含 XAML 檔案的專案時，它會剖析 XAML 檔案以產生 c # 程式碼檔案 (例如，包含方法定義的 **MainPage.xaml.g.cs** ) `InitializeComponent` ：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法會呼叫 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法，從 .NET Standard 程式庫將 XAML 檔案 (或其編譯的二進位) 解壓縮。 在解壓縮之後，它會初始化 XAML 檔案中定義的所有物件，並將它們全部連接在父子式關聯性中，將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件，並將物件的結果樹狀結構設定為頁面的內容。

## <a name="loading-xaml-at-runtime"></a>在執行時間載入 XAML

這些 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法是 `public` ，因此可以從 Xamarin.Forms 應用程式呼叫，以便在執行時間載入和剖析 XAML。 這可允許應用程式等案例從 web 服務下載 XAML、從 XAML 建立必要的視圖，以及將它顯示在應用程式中。

> [!WARNING]
> 在執行時間載入 XAML 具有顯著的效能成本，通常應該避免。

下列程式碼範例顯示簡單的使用方式：

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此範例中， [`Button`](xref:Xamarin.Forms.Button) 會建立實例，並 [`Text`](xref:Xamarin.Forms.Button.Text) 從中定義的 XAML 設定其屬性值 `string` 。 `Button`接著會將加入至已 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 在該頁面的 XAML 中定義的。

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)擴充方法允許指定泛型型別引數。 不過，很少需要指定類型引數，因為它將會從其操作所在之實例的型別推斷。

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可以用來擴充任何 XAML，並使用下列範例因而誇大 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，然後流覽至它：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>存取元素

使用方法在執行時間載入 XAML 時， [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 不允許強型別存取具有指定的執行時間物件名稱 (使用) 的 xaml 專案 `x:Name` 。 不過，您可以使用方法來抓取這些 XAML 專案 [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) ，然後視需要加以存取：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此範例中，的 XAML [`ContentPage`](xref:Xamarin.Forms.ContentPage) 會放大。 此 XAML 包含 [`Label`](xref:Xamarin.Forms.Label) 名為的 `monkeyName` ，它會在 [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) 設定屬性之前使用方法抓取 [`Text`](xref:Xamarin.Forms.Label.Text) 。

## <a name="related-links"></a>相關連結

- [LoadRuntimeXAML (範例) ](/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)