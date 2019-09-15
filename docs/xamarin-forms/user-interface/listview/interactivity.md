---
title: ListView 互動性
description: 這篇文章說明如何藉由實作選取項目、 內容動作，以及提取以重新整理時，將互動功能新增至 Xamarin.Forms ListView。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: e2d51f42339b1ff2a99f2a00bb5a9e662fb01d87
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998070"
---
# <a name="listview-interactivity"></a>ListView 互動性

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

[Xamarin [`ListView`](xref:Xamarin.Forms.ListView) ] 類別可支援使用者與它所呈現的資料互動。

## <a name="selection-and-taps"></a>選取和點擊

[ `ListView` ](xref:Xamarin.Forms.ListView)選取模式控制 splittunneling [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode)列舉型別：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 表示單一項目可以選取，以選取要反白顯示的項目。 這是預設值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 表示無法選取項目。

當使用者點選項目時，則不會引發兩個事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 選取新的項目時引發。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 點選一個項目時引發。

兩次點選相同的項目會引發兩個[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，但會只觸發單一[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。

> [!NOTE]
> [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) `ItemIndex` [`ListView`](xref:Xamarin.Forms.ListView)類別，其中包含[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件的事件引數、具有和屬性，以及屬性，其值代表分項專案之中的索引。 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 同樣地， [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)類別（包含[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件的事件引數）具有[`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)屬性，而`SelectedItemIndex`屬性（property）的值代表所選項目`ListView`之中的索引。

當[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[ `Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)可以選取[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)並[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)會引發事件，而[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬性會設定為所選的項目值。

當[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[ `None`](xref:Xamarin.Forms.ListViewSelectionMode.None)中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)無法選取[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)將不會引發事件，而[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬性會保留`null`。 不過， [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)仍會引發事件，並點選期間短暫反白點選的項目。

當已選取項目和[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性已從[ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single)至[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None)， [ `SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性會設定為`null`並[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)將會引發事件，與`null`項目。

下列螢幕擷取畫面所示[ `ListView` ](xref:Xamarin.Forms.ListView)與預設的選取模式：

![](interactivity-images/selection-default.png "使用選取項目啟用的 ListView")

### <a name="disable-selection"></a>停用選取範圍

若要停用[ `ListView` ](xref:Xamarin.Forms.ListView)選取項目組[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>內容動作

通常，使用者會想要採取的動作中的項目`ListView`。 例如，請考慮的電子郵件中的郵件應用程式清單。 您可以在 iOS 上，撥動刪除訊息：

![](interactivity-images/context-default.png "ListView 內容動作")

內容動作可以在 C# 和 XAML 實作。 下面您會發現特定指南，但先讓我們同時看看一些重要的實作詳細資料。

內容動作是使用`MenuItem`元素建立。 點一下`MenuItems`物件的事件會`MenuItem`由本身引發，而不`ListView`是。 這不同于資料格的點按`ListView`事件處理方式，其中會引發事件，而不是資料格。 `ListView`因為正在引發事件，所以會提供其事件處理常式的重要資訊，例如選取或點擊的專案。

根據預設， `MenuItem`無法得知它所屬的資料格。 屬性可在上`MenuItem`用來儲存物件，例如的後方`MenuItem` `ViewCell`物件。 `CommandParameter` 屬性可以同時在 XAML 和C#中設定。 `CommandParameter`

### <a name="xaml"></a>XAML

`MenuItem`元素可以在 XAML 集合中建立。 下列 XAML 會示範自訂儲存格與實作的兩個內容動作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

在程式碼後置檔案中，請確定`Clicked`方法實作：

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer` Android 具有可覆寫`UpdateMenuItemIcon`方法，可用來從自訂載入圖示`Drawable`。 此覆寫可讓您使用 SVG 影像與圖示上`MenuItem`在 Android 上的執行個體。

### <a name="code"></a>程式碼

您可以藉由建立`Cell` `MenuItem`實例並將其加入儲存格的`ContextActions`集合中，在任何子類別中執行內容動作（只要它不是用來做為群組標頭）。 您有下列屬性可以設定內容動作：

- **文字**&ndash;功能表項目中顯示的字串。
- **按下**&ndash;事件，當按一下項目。
- **IsDestructive**&ndash; （選擇性）如果為 true，則會在 iOS 上以不同的方式轉譯專案。

內容的多個動作可以加入至資料格，但只有一個應有`IsDestructive`設定為`true`。 下列程式碼示範如何操作動作將會新增至`ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>提取至重新整理

使用者都是下拉清單中的資料將會重新整理該清單。 此[`ListView`](xref:Xamarin.Forms.ListView)控制項支援這個現成的。 若要啟用提取重新整理功能，請將[`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)設定`true`為：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

對等的 C# 程式碼是：

```csharp
listView.IsPullToRefreshEnabled = true;
```

重新整理期間會出現一個微調，預設為黑色。 不過，您可以藉由將`RefreshControlColor`屬性設定[`Color`](xref:Xamarin.Forms.Color)為，在 iOS 和 Android 上變更微調色彩：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

對等的 C# 程式碼是：

```csharp
listView.RefreshControlColor = Color.Red;
```

下列螢幕擷取畫面顯示在使用者提取時的提取重新整理：

![](interactivity-images/refresh-start.png "ListView 拖動以重新整理進行中")

下列螢幕擷取畫面顯示在使用者釋放提取之後的提取重新整理，並在更新時[`ListView`](xref:Xamarin.Forms.ListView)顯示微調按鈕：

![](interactivity-images/refresh-in-progress.png "ListView 提取以重新整理完成")

[`ListView`](xref:Xamarin.Forms.ListView)引發事件以起始重新整理， [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)並將屬性設定為`true`。 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 要重新整理之`ListView`內容所需的任何程式碼，都應該由`Refreshing`事件的事件處理常式或由執行[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)的方法執行。 重新整理之後`IsRefreshing` ，應該將屬性[`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh)設定為`false`，或應呼叫方法，以表示重新整理已完成。 `ListView`

> [!NOTE]
> 定義[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)時，可以指定`CanExecute`命令的方法來啟用或停用命令。

## <a name="related-links"></a>相關連結

- [ListView 互動性 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
