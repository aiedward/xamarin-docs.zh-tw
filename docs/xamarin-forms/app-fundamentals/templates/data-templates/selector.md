---
title: 建立 Xamarin.Forms DataTemplateSelector
description: 這篇文章會示範如何建立和取用 DataTemplateSelector，可用來選擇 DataTemplate，以在執行階段根據資料繫結屬性的值。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994513"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>建立 Xamarin.Forms DataTemplateSelector

_DataTemplateSelector 可用來選擇 DataTemplate，以在執行階段根據資料繫結屬性的值。這可讓多個的 Datatemplate，要套用至相同類型的物件，以自訂特定物件的外觀。這篇文章會示範如何建立和取用 DataTemplateSelector。_

資料範本選取器會啟用這類案例[ `ListView` ](xref:Xamarin.Forms.ListView)繫結至物件的集合，其中在每個物件的外觀`ListView`可以選擇在執行階段，傳回的資料範本選取器特定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

資料範本選取器藉由建立繼承自類別[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)。 `OnSelectTemplate`方法再覆寫，以傳回特定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，如下列程式碼範例所示：

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

`OnSelectTemplate`方法會傳回適當的值為基礎的範本`DateOfBirth`屬性。 要傳回的範本是值`ValidTemplate`屬性或`InvalidTemplate`屬性，它會設定當使用`PersonDataTemplateSelector`。

資料範本選取器類別的執行個體然後指派給 Xamarin.Forms 控制項屬性這類[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)。 如需有效的屬性，請參閱[建立 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 執行個體具有下列限制：

- `DataTemplateSelector`子類別必須一律傳回相同的範本，為相同的資料，如果查詢多次。
- `DataTemplateSelector`子類別不可以傳回另一個`DataTemplateSelector`子類別。
- `DataTemplateSelector`子類別不可以傳回的新執行個體`DataTemplate`每次呼叫。 相反地，您必須傳回相同的執行個體。 若要這樣做的失敗會建立記憶體流失，而且將會停用虛擬化。
- 在 Android 上，可以是每個不超過 20 個不同的資料範本`ListView`。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>使用 XAML 在 DataTemplateSelector

在 XAML，`PersonDataTemplateSelector`可以具現化宣告為資源，如下列程式碼範例所示：

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

此頁面層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定義兩個[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)執行個體和`PersonDataTemplateSelector`執行個體。 `PersonDataTemplateSelector`執行個體設定其`ValidTemplate`並`InvalidTemplate`屬性，以適當`DataTemplate`使用的執行個體`StaticResource`標記延伸。 請注意，當資源的頁面中定義[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，也可以定義控制層級或應用程式層級。

`PersonDataTemplateSelector`執行個體由將它指派給[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性，如下列程式碼範例所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在執行階段[ `ListView` ](xref:Xamarin.Forms.ListView)呼叫`PersonDataTemplateSelector.OnSelectTemplate`為基礎的集合，與呼叫傳遞做為資料物件中項目的每個方法`item`參數。 [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)時所傳回的方法適用於該物件。

下列螢幕擷取畫面顯示的結果[ `ListView` ](xref:Xamarin.Forms.ListView)套用`PersonDataTemplateSelector`基礎集合中每個物件：

![](selector-images/data-template-selector.png "使用資料範本選取器的 ListView")

任何`Person`具有物件`DateOfBirth`屬性值大於或等於 1980年中會顯示綠色，以紅色顯示剩餘的物件。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>使用以 C DataTemplateSelector&num;

在 C# 中，`PersonDataTemplateSelector`可以具現化並指派給[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性，如下列程式碼範例所示：

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

`PersonDataTemplateSelector`執行個體設定其`ValidTemplate`並`InvalidTemplate`屬性，以適當[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)所建立的執行個體`SetupDataTemplates`方法。 在執行階段[ `ListView` ](xref:Xamarin.Forms.ListView)呼叫`PersonDataTemplateSelector.OnSelectTemplate`為基礎的集合，與呼叫傳遞做為資料物件中項目的每個方法`item`參數。 `DataTemplate`時所傳回的方法適用於該物件。

## <a name="summary"></a>總結

這篇文章已示範如何建立及取用[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)。 A`DataTemplateSelector`可用來選擇[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在執行階段根據資料繫結屬性的值。 這可讓多個`DataTemplate`来套用至的物件，以自訂特定物件的外觀相同類型的執行個體。


## <a name="related-links"></a>相關連結

- [資料範本選取器 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
