---
title: Xamarin.表單檢視群組
description: CollectionView 可以通過將其 IsGroups 屬性設置為 true 來正確顯示正確分組的數據。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8360123b01f36bde084b4dc315109e6bdaef2207
ms.sourcegitcommit: 99aa05bd9b5e3f66d134066b860f41b54fa2d850
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103278"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin.表單檢視群組

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

大型數據集在不斷滾動清單中顯示時通常會變得笨拙。 在這種情況下,將數據組織成組可以更輕鬆地瀏覽數據來改善用戶體驗。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支援顯示群組資料,並定義以下屬性來控制資料的顯示方式:

- `IsGrouped`的類型`bool`, 指示基礎資料是否應以組形式顯示。 此屬性的預設值為 `false`。
- `GroupHeaderTemplate`的類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate),用於每個組標頭的範本。
- `GroupFooterTemplate`的類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate),用於每個組腳腳的範本。

這些屬性由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件支援,這意味著這些屬性可以是數據綁定的目標。

以下螢幕截圖顯示[`CollectionView`](xref:Xamarin.Forms.CollectionView)群組資料:

[![在 iOS 和 Android 上的 CollectionView 中群組資料的螢幕截圖](grouping-images/grouped-data.png "具有群組資料的集合檢視")](grouping-images/grouped-data-large.png#lightbox "具有群組資料的集合檢視")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="group-data"></a>群組資料

必須先對數據進行分組,然後才能顯示數據。 這可以通過創建組清單來實現,其中每個組都是項清單。 群組清單應為`IEnumerable<T>`集合,其中`T`定義兩個資料段:

- 組名稱。
- 定義`IEnumerable`屬於組項的集合。

因此,對數據進行分組的過程是:

- 創建對單個項建模的類型。
- 創建對單個項組建模的類型。
- 創建集合`IEnumerable<T>`,`T`其中 為單個項組建模的類型。 因此,此集合是組的集合,用於存儲分組數據。
- 將數據添加到`IEnumerable<T>`集合中。

### <a name="example"></a>範例

對數據進行分組時,第一步是創建對單個項建模的類型。 下面的範例顯示了範例應用程式中`Animal`的類:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

類`Animal`對單個項建模。 然後可以創建對一組項建模的類型。 下面的範例顯示了範例應用程式中`AnimalGroup`的類:

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

類`AnimalGroup``List<T>`從 類繼承並添加表示`Name`組名稱 的屬性。

然後`IEnumerable<T>`可以建立群組的集合:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此代碼定義名為的集合`Animals`,其中集合中的每個項都是一`AnimalGroup`個 物件。 每個`AnimalGroup`物件包括一個名稱和一`List<Animal>`個 定義`Animal`組中 的物件的集合。

然後,可以將群組資料加入到集合中`Animals`:

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

此代碼在`Animals`集合中創建兩個組。 第一`AnimalGroup``List<Animal>`個命名`Bears`為 ,並包含熊詳細資訊的集合。 第二`AnimalGroup`個命名`Monkeys`為 ,包含`List<Animal>`猴子細節的集合。

## <a name="display-grouped-data"></a>顯示群組資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView)將顯示分組資料,前提是資料已正確群組,透過設定`IsGrouped`為`true`:

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

中每個項[`CollectionView`](xref:Xamarin.Forms.CollectionView)的外觀都通過[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)將 屬性[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)設置為 來定義。 有關詳細資訊,請參閱[定義項外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 預設情況下,[`CollectionView`](xref:Xamarin.Forms.CollectionView)將在組標題和頁腳中顯示組名稱。 可以通過自定義組標頭和組頁腳來更改此行為。

## <a name="customize-the-group-header"></a>自訂群組標頭

可以透過`CollectionView.GroupHeaderTemplate`設定屬性設定為 來自訂每個組標頭[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的外觀 :

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

在此範例中,每個組標頭都設置為 顯示組[`Label`](xref:Xamarin.Forms.Label)名稱的 ,並且具有其他外觀屬性集。 以下螢幕截圖顯示了自訂的組標頭:

[![在 iOS 和 Android 上的集合檢視中自訂組標題的螢幕截圖](grouping-images/customized-header.png "具有自訂群組標題的集合檢視")](grouping-images/customized-header-large.png#lightbox "具有自訂群組標題的集合檢視")

## <a name="customize-the-group-footer"></a>自訂群組註腳

可以通過`CollectionView.GroupFooterTemplate`屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

在此範例中,每個組頁腳設置為 顯示組中的項目[`Label`](xref:Xamarin.Forms.Label)數 的頁腳。 以下螢幕截圖顯示了自訂組頁腳:

[![在 iOS 和 Android 上的集合檢視中自訂組頁腳的螢幕截圖](grouping-images/customized-footer.png "具有自訂群組註腳的收集檢視")](grouping-images/customized-footer-large.png#lightbox "具有自訂群組註腳的收集檢視")

## <a name="empty-groups"></a>空組

當[`CollectionView`](xref:Xamarin.Forms.CollectionView)顯示分組數據時,它將顯示任何空組。 此類組將顯示一個組標題和頁腳,指示該組為空。 以下螢幕截圖顯示空列:

[![在 iOS 和 Android 上的集合檢視中顯示空組的螢幕截圖](grouping-images/empty-group.png "具有空列的集合檢視")](grouping-images/empty-group-large.png#lightbox "具有空列的集合檢視")

> [!NOTE]
> 在 iOS 10 和更低等級上,空組的組標題和頁腳可能全部`CollectionView`顯示在頂部 。

## <a name="group-without-templates"></a>無樣本的群組

[`CollectionView`](xref:Xamarin.Forms.CollectionView)無需將[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為:可以正確顯示已分組[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的資料 ,

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在這種情況下,可以通過重寫對單個項建`ToString`模 的類型中的方法以及對單個項組建模的類型來顯示有意義的數據。

## <a name="related-links"></a>相關連結

- [集合檢視(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
