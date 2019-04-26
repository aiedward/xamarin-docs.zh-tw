---
title: 載入 XAML 在 Xamarin.Forms 中的執行階段
description: XAML 可以載入，且在執行階段剖析 LoadFromXaml 的擴充方法。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178042"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>載入 XAML 在 Xamarin.Forms 中的執行階段

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

[ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml)命名空間包含兩個[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)擴充方法，可用來載入，並在執行階段剖析 XAML。

## <a name="background"></a>背景

當建構 Xamarin.Forms XAML 類別時， [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)間接呼叫方法。 這是因為 XAML 的程式碼後置檔案類別會呼叫`InitializeComponent`從其建構函式的方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

當 Visual Studio 建置的專案其內含的 XAML 檔案時，它會剖析 XAML 檔案來產生C#程式碼檔案 (例如， **MainPage.xaml.g.cs**)，其中包含的定義`InitializeComponent`方法：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法呼叫[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法來擷取.NET Standard 程式庫中的 XAML 檔案 （或其已編譯的二進位檔）。 解壓縮後初始化所有 XAML 檔案中定義的物件、 連接它們一起在父子式關聯性，附加在 XAML 檔案中，設定事件的程式碼中定義的事件處理常式以及設定物件的內容結果樹狀結構頁面。

## <a name="loading-xaml-at-runtime"></a>在執行階段載入 XAML

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法為`public`，因此您可以從載入，Xamarin.Forms 應用程式呼叫和剖析 XAML 在執行階段。 這可讓案例，例如從 web 服務，從 XAML 中，建立所需的檢視，並顯示在應用程式下載 XAML 應用程式。

> [!WARNING]
> 在執行階段載入 XAML 顯著的效能成本，而且通常應該避免。

下列程式碼範例顯示簡單的使用方式：

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此範例中， [ `Button` ](xref:Xamarin.Forms.Button)隨即建立執行個體，而其[ `Text` ](xref:Xamarin.Forms.Button.Text)從 XAML 所設定的屬性值定義在`string`。 `Button`接著會新增至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout) XAML 頁面中已定義的。

> [!NOTE]
> [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)延伸方法可以讓指定的泛型型別引數。 不過，很少需要指定類型引數，因為它會從執行個體的類型推斷其上運作。

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可用來擴充任何 XAML 中，使用下列的範例並[ `ContentPage` ](xref:Xamarin.Forms.ContentPage) ，然後巡覽至它：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>存取項目

使用在執行階段載入 XAML [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法中不允許強型別存取 XAML 項目所指定執行階段物件名稱 (使用`x:Name`)。 不過，這些 XAML 項目可以使用來擷取[ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法，然後視需要存取：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此範例中，針對 XAML [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)會擴大。 包含此 XAML [ `Label` ](xref:Xamarin.Forms.Label)名為`monkeyName`，其中會使用擷取[ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法，它有之前[ `Text` ](xref:Xamarin.Forms.Label.Text)設定屬性。

## <a name="related-links"></a>相關連結

- [LoadRuntimeXAML （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
