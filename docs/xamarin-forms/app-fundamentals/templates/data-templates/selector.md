---
title: 建立 DataTemplateSelector
description: DataTemplateSelector 可以用來選擇 DataTemplate 在執行階段根據資料繫結屬性的值。 這可讓多個 DataTemplates 来套用至相同類型的物件，以自訂特定物件的外觀。 本文將示範如何建立和使用 DataTemplateSelector。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d87b9f3cf47e3b3efa42ad53cfba91bac1d07d4f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

_DataTemplateSelector 可以用來選擇 DataTemplate 在執行階段根據資料繫結屬性的值。這可讓多個 DataTemplates 来套用至相同類型的物件，以自訂特定物件的外觀。本文將示範如何建立和使用 DataTemplateSelector。_

資料範本選擇器可的案例，例如[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)繫結至集合的物件，其中每個物件中的外觀`ListView`可以選擇在執行階段所傳回的資料範本選擇器特定[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。

## <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

資料範本選擇器藉由建立繼承自一個類別[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)。 `OnSelectTemplate`方法然後覆寫，以傳回特定[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)，如下列程式碼範例所示：

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

`OnSelectTemplate`方法會傳回適當的值為基礎的範本`DateOfBirth`屬性。 要傳回的範本是值`ValidTemplate`屬性或`InvalidTemplate`屬性，它會設定使用時`PersonDataTemplateSelector`。

資料範本選擇器類別的執行個體再指派給 Xamarin.Forms 控制項屬性例如[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)。 如需有效內容，請參閱[建立 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) 執行個體具有下列限制：

- `DataTemplateSelector`子類別必須一律傳回相同的範本為相同的資料，如果查詢多次。
- `DataTemplateSelector`子類別不可以傳回另一個`DataTemplateSelector`子類別。
- `DataTemplateSelector`子類別不可以傳回的新執行個體`DataTemplate`每次呼叫。 相反地，您必須傳回相同的執行個體。 這樣會建立有記憶體流失，並將會停用虛擬化。
- 在 Android 上，可以是每個不超過 20 個不同的資料範本`ListView`。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>使用 xaml DataTemplateSelector

在 XAML 中、`PersonDataTemplateSelector`可以具現化宣告做為資源，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

此頁面層級[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)會定義兩個[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)執行個體和`PersonDataTemplateSelector`執行個體。 `PersonDataTemplateSelector`執行個體設定其`ValidTemplate`和`InvalidTemplate`屬性設為適當`DataTemplate`所使用的執行個體`StaticResource`標記延伸。 請注意，當資源頁中所定義[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，也無法定義控制層級或應用程式層級。

`PersonDataTemplateSelector`執行個體由將它指派給[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性，如下列程式碼範例所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在執行階段， [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)呼叫`PersonDataTemplateSelector.OnSelectTemplate`方法為基礎的集合，與做為資料物件傳遞給呼叫中的項目每個`item`參數。 [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)所傳回的方法適用於該物件。

下列螢幕擷取畫面顯示的結果[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)套用`PersonDataTemplateSelector`基礎的集合中每個物件：

![](selector-images/data-template-selector.png "ListView 資料範本選擇器")

任何`Person`具有物件`DateOfBirth`屬性值大於或等於 1980年中會顯示綠色，以紅色顯示剩餘的物件。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>使用在 C 中 DataTemplateSelector&num;

在 C# 中，`PersonDataTemplateSelector`可以具現化並指派給[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性，如下列程式碼範例所示：

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

`PersonDataTemplateSelector`執行個體設定其`ValidTemplate`和`InvalidTemplate`屬性設為適當[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)所建立的執行個體`SetupDataTemplates`方法。 在執行階段， [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)呼叫`PersonDataTemplateSelector.OnSelectTemplate`方法為基礎的集合，與做為資料物件傳遞給呼叫中的項目每個`item`參數。 `DataTemplate`所傳回的方法適用於該物件。

## <a name="summary"></a>總結

本文示範如何建立和使用[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)。 A`DataTemplateSelector`可用來選擇[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)在執行階段根據資料繫結屬性的值。 這可讓多個`DataTemplate`来套用至的物件，以自訂特定物件的外觀相同類型的執行個體。


## <a name="related-links"></a>相關連結

- [資料範本選擇器 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)
