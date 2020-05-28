---
title: 在執行時間載入 XAMLXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d750aa84a48ad4c8015a619d819134cefc63c3d9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139343"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>在執行時間載入 XAMLXamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

[`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)命名空間包含兩種 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 擴充方法，可在執行時間用來載入和剖析 XAML。

## <a name="background"></a>背景

當 Xamarin.Forms 構造 XAML 類別時， [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 會間接呼叫方法。 之所以會發生這種情況，是因為 XAML 類別的程式碼後置檔案會從它的「函式」呼叫 `InitializeComponent` 方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

當 Visual Studio 建立包含 XAML 檔案的專案時，它會剖析 XAML 檔案以產生 c # 程式碼檔案（例如， **MainPage.xaml.g.cs**），其中包含方法的定義 `InitializeComponent` ：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法會呼叫 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法，從 .NET Standard 程式庫中解壓縮 XAML 檔案（或其編譯的二進位檔）。 解壓縮之後，它會初始化 XAML 檔案中定義的所有物件，並將它們全部連接在父子式關聯性中，將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件，並將物件的結果樹狀結構設定為頁面的內容。

## <a name="loading-xaml-at-runtime"></a>在執行時間載入 XAML

這些 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法是 `public` ，因此可以從 Xamarin.Forms 應用程式呼叫以載入，並在執行時間剖析 XAML。 這會允許應用程式從 web 服務下載 XAML、從 XAML 建立必要的視圖，並在應用程式中顯示這類案例。

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

在此範例中， [`Button`](xref:Xamarin.Forms.Button) 會建立實例，並 [`Text`](xref:Xamarin.Forms.Button.Text) 從中定義的 XAML 設定其屬性值 `string` 。 `Button`接著會加入至已 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 在該頁面的 XAML 中定義的。

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)擴充方法可讓您指定泛型型別引數。 不過，很少需要指定型別引數，因為它會從其操作所在的實例型別推斷而來。

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可以用來擴充任何 XAML，下列範例會因而誇大 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 並流覽至它：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>存取元素

使用方法在執行時間載入 XAML 時， [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 不允許對具有指定執行時間物件名稱（使用）之 XAML 專案的強型別存取 `x:Name` 。 不過，您可以使用方法來抓取這些 XAML 元素 [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) ，然後視需要存取：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此範例中，的 XAML [`ContentPage`](xref:Xamarin.Forms.ContentPage) 已放大。 此 XAML 包含 [`Label`](xref:Xamarin.Forms.Label) 名為的 `monkeyName` ，它會使用方法來抓取， [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) 然後才 [`Text`](xref:Xamarin.Forms.Label.Text) 設定其屬性。

## <a name="related-links"></a>相關連結

- [LoadRuntimeXAML （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
