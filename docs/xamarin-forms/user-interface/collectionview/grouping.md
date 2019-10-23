---
title: Xamarin. Forms CollectionView 群組
description: CollectionView 可以將其 IsGrouped 屬性設為 true，以顯示正確群組的資料。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 3a0fe7159e6af24d58e49dea4166d012605c9985
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749800"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin. Forms CollectionView 群組

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

大型資料集通常會在持續滾動清單中出現時變得難以使用。 在此案例中，將資料組織成群組可以藉由更輕鬆地流覽資料來改善使用者體驗。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支援顯示群組資料，並定義下列屬性來控制其呈現方式：

- `IsGrouped`，屬於 `bool` 類型，表示基礎資料是否應該顯示在群組中。 此屬性的預設值為 `false`。
- `GroupHeaderTemplate`，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型的範本，用於每個群組的標頭。
- `GroupFooterTemplate`，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型的範本，用於每個群組的頁尾。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

下列螢幕擷取畫面顯示顯示群組資料的[`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

[![在 iOS 和 Android 上 CollectionView 中群組資料的螢幕擷取畫面](grouping-images/grouped-data.png "具有群組資料的 CollectionView")](grouping-images/grouped-data-large.png#lightbox "具有群組資料的 CollectionView")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="group-data"></a>群組資料

資料必須先分組，才能顯示。 這可以藉由建立群組清單來完成，其中每個群組都是專案清單。 群組清單應該是 `IEnumerable<T>` 集合，其中 `T` 會定義兩個數據片段：

- 組名。
- 定義屬於群組之專案的 `IEnumerable` 集合。

因此，將資料分組的程式是：

- 建立模型單一專案的類型。
- 建立一種類型，以模型專案的單一群組。
- 建立 `IEnumerable<T>` 集合，其中 `T` 是用來建立單一專案群組模型的類型。 因此，此集合是群組的集合，其中儲存了分組的資料。
- 將資料新增至 `IEnumerable<T>` 集合。

### <a name="example"></a>範例

群組資料時，第一個步驟是建立模型單一專案的型別。 下列範例顯示範例應用程式中的 `Animal` 類別：

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

@No__t_0 類別會建立單一專案的模型。 接著，可以建立模型群組專案的類型。 下列範例顯示範例應用程式中的 `AnimalGroup` 類別：

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

@No__t_0 類別繼承自 `List<T>` 類別，並加入代表組名的 `Name` 屬性。

然後可以建立 `IEnumerable<T>` 群組集合：

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此程式碼會定義名為 `Animals` 的集合，其中集合中的每個專案都是 `AnimalGroup` 物件。 每個 `AnimalGroup` 物件都包含一個名稱，以及定義群組中 `Animal` 物件的 `List<Animal>` 集合。

然後，群組的資料可以新增至 `Animals` 集合：

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
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

此程式碼會在 `Animals` 集合中建立兩個群組。 第一個 `AnimalGroup` 名為 `Bears`，並包含一個 `List<Animal>` 的熊詳細資料集合。 第二個 `AnimalGroup` 名為 `Monkeys`，並包含 `List<Animal>` 的猴子詳細資料集合。

## <a name="display-grouped-data"></a>顯示群組資料

如果資料已正確分組， [`CollectionView`](xref:Xamarin.Forms.CollectionView)將會顯示群組資料，方法是將 `IsGrouped` 屬性設定為 `true`：

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

[@No__t_1](xref:Xamarin.Forms.CollectionView)中每個專案的外觀都是藉由將[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)來定義。 如需詳細資訊，請參閱[定義專案外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在群組首和頁尾中顯示組名。 您可以藉由自訂群組首和群組尾來變更此行為。

## <a name="customize-the-group-header"></a>自訂群組標頭

將 [`CollectionView.GroupHeaderTemplate`] 屬性設定為 [ [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)]，即可自訂每個群組標頭的外觀：

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

在此範例中，每個群組標頭都會設定為顯示組名的[`Label`](xref:Xamarin.Forms.Label) ，並已設定其他外觀屬性。 下列螢幕擷取畫面顯示自訂的群組標頭：

[![CollectionView （在 iOS 和 Android 上）中自訂群組標頭的螢幕擷取畫面](grouping-images/customized-header.png "具有自訂群組標頭的 CollectionView")](grouping-images/customized-header-large.png#lightbox "具有自訂群組標頭的 CollectionView")

## <a name="customize-the-group-footer"></a>自訂群組尾

將 [`CollectionView.GroupFooterTemplate`] 屬性設定為 [ [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)]，即可自訂每個群組頁尾的外觀：

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

在此範例中，每個群組尾都會設定為顯示群組中專案數的[`Label`](xref:Xamarin.Forms.Label) 。 下列螢幕擷取畫面顯示自訂的群組尾：

[![CollectionView （在 iOS 和 Android 上）中自訂群組尾的螢幕擷取畫面](grouping-images/customized-footer.png "具有自訂群組尾的 CollectionView")](grouping-images/customized-footer-large.png#lightbox "具有自訂群組尾的 CollectionView")

## <a name="empty-groups"></a>空白群組

當[`CollectionView`](xref:Xamarin.Forms.CollectionView)顯示群組的資料時，它會顯示任何空白的群組。 這類群組會以群組頁首和頁尾顯示，表示群組是空的。 下列螢幕擷取畫面顯示空的群組：

[![在 iOS 和 Android 上 CollectionView 中的空白群組螢幕擷取畫面](grouping-images/empty-group.png "具有空白群組的 CollectionView")](grouping-images/empty-group-large.png#lightbox "具有空白群組的 CollectionView")

> [!NOTE]
> 在 iOS 10 和更低版本中，空白群組的群組標頭和頁尾可能會顯示在 `CollectionView` 的頂端。

## <a name="group-without-templates"></a>沒有範本的群組

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以顯示正確群組的資料，而不將[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在此案例中，您可以覆寫模型單一專案的類型中的 `ToString` 方法，以及模型單一專案群組的類型，藉以顯示有意義的資料。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. 表單資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
