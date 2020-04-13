---
title: 建立 Xamarin.Forms DataTemplateSelector
description: 本文示範如何建立和使用 DataTemplateSelector，您可以在執行階段使用它根據資料繫結屬性值來選擇 DataTemplate。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9a17bff9bd0a23d59faf7602544b25c7ec05a86
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771248"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>建立 Xamarin.Forms DataTemplateSelector

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_DataTemplate 選擇器可用於根據資料綁定屬性的值在運行時選擇DataTemplate。這使得多個 DataTemplate 能夠應用於同一類型的物件,從而自定義特定物件的外觀。本文演示如何創建和使用數據範本選擇器。_

數據範本選擇器支援一些方案,例如[`ListView`](xref:Xamarin.Forms.ListView)綁定到物件集合,其中每個物件的外觀`ListView`可以在運行時由返回[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)特定 的數據範本選擇器選擇。

## <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

通過創建從[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)繼承的類實現數據範本選擇器。 然後`OnSelectTemplate`重寫該方法以返回[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)特定 ,如以下代碼示例所示:

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

然後,可以將數據範本選擇器類的實例分配給 Xamarin. Forms 控制件[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性(如 )。 如需有效屬性清單，請參閱[建立 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)實體有以下限制:

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

此頁級別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)定義[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)兩 個實例和`PersonDataTemplateSelector`一個 實例。 `PersonDataTemplateSelector` 執行個體會使用 `StaticResource` 標記延伸，將其 `ValidTemplate` 和 `InvalidTemplate` 屬性設定為適當的 `DataTemplate` 執行個體。 請注意,雖然資源在頁面[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義,但它們也可以在控件級別或應用程式級別定義。

實體以`PersonDataTemplateSelector`將實體分配給[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性來使用,如以下代碼範例所示:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在執行時,[`ListView`](xref:Xamarin.Forms.ListView)調用基礎集合`PersonDataTemplateSelector.OnSelectTemplate`中 每個項的方法,調用傳遞數據`item`物件作為 參數。 然後[`DataTemplate`](xref:Xamarin.Forms.DataTemplate),該方法返回的 。

以下螢幕截圖顯示了[`ListView`](xref:Xamarin.Forms.ListView)將`PersonDataTemplateSelector`應用於 基礎集合中的每個物件的結果:

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

任何 `DateOfBirth` 屬性值大於或等於 1980 的 `Person` 物件會顯示為綠色，其餘物件則顯示為紅色。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>在 C&num; 中使用 DataTemplateSelector

在 C#`PersonDataTemplateSelector`中,可以實體化並分配[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)給 屬性,如以下代碼範例所示:

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

實例`PersonDataTemplateSelector``ValidTemplate`將其`InvalidTemplate`和 屬性[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)`SetupDataTemplates`設置到 方法創建的相應實例。 在執行時,[`ListView`](xref:Xamarin.Forms.ListView)調用基礎集合`PersonDataTemplateSelector.OnSelectTemplate`中 每個項的方法,調用傳遞數據`item`物件作為 參數。 由方法所傳回的 `DataTemplate` 接著會套用到該物件。

## <a name="summary"></a>總結

此專案簡寫此內容建立[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)如何建立與使用 。 `DataTemplateSelector`可用於根據資料繫結屬性的值在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)執行時選擇 。 這可將多個 `DataTemplate` 執行個體套用至相同類型的物件，以自訂特定物件的外觀。

## <a name="related-links"></a>相關連結

- [Data Template Selector (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector) (日期範本選取器 (範例))
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
