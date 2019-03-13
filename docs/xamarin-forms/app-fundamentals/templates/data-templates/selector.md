---
title: 建立 Xamarin.Forms DataTemplateSelector
description: 本文示範如何建立和使用 DataTemplateSelector，您可以在執行階段使用它根據資料繫結屬性值來選擇 DataTemplate。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 75ea30dd510021d7755814e19728bfe60f765645
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057281"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>建立 Xamarin.Forms DataTemplateSelector

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)

_您可以在執行階段使用 DataTemplateSelector 根據資料繫結屬性值來選擇 DataTemplate。這可將多個 DataTemplate 套用至相同類型的物件，以自訂特定物件的外觀。本文示範如何建立和使用 DataTemplateSelector。_

資料範本選取器允許進行將 [`ListView`](xref:Xamarin.Forms.ListView) 繫結至物件集合之類的情節；在此情節中，您可以在執行階段透過傳回特定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的資料範本選取器，選擇 `ListView` 中每個物件的外觀。

## <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

您可以透過建立繼承自 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 的類別，來實作資料範本選取器。 然後覆寫 `OnSelectTemplate` 方法以傳回特定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，如下列程式碼範例所示：

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

`OnSelectTemplate` 方法會根據 `DateOfBirth` 屬性值傳回適當的範本。 所要傳回範本是 `ValidTemplate` 屬性或 `InvalidTemplate` 屬性的值，這會在使用 `PersonDataTemplateSelector` 時設定。

接著可將資料範本選取器類別執行個體指派給 Xamarin.Forms 控制項屬性，例如 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)。 如需有效屬性清單，請參閱[建立 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 執行個體具有下列限制：

- 如果多次查詢相同的資料，針對該資料，`DataTemplateSelector` 子類別必須一律傳回相同的範本。
- `DataTemplateSelector` 子類別不得傳回另一個 `DataTemplateSelector` 子類別。
- `DataTemplateSelector` 子類別不得在每次呼叫時傳回新的 `DataTemplate` 執行個體。 相反地，必須傳回相同的執行個體。 若未這樣做，則會造成記憶體流失並停用虛擬化。
- 在 Android 上，每個 `ListView` 不可以有超過 20 種不同的資料範本。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>在 XAML 中使用 DataTemplateSelector

在 XAML 中，您可以將 `PersonDataTemplateSelector` 宣告為資源來予以具現化，如下列程式碼範例所示：

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

此頁面層級 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 定義兩個 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 執行個體和一個 `PersonDataTemplateSelector` 執行個體。 `PersonDataTemplateSelector` 執行個體會使用 `StaticResource` 標記延伸，將其 `ValidTemplate` 和 `InvalidTemplate` 屬性設定為適當的 `DataTemplate` 執行個體。 請注意，雖然這些資源是在頁面的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定義，但也可以在控制項層級或應用程式層級定義。

您可以將 `PersonDataTemplateSelector` 執行個體指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性來使用它，如下列程式碼範例所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在執行階段，[`ListView`](xref:Xamarin.Forms.ListView) 會針對基礎集合中的每個項目呼叫 `PersonDataTemplateSelector.OnSelectTemplate` 方法，每個呼叫會將資料物件傳遞作為 `item` 參數。 由方法所傳回的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 接著會套用到該物件。

下列螢幕擷取畫面顯示將 `PersonDataTemplateSelector` 套用至基礎集合中每個物件的 [`ListView`](xref:Xamarin.Forms.ListView) 結果：

![](selector-images/data-template-selector.png "具有日期範本選取器的 ListView")

任何 `DateOfBirth` 屬性值大於或等於 1980 的 `Person` 物件會顯示為綠色，其餘物件則顯示為紅色。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>在 C&num; 中使用 DataTemplateSelector

在 C# 中，您可以具現化 `PersonDataTemplateSelector` 並將它指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性，如下列程式碼範例所示：

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

`PersonDataTemplateSelector` 執行個體會將其 `ValidTemplate` 和 `InvalidTemplate` 屬性設定為 `SetupDataTemplates` 方法所建立的適當 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 執行個體。 在執行階段，[`ListView`](xref:Xamarin.Forms.ListView) 會針對基礎集合中的每個項目呼叫 `PersonDataTemplateSelector.OnSelectTemplate` 方法，每個呼叫會將資料物件傳遞作為 `item` 參數。 由方法所傳回的 `DataTemplate` 接著會套用到該物件。

## <a name="summary"></a>總結

本文示範了如何建立和使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)。 您可以在執行階段使用 `DataTemplateSelector` 根據資料繫結屬性值來選擇 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 這可將多個 `DataTemplate` 執行個體套用至相同類型的物件，以自訂特定物件的外觀。


## <a name="related-links"></a>相關連結

- [Data Template Selector (Samples)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/) (日期範本選取器 (範例))
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
