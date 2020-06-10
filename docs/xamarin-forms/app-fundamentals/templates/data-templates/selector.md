---
title： "建立 Xamarin.Forms DataTemplateSelector" 描述： "本文示範如何建立和使用 DataTemplateSelector，這可在執行時間根據資料系結屬性的值來選擇 DataTemplate。
assetid： A4629E8F-2BAF-45CE-A76E-DF225FE8D26C ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/08/2016 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="creating-a-xamarinforms-datatemplateselector"></a>建立 Xamarin.Forms DataTemplateSelector

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_DataTemplateSelector 可以用來根據資料系結屬性的值，在執行時間選擇 DataTemplate。這可讓多個 DataTemplates 套用至相同類型的物件，以自訂特定物件的外觀。本文示範如何建立和使用 DataTemplateSelector。_

資料範本選取器可讓您將系結 [`ListView`](xref:Xamarin.Forms.ListView) 至物件集合的案例，其中每個物件的外觀 `ListView` 都可以在執行時間由傳回特定的資料範本選取器來選擇 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。

## <a name="creating-a-datatemplateselector"></a>建立 DataTemplateSelector

藉由建立繼承自的類別，即可實作為資料範本選取器 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。 `OnSelectTemplate`然後會覆寫方法以傳回特定的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，如下列程式碼範例所示：

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

然後，您可以將資料範本選取器類別的實例指派給 Xamarin.Forms 控制項屬性（例如） [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 。 如需有效屬性清單，請參閱[建立 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)實例有下列限制：

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

此頁面層級 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 定義兩個 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 實例和一個 `PersonDataTemplateSelector` 實例。 `PersonDataTemplateSelector` 執行個體會使用 `StaticResource` 標記延伸，將其 `ValidTemplate` 和 `InvalidTemplate` 屬性設定為適當的 `DataTemplate` 執行個體。 請注意，雖然資源是在頁面的中定義 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，但也可以在控制項層級或應用層級定義。

`PersonDataTemplateSelector`實例會藉由將它指派給屬性來使用 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) ，如下列程式碼範例所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在執行時間，會 [`ListView`](xref:Xamarin.Forms.ListView) `PersonDataTemplateSelector.OnSelectTemplate` 針對基礎集合中的每個專案呼叫方法，並以呼叫傳遞資料物件做為 `item` 參數。 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)然後，方法所傳回的會套用至該物件。

下列螢幕擷取畫面顯示將套用 [`ListView`](xref:Xamarin.Forms.ListView) `PersonDataTemplateSelector` 至基礎集合中每個物件的結果：

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

任何 `DateOfBirth` 屬性值大於或等於 1980 的 `Person` 物件會顯示為綠色，其餘物件則顯示為紅色。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>在 C&num; 中使用 DataTemplateSelector

在 c # 中，可以具現 `PersonDataTemplateSelector` 化並指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性，如下列程式碼範例所示：

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

`PersonDataTemplateSelector`實例會將其 `ValidTemplate` 和 `InvalidTemplate` 屬性設定為方法所建立的適當 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 實例 `SetupDataTemplates` 。 在執行時間，會 [`ListView`](xref:Xamarin.Forms.ListView) `PersonDataTemplateSelector.OnSelectTemplate` 針對基礎集合中的每個專案呼叫方法，並以呼叫傳遞資料物件做為 `item` 參數。 由方法所傳回的 `DataTemplate` 接著會套用到該物件。

## <a name="summary"></a>摘要

本文已示範如何建立和使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。 `DataTemplateSelector`可以用來根據資料系結 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 屬性的值，在執行時間選擇。 這可將多個 `DataTemplate` 執行個體套用至相同類型的物件，以自訂特定物件的外觀。

## <a name="related-links"></a>相關連結

- [Data Template Selector (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector) (日期範本選取器 (範例))
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
