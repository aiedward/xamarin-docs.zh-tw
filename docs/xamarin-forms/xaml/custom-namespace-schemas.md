---
title: 在 Xamarin.Forms 中 XAML 自訂命名空間的結構描述
description: 可以定義 XAML 自訂命名空間的結構描述與 XmlnsDefinitionAttribute 類別，其指定自訂 URL 與一或多個 CLR 命名空間之間的對應。 自訂命名空間的結構描述可用的 XAML 命名空間宣告。
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 2e09e89fe17956efaef910638e827b69a5795bc0
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557213"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 自訂命名空間的結構描述

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

可以與指定的 Common Language Runtime (CLR) 命名空間名稱和組件名稱的命名空間宣告中宣告 XAML 命名空間程式庫、 在 XAML 中參考文件庫中的類型：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

不過，指定 CLR 命名空間和組件名稱，在`xmlns`定義可能很冗長，因此又容易出錯。 此外，多個 XAML 命名空間宣告可能需要如果程式庫包含多個命名空間中的型別。

替代方法是定義自訂命名空間的結構描述，例如`http://mycompany.com/schemas/controls`，對應至一或多個 CLR 命名空間。 這可讓單一的 XAML 命名空間宣告來參考組件中的所有類型，即使它們位於不同的命名空間。 它也可讓單一 XAML 命名空間宣告，以在多個組件的參考型別。

如需有關 XAML 命名空間的詳細資訊，請參閱 < [Xamarin.Forms 中的 XAML 命名空間](namespaces.md)。

## <a name="defining-a-custom-namespace-schema"></a>定義自訂命名空間的結構描述

範例應用程式包含會公開一些簡單的控制項，例如程式庫`CircleButton`:

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

文件庫中的所有控制項都位於`MyCompany.Controls`命名空間。 這些控制項可以公開給呼叫的組件，透過自訂命名空間的結構描述中。

自訂命名空間的結構描述定義`XmlnsDefinitionAttribute`類別，指定的 XAML 命名空間與一或多個 CLR 命名空間之間的對應。 `XmlnsDefinitionAttribute`會採用兩個引數： XAML 命名空間名稱，而 CLR 命名空間名稱。 XAML 命名空間名稱會儲存在`XmlnsDefinitionAttribute.XmlNamespace`屬性和 CLR 命名空間名稱會儲存在`XmlnsDefinitionAttribute.ClrNamespace`屬性。

> [!NOTE]
> `XmlnsDefinitionAttribute`類別也有一個名為屬性`AssemblyName`，其中可以選擇性地設定為組件的名稱。 這只是從參考的 CLR 命名空間時才需要`XmlnsDefinitionAttribute`外部組件中。

`XmlnsDefinitionAttribute`應該在組件層級專案，其中包含將自訂命名空間的結構描述中對應的 CLR 命名空間中定義。 下列範例所示**AssemblyInfo.cs**之範例應用程式檔案：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

此程式碼會建立對應的自訂命名空間的結構描述`http://mycompany.com/schemas/controls`URL `MyCompany.Controls` CLR 命名空間。 颾魤 ㄛ`Preserve`組件，以確保連結器會保留組件中的所有類型上指定屬性。

> [!IMPORTANT]
> `Preserve`屬性應該套用至組件的自訂命名空間的結構描述中，透過對應或套用至整個組件中的類別。

自訂命名空間的結構描述可用的 XAML 檔案中的型別解析。

## <a name="consuming-a-custom-namespace-schema"></a>使用自訂命名空間的結構描述

若要使用的自訂命名空間的結構描述的型別，XAML 編譯器需要取用類型的組件的程式碼參考時，組件所定義的類型。 這可藉由新增一個類別，其中包含`Init`方法，以定義將會使用透過 XAML 類型的組件：

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

`Init`然後從該組件會取用來自自訂命名空間的結構描述的型別呼叫方法：

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
> 未包含這類程式碼參考會導致 XAML 編譯器在找不到包含自訂命名空間的結構描述類型的組件。

若要取用`CircleButton`控制項，XAML 命名空間宣告，與指定的自訂命名空間的結構描述 URL 命名空間宣告：

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

`CircleButton` 執行個體可以再加入[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)藉由宣告以`controls`命名空間前置詞。

若要尋找的自訂命名空間的結構描述型別，Xamarin.Forms 會搜尋參考組件的`XmlnsDefinitionAttribute`執行個體。 如果`xmlns`屬性的 XAML 檔案中的項目符合`XmlNamespace`中的屬性值`XmlnsDefinitionAttribute`，會嘗試使用 Xamarin.Forms`XmlnsDefinitionAttribute.ClrNamespace`來解析類型的屬性值。 如果型別解析失敗，Xamarin.Forms 會繼續嘗試根據任何額外的比對的型別解析`XmlnsDefinitionAttribute`執行個體。

結果是兩個`CircleButton`會顯示執行個體：

![圓形按鈕](custom-namespace-schemas-images/circle-buttons.png "圓形按鈕")

## <a name="related-links"></a>相關連結

- [自訂命名空間的結構描述 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [建議的前置詞的 XAML 命名空間](custom-prefix.md)
- [在 Xamarin.Forms 中的 XAML 命名空間](namespaces.md)
