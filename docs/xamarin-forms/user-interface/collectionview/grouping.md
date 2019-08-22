---
title: Xamarin. Forms CollectionView 群組
description: CollectionView 可以將其 IsGrouped 屬性設為 true, 以顯示正確群組的資料。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8fd37999428c2813bbf96de3bcbd6ebd1fe0879d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894025"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin. Forms CollectionView 群組

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

大型資料集通常會在持續滾動清單中出現時變得難以使用。 在此案例中, 將資料組織成群組可以藉由更輕鬆地流覽資料來改善使用者體驗。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支援顯示群組資料, 並定義下列屬性來控制其呈現方式:

- `IsGrouped`, 屬於類型`bool`, 表示基礎資料是否應該顯示在群組中。 此屬性的預設值為 `false`。
- `GroupHeaderTemplate`, 屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 用於每個群組之標頭的範本。
- `GroupFooterTemplate`, 屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 用於每個群組頁尾的範本。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

> [!IMPORTANT]
> 目前只有 iOS [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援使用的群組資料。

## <a name="group-data"></a>群組資料

資料必須先分組, 才能顯示。 這可以藉由建立群組清單來完成, 其中每個群組都是專案清單。 群組清單應為`IEnumerable<T>`集合, 其中`T`會定義兩個數據片段:

- 組名。
- 定義屬於群組之專案的集合。`IEnumerable`

因此, 將資料分組的程式是:

- 建立模型單一專案的類型。
- 建立一種類型, 以模型專案的單一群組。
- 建立集合, 其中`T`是用來建立單一專案群組模型的類型。 `IEnumerable<T>` 因此, 此集合是群組的集合, 其中儲存了分組的資料。
- 將資料新增至`IEnumerable<T>`集合。

### <a name="example"></a>範例

群組資料時, 第一個步驟是建立模型單一專案的型別。 下列範例顯示範例應用`Animal`程式中的類別:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

`Animal`類別會建立單一專案的模型。 接著, 可以建立模型群組專案的類型。 下列範例顯示範例應用`AnimalGroup`程式中的類別:

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

類別繼承自類別, 並加入`Name`代表組名的屬性。 `List<T>` `AnimalGroup`

接著可以建立群組的集合:`IEnumerable<T>`

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此程式碼`AnimalGroup`會定義名`Animals`為的集合, 其中集合中的每個專案都是物件。 每`AnimalGroup`個物件都包含一個名稱, `List<Animal>`以及一個定義`Animal`群組中物件的集合。

然後, 群組的`Animals`資料可以新增至集合:

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

此程式碼會在`Animals`集合中建立兩個群組。 第一個`AnimalGroup`名`Bears`為, 並包含一`List<Animal>`組使用的資料。 第二`AnimalGroup`個名`Monkeys`為, 其中包含`List<Animal>`一組猴子詳細資料。

## <a name="display-grouped-data"></a>顯示群組資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會顯示群組資料, 前提是資料已正確地分組, 方法是將`IsGrouped`屬性設定為: `true`

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

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

將[`CollectionView`](xref:Xamarin.Forms.CollectionView) [屬性`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 即可定義中每個專案的外觀。 如需詳細資訊, 請參閱[定義專案外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 根據預設, [`CollectionView`](xref:Xamarin.Forms.CollectionView)將會在群組首和頁尾中顯示組名。 您可以藉由自訂群組首和群組尾來變更此行為。

## <a name="customize-the-group-header"></a>自訂群組標頭

將`CollectionView.GroupHeaderTemplate`屬性設定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為, 即可自訂每個群組標頭的外觀:

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

在此範例中, 每個群組標頭都會[`Label`](xref:Xamarin.Forms.Label)設定為, 其中會顯示組名, 以及已設定其他外觀屬性的。

## <a name="customize-the-group-footer"></a>自訂群組尾

將`CollectionView.GroupFooterTemplate`屬性設定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為, 即可自訂每個群組尾的外觀:

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

在此範例中, 每個群組尾都會設定[`Label`](xref:Xamarin.Forms.Label)為, 以顯示群組中的專案數。

## <a name="empty-groups"></a>空白群組

[`CollectionView`](xref:Xamarin.Forms.CollectionView)當顯示群組資料時, 它會顯示任何空白的群組。 這類群組會以群組頁首和頁尾顯示, 表示群組是空的。

> [!NOTE]
> 在 iOS 10 和更低版本中, 空白群組的群組標頭和頁尾可能會顯示在頂端`CollectionView`。

## <a name="group-without-templates"></a>沒有範本的群組

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以顯示正確群組的[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)資料, 而不將屬性設為:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在此案例中, 您可以覆寫模型單一專案`ToString`的型別中的方法, 以及模型單一專案群組的型別, 藉以顯示有意義的資料。

## <a name="related-links"></a>相關連結

- [CollectionView (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
