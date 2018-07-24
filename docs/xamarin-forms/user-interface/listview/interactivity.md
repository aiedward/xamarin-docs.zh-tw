---
title: ListView 互動性
description: 這篇文章說明如何藉由實作選取項目、 內容動作，以及提取以重新整理時，將互動功能新增至 Xamarin.Forms ListView。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 77a48e36f33fc690306f5e590f9f4f3064fe1ddf
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202938"
---
# <a name="listview-interactivity"></a>ListView 互動性

ListView 支援透過下列方式提供資料互動：

- [**選取範圍] & [點選**](#selectiontaps) &ndash;回應點選，以及選取項目/deselections 的項目。 啟用或停用 （預設為啟用） 的資料列選取範圍。
- [**內容動作**](#Context_Actions) &ndash;公開功能每個項目，例如撥動--刪除。
- [**若要重新整理提取**](#Pull_to_Refresh) &ndash;實作使用者都是從原生體驗提取以重新整理慣用語。

<a name="selectiontaps" />

## <a name="selection--taps"></a>選取範圍] & [點選

[ `ListView` ](xref:Xamarin.Forms.ListView)選取模式控制 splittunneling [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode)列舉型別：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 表示單一項目可以選取，以選取要反白顯示的項目。 這是預設值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 表示無法選取項目。

當使用者點選項目時，則不會引發兩個事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 選取新的項目時引發。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 點選一個項目時引發。

> [!NOTE]
> 兩次點選相同的項目會引發兩個[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，但會只觸發單一[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。

當[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[ `Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)可以選取[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)並[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)會引發事件，而[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬性會設定為所選的項目值。

當[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[ `None`](xref:Xamarin.Forms.ListViewSelectionMode.None)中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)無法選取[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)將不會引發事件，而[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬性會保留`null`。 不過， [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)仍會引發事件，並點選期間短暫反白點選的項目。

當已選取項目和[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性已從[ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single)至[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None)， [ `SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性會設定為`null`並[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)將會引發事件，與`null`項目。

下列螢幕擷取畫面所示[ `ListView` ](xref:Xamarin.Forms.ListView)與預設的選取模式：

![](interactivity-images/selection-default.png "使用選取項目啟用的 ListView")

### <a name="disabling-selection"></a>停用選取範圍

若要停用[ `ListView` ](xref:Xamarin.Forms.ListView)選取項目組[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)屬性設[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>內容動作
通常，使用者會想要採取的動作中的項目`ListView`。 例如，請考慮的電子郵件中的郵件應用程式清單。 您可以在 iOS 上，撥動刪除訊息：

![](interactivity-images/context-default.png "ListView 內容動作")

內容動作可以在 C# 和 XAML 實作。 下面您會發現特定指南，但先讓我們同時看看一些重要的實作詳細資料。

內容動作會建立使用`MenuItem`s。 功能表項目本身，不是 ListView 會引發 MenuItems 的點選事件。 這點不同於資料格，其中的 ListView 引發事件，而不是資料格會處理的點選事件。 因為 ListView 引發事件，其事件處理常式會提供類似的項目已選取，或點選的重要資訊。

根據預設，功能表項目會具有無從得知它屬於哪一個儲存格。 `CommandParameter` 位於`MenuItem`儲存物件，例如背後的 MenuItem ViewCell 物件。 `CommandParameter` 可以在 XAML 和 C# 兩種設定。

### <a name="c"></a>C#  

內容動作可以在任何實作`Cell`子類別 （只要沒有正在使用群組標頭的形式） 建立`MenuItem`s，並將它們加入至`ContextActions`儲存格的集合。 您有下列屬性可以設定內容動作：

* **文字**&ndash;功能表項目中顯示的字串。
* **按下**&ndash;事件，當按一下項目。
* **IsDestructive** &ndash; （選擇性） 若為 true 的項目不同的方式呈現在 iOS 上。

內容的多個動作可以加入至資料格，但只有一個應有`IsDestructive`設定為`true`。 下列程式碼示範如何操作動作將會新增至`ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s 也可以建立 XAML 集合中以宣告方式。 下列 XAML 會示範自訂儲存格與實作的兩個內容動作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
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
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer` Android 具有可覆寫`UpdateMenuItemIcon`方法，可用來從自訂載入圖示`Drawable`。 此覆寫可讓您使用 SVG 影像與圖示上`MenuItem`在 Android 上的執行個體。

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>提取以重新整理
使用者都是下拉清單中的資料將會重新整理該清單。 `ListView` 支援此--蜪鎏。 若要啟用提取以重新整理功能，請設定`IsPullToRefreshEnabled`設為 true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

提取提取-即可重新整理以使用者的身分：

![](interactivity-images/refresh-start.png "ListView 拖動以重新整理進行中")

提取以更新使用者身分已發行的提取。 這是什麼使用者會看到當您更新的清單： ![ ] (interactivity-images/refresh-in-progress.png "ListView 提取以重新整理完成")

ListView 會公開一些可讓您回應提取以重新整理事件的事件。

-  `RefreshCommand`就會叫用和`Refreshing`呼叫的事件。 `IsRefreshing` 將會設定為`true`。
-  您應該執行任何程式碼，才能重新整理清單檢視中，在命令或事件的內容。
-  重新整理時完成，請呼叫`EndRefresh`或設定`IsRefreshing`到`false`告訴您完成時的 [清單] 檢視。

`CanExecute`遵守屬性，可讓您一個方式來控制是否應該啟用提取以重新整理命令。



## <a name="related-links"></a>相關連結

- [ListView 互動性 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [1.4 的版本資訊](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的版本資訊](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
