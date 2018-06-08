---
title: ListView 互動功能
description: 透過實作選取項目、 撥動-刪除和重新整理提取至您的 ListView 加入互動功能。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/01/2018
ms.openlocfilehash: 5fe821e7e5254da8febbbde518b9fd42526bf262
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848118"
---
# <a name="listview-interactivity"></a>ListView 互動功能

清單檢視支援的資料會透過下列方式呈現與互動：

- [**選取項目 （& s) 點選**](#selectiontaps) &ndash;回應點選，以及選取項目/deselections 的項目。 啟用或停用 （預設為啟用） 的資料列選取。
- [**內容動作**](#Context_Actions) &ndash;公開功能每個項目，例如，若要刪除撥動。
- [**若要重新整理提取**](#Pull_to_Refresh) &ndash;實作使用者預期會有的原生的體驗從提取-重新整理慣用語。

<a name="selectiontaps" />

## <a name="selection--taps"></a>選取項目 （& s) 點選
`ListView` 支援選取的一個項目一次。 預設為開啟選取項目。 當使用者點選項目時，會引發兩個事件：`ItemTapped`和`ItemSelected`。 請注意，兩次點選相同的項目不會引發多個`ItemSelected`事件，但會引發多個`ItemTapped`事件。 也請注意，`ItemSelected`如果取消選取項目時則會呼叫。

請注意，`ItemSelected`當項目就會取消及選取此選項時呼叫。 這表示您必須檢查是否有 null`SelectedItem`中您`ItemSelected`事件處理常式之前，您可以使用它：

```csharp
void OnSelection (object sender, SelectedItemChangedEventArgs e)
{
  if (e.SelectedItem == null) {
    return; //ItemSelected is called on deselection, which results in SelectedItem being set to null
  }
  DisplayAlert ("Item Selected", e.SelectedItem.ToString (), "Ok");
  //((ListView)sender).SelectedItem = null; //uncomment line if you want to disable the visual selection state.
}
```

### <a name="disabling-selection"></a>停用選取範圍

如果您想要停用選取範圍，處理`ItemSelected`事件以及組`SelectedItem`屬性設為 null:

```csharp
SelectionDemoList.ItemSelected += (sender, e) => {
    ((ListView)sender).SelectedItem = null;
};
```

與啟用的選取項目：

![](interactivity-images/selection-default.png "清單檢視與啟用的選取項目")

<a name="Context_Actions" />

## <a name="context-actions"></a>內容的動作
通常，使用者會想要採取的動作中的項目`ListView`。 例如，請考慮一份電子郵件的郵件應用程式中。 在 iOS 上，您可以刪除訊息撥動::

![](interactivity-images/context-default.png "動作內容的 ListView")

內容動作可以在 C# 和 XAML 來實作。 以下會發現特定指南，但首先讓我們兩者看看一些重要實作詳細資料。

使用建立內容動作`MenuItem`s。 功能表項目本身不 ListView 所引發的 MenuItems 點選事件。 這點不同於點選事件的資料格，其中 ListView 引發事件，而不是資料格的處理方式。 ListView 引發事件，因為其事件處理常式會提供類似的項目已選取，或點選的金鑰資訊。

根據預設，功能表項目便無法得知它屬於哪一個儲存格。 `CommandParameter` 位於`MenuItem`儲存物件，例如背後的 MenuItem ViewCell 物件。 `CommandParameter` 可以將 XAML 和 C# 中。

### <a name="c"></a>C#  

可以在任何實作內容動作`Cell`子類別 （只要佔用做為群組標頭） 藉由建立`MenuItem`s，並將它們加入至`ContextActions`儲存格的集合。 您有下列屬性可以設定內容動作：

* **文字**&ndash;功能表項目中出現的字串。
* **按下**&ndash;事件時按一下項目。
* **IsDestructive** &ndash; （選擇性） 為 true 時轉譯項目以不同的方式在 iOS 上。

內容的多個動作可以加入一個資料格，不過只有其中應該有`IsDestructive`設`true`。 下列程式碼示範如何將內容動作加入至`ViewCell`:

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

`MenuItem`s 也可以建立 XAML 集合中以宣告方式。 以下的 XAML 實作的兩個內容動作示範自訂儲存格：

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

在程式碼後置檔案中，確定`Clicked`方法實作：

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
> `NavigationPageRenderer` Android 具有可覆寫`UpdateMenuItemIcon`方法可以用來從自訂載入圖示`Drawable`。 此覆寫會讓您能夠使用作為圖示上的 SVG 影像`MenuItem`在 Android 上的執行個體。

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>若要重新整理提取
使用者預期會有的往清單中的資料將會重新整理該清單。 `ListView` 支援這個--現成。 若要啟用提取-重新整理功能，請設定`IsPullToRefreshEnabled`為 true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

提取提取更新以使用者的身分：

![](interactivity-images/refresh-start.png "ListView 拖動以重新整理進行中")

提取更新以使用者身分發行提取。 這是什麼，使用者會看到當您更新的清單： ![ ] (interactivity-images/refresh-in-progress.png "ListView 拖動以重新整理完成")

清單檢視會公開一些可讓您回應更新提取事件的事件。

-  `RefreshCommand`會叫用和`Refreshing`呼叫事件。 `IsRefreshing` 將設定為`true`。
-  您應該執行任何程式碼，才能重新整理清單檢視中，在命令或事件的內容。
-  重新整理時已完成，請呼叫`EndRefresh`或設定`IsRefreshing`至`false`告訴您完成清單檢視。

`CanExecute`遵守屬性，可讓您能夠控制是否應該啟用提取-重新整理命令。



## <a name="related-links"></a>相關連結

- [ListView 互動功能 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [1.4 版本資訊](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 版的版本資訊](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
