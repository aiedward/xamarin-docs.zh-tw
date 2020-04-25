---
title: Xamarin 中的 XAML 自訂命名空間架構
description: 您可以使用 XmlnsDefinitionAttribute 類別來定義 XAML 自訂命名空間架構，這會指定自訂 URL 與一或多個 CLR 命名空間之間的對應。 接著，您可以在 XAML 命名空間宣告中使用自訂命名空間架構。
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 98da98c6ef7058264fac12f8271b3bc1848fdbe0
ms.sourcegitcommit: 1fb87ff74560d4d7c89f80018cc010c07646461c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139040"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Xamarin 中的 XAML 自訂命名空間架構

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

您可以宣告程式庫的 XAML 命名空間來參考程式庫中的類型，方法是使用指定 Common Language Runtime （CLR）命名空間名稱和元件名稱的命名空間宣告：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

不過，在`xmlns`定義中指定 CLR 命名空間和元件名稱可能會很麻煩，而且容易出錯。 此外，如果程式庫包含多個命名空間中的類型，則可能需要多個 XAML 命名空間宣告。

另一個方法是定義一個對應至一或多個 CLR `http://mycompany.com/schemas/controls`命名空間的自訂命名空間架構，例如。 這可讓單一 XAML 命名空間宣告參考元件中的所有類型，即使它們位於不同的命名空間中也一樣。 它也會啟用單一 XAML 命名空間宣告，以參考多個元件中的類型。

如需 XAML 命名空間的詳細資訊，請參閱[Xamarin 中的 Xaml 命名空間](namespaces.md)。

## <a name="defining-a-custom-namespace-schema"></a>定義自訂命名空間架構

範例應用程式包含一個程式庫，它會公開一些簡單的`CircleButton`控制項，例如：

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

程式庫中的所有控制項都位於`MyCompany.Controls`命名空間中。 這些控制項可以透過自訂命名空間架構公開給呼叫元件。

自訂命名空間架構是使用`XmlnsDefinitionAttribute`類別定義的，它會指定 XAML 命名空間與一或多個 CLR 命名空間之間的對應。 `XmlnsDefinitionAttribute`會採用兩個引數： XAML 命名空間名稱和 CLR 命名空間名稱。 XAML 命名空間名稱會儲存在`XmlnsDefinitionAttribute.XmlNamespace`屬性中，而 CLR 命名空間名稱會儲存在`XmlnsDefinitionAttribute.ClrNamespace`屬性中。

> [!NOTE]
> `XmlnsDefinitionAttribute`類別也有一個名為`AssemblyName`的屬性，它可以選擇性地設定為元件的名稱。 只有當從參考的 CLR 命名空間`XmlnsDefinitionAttribute`位於外部元件時，才需要此項。

`XmlnsDefinitionAttribute`應定義于專案中的元件層級，而此專案中包含將在自訂命名空間架構中對應的 CLR 命名空間。 下列範例顯示範例應用程式中的**AssemblyInfo.cs**檔案：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

此程式碼會建立自訂命名空間架構`http://mycompany.com/schemas/controls` ，以將`MyCompany.Controls` URL 對應至 CLR 命名空間。 此外，也會`Preserve`在元件上指定屬性，以確保連結器保留元件中的所有類型。

> [!IMPORTANT]
> `Preserve`屬性應套用至元件中透過自訂命名空間架構對應的類別，或套用至整個元件。

然後，自訂命名空間架構可用於 XAML 檔案中的類型解析。

## <a name="consuming-a-custom-namespace-schema"></a>使用自訂命名空間架構

若要從自訂命名空間架構取用型別，XAML 編譯器會要求從使用型別之元件的程式碼參考，到定義類型的元件。 這可以藉由將包含`Init`方法的類別加入至定義將透過 XAML 取用之型別的元件來完成：

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

然後`Init` ，您可以從使用自訂命名空間架構之類型的元件中呼叫方法：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> 如果無法包含這類程式碼參考，將會導致 XAML 編譯器找不到包含自訂命名空間架構類型的元件。

若要使用`CircleButton`控制項，會宣告 XAML 命名空間，並使用命名空間宣告來指定自訂命名空間架構 URL：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton`接著，您可以使用[`ContentPage`](xref:Xamarin.Forms.ContentPage) `controls`命名空間前置詞來宣告實例，以將其新增至。

若要尋找自訂命名空間架構類型，Xamarin. Forms 會搜尋參考`XmlnsDefinitionAttribute`的元件以尋找實例。 如果 XAML `xmlns`檔案中專案的屬性符合中的`XmlNamespace`屬性值`XmlnsDefinitionAttribute`，則 Xamarin. form 會嘗試使用`XmlnsDefinitionAttribute.ClrNamespace`屬性值來解析類型。 如果類型解析失敗，則 Xamarin 會繼續根據任何其他相符`XmlnsDefinitionAttribute`的實例來嘗試類型解析。

結果會顯示兩個`CircleButton`實例：

![圓形按鈕](custom-namespace-schemas-images/circle-buttons.png "圓形按鈕")

## <a name="related-links"></a>相關連結

- [自訂命名空間架構（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [XAML 命名空間建議的前置詞](custom-prefix.md)
- [Xamarin 中的 XAML 命名空間](namespaces.md)
