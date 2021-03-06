---
title: Xamarin.Forms CollectionView 群組
description: CollectionView 可以將其 IsGrouped 屬性設為 true，以顯示正確群組的資料。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8858be2839de58369a6b5f0cb8a6c09c44bb3861
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366148"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin.Forms CollectionView 群組

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

在持續滾動清單中顯示大型資料集時，經常會變得很困難。 在此案例中，將資料組織成群組可讓您更輕鬆地流覽資料，進而改善使用者體驗。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支援顯示群組資料，並定義下列屬性來控制其呈現方式：

- `IsGrouped`型別為的， `bool` 表示基礎資料是否應顯示在群組中。 此屬性的預設值為 `false`。
- `GroupHeaderTemplate`，屬於類型的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 範本，用於每個群組的標頭。
- `GroupFooterTemplate`，屬於類型的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 範本，用於每個群組的頁尾。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

下列螢幕擷取畫面顯示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 顯示群組資料：

[![螢幕擷取畫面：在 iOS 和 Android 上，CollectionView 中的群組資料](grouping-images/grouped-data.png "具有群組資料的 CollectionView")](grouping-images/grouped-data-large.png#lightbox "具有群組資料的 CollectionView")

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="group-data"></a>群組資料

您必須先將資料分組，才能顯示。 這可以藉由建立群組清單來完成，其中每個群組都是專案清單。 群組清單應該是 `IEnumerable<T>` 集合，其中 `T` 定義了兩個數據片段：

- 組名。
- `IEnumerable`定義屬於群組之專案的集合。

因此，將資料分組的程式是：

- 建立建立單一專案模型的型別。
- 建立可建立單一專案群組模型的型別。
- 建立 `IEnumerable<T>` 集合，其中 `T` 是用來建立單一專案群組模型的型別。 因此，這個集合是用來儲存群組資料的群組集合。
- 將資料加入至 `IEnumerable<T>` 集合。

### <a name="example"></a>範例

將資料分組時，第一個步驟是建立模型單一專案的型別。 下列範例顯示 `Animal` 來自範例應用程式的類別：

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

`Animal`類別會建立單一專案的模型。 接著可以建立可建立專案群組模型的型別。 下列範例顯示 `AnimalGroup` 來自範例應用程式的類別：

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

`AnimalGroup`類別繼承自 `List<T>` 類別，並加入 `Name` 代表組名的屬性。

`IEnumerable<T>`然後可以建立群組集合：

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此程式碼會定義名為的集合 `Animals` ，其中集合中的每個專案都是 `AnimalGroup` 物件。 每個 `AnimalGroup` 物件都包含一個名稱，以及一個 `List<Animal>` 定義 `Animal` 群組中物件的集合。

然後，您可以將群組的資料加入至 `Animals` 集合：

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

此程式碼會在集合中建立兩個群組 `Animals` 。 第一個 `AnimalGroup` 名為 `Bears` ，且包含一 `List<Animal>` 組熊詳細資料。 第二個 `AnimalGroup` 名為 `Monkeys` ，且包含一 `List<Animal>` 組猴子詳細資料。

## <a name="display-grouped-data"></a>顯示群組資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 將會將屬性設定為，以顯示群組資料（假設資料已正確分組） `IsGrouped` `true` ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

中的每個專案的外觀 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 是藉由將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 屬性設定為來定義 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需詳細資訊，請參閱 [定義專案外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 依預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會在群組頁首和頁尾中顯示組名。 您可以藉由自訂群組頁首和群組頁尾來變更此行為。

## <a name="customize-the-group-header"></a>自訂群組標頭

您可以將屬性設定為，以自訂每個群組標頭的外觀 `CollectionView.GroupHeaderTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

在此範例中，每個群組標頭都會設定為 [`Label`](xref:Xamarin.Forms.Label) ，它會顯示組名，而且會設定其他外觀屬性。 下列螢幕擷取畫面顯示自訂群組標頭：

[![螢幕擷取畫面： iOS 和 Android 上 CollectionView 中的自訂群組標頭](grouping-images/customized-header.png "具有自訂群組標頭的 CollectionView")](grouping-images/customized-header-large.png#lightbox "具有自訂群組標頭的 CollectionView")

## <a name="customize-the-group-footer"></a>自訂群組尾

您可以藉由將屬性設定為，來自訂每個群組頁尾的外觀 `CollectionView.GroupFooterTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

在此範例中，每個群組頁尾都會設定為 [`Label`](xref:Xamarin.Forms.Label) ，以顯示群組中的專案數。 下列螢幕擷取畫面顯示自訂的群組尾：

[![螢幕擷取畫面： iOS 和 Android 上 CollectionView 中的自訂群組頁尾](grouping-images/customized-footer.png "具有自訂群組尾的 CollectionView")](grouping-images/customized-footer-large.png#lightbox "具有自訂群組尾的 CollectionView")

## <a name="empty-groups"></a>空白群組

當 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 顯示群組資料時，它會顯示任何空白的群組。 這類群組將會以群組頁首和頁尾顯示，表示該群組是空的。 下列螢幕擷取畫面顯示空白群組：

[![螢幕擷取畫面：在 iOS 和 Android 上，CollectionView 中的空白群組](grouping-images/empty-group.png "具有空白群組的 CollectionView")](grouping-images/empty-group-large.png#lightbox "具有空白群組的 CollectionView")

> [!NOTE]
> 在 iOS 10 和更低版本上，空群組的群組頁首和頁尾都可能會顯示在頂端 `CollectionView` 。

## <a name="group-without-templates"></a>沒有範本的群組

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以正確地顯示群組資料，而不需要將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 屬性設為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在此案例中，您可以覆寫 `ToString` 模型單一專案的型別中的方法，以及建立單一專案群組模型的型別，藉以顯示有意義的資料。

## <a name="related-links"></a>相關連結

- [CollectionView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)