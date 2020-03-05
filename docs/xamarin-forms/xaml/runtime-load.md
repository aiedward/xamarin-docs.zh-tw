---
title: 在 Xamarin 中的執行時間載入 XAML
description: 您可以在執行時間使用 LoadFromXaml 擴充方法來載入和剖析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "78291498"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>在 Xamarin 中的執行時間載入 XAML

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

[`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)命名空間包含兩個[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)擴充方法，可在執行時間用來載入和剖析 XAML。

## <a name="background"></a>背景

當構造了 Xamarin XAML 類別時，會間接呼叫[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法。 之所以會發生這種情況，是因為 XAML 類別的程式碼後置檔案會從其函式呼叫 `InitializeComponent` 方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

當 Visual Studio 建立包含 XAML 檔案的專案時，它會剖析 XAML 檔案，以產生C#包含 `InitializeComponent` 方法定義的程式碼檔案（例如， **MainPage.xaml.g.cs**）：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent` 方法會呼叫[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法，從 .NET Standard 程式庫解壓縮 XAML 檔案（或其編譯的二進位檔）。 解壓縮之後，它會初始化 XAML 檔案中定義的所有物件，並將它們全部連接在父子式關聯性中，將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件，並將物件的結果樹狀結構設定為的內容本頁.

## <a name="loading-xaml-at-runtime"></a>在執行時間載入 XAML

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)的方法會 `public`，因此可以從 Xamarin 應用程式呼叫以載入，並在執行時間剖析 XAML。 這會允許應用程式從 web 服務下載 XAML、從 XAML 建立必要的視圖，並在應用程式中顯示這類案例。

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

在此範例中，會建立[`Button`](xref:Xamarin.Forms.Button)實例，並從 `string`中定義的 XAML 設定其[`Text`](xref:Xamarin.Forms.Button.Text)屬性值。 `Button` 接著會加入至已在該頁面的 XAML 中定義的[`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)的擴充方法可讓您指定泛型型別引數。 不過，很少需要指定型別引數，因為它會從其操作所在的實例型別推斷而來。

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可以用來擴充任何 XAML，下列範例會因而誇大[`ContentPage`](xref:Xamarin.Forms.ContentPage)然後流覽至它：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>存取元素

使用[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法在執行時間載入 xaml 時，不允許具有指定執行時間物件名稱（使用 `x:Name`）之 xaml 元素的強型別存取。 不過，您可以使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法來抓取這些 XAML 元素，然後視需要存取：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此範例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage)的 XAML 已膨脹。 此 XAML 包含名為 `monkeyName`的[`Label`](xref:Xamarin.Forms.Label) ，它會使用[`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法來抓取，然後才設定其[`Text`](xref:Xamarin.Forms.Label.Text)屬性。

## <a name="related-links"></a>相關連結

- [LoadRuntimeXAML （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
