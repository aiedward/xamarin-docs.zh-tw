---
title: Xamarin.Forms 強制回應頁面
description: Xamarin.Forms 可支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。 這篇文章會示範如何巡覽到強制回應頁面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 4540ac006993a46cb0ead9346c1cb960ac631926
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240134"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.Forms 強制回應頁面

_Xamarin.Forms 提供支援強制回應頁面。強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。這篇文章會示範如何巡覽到強制回應頁面。_

本文將討論下列主題：

- [執行巡覽](#Performing_Navigation)– 將頁面推送到強制回應堆疊雜訊頁面從強制回應堆疊、 停用 [上一頁] 按鈕和動畫畫面轉換。
- [瀏覽時，將資料傳遞](#Passing_Data_when_Navigating)– 傳遞資料透過頁面的建構函式，以及透過`BindingContext`。

## <a name="overview"></a>總覽

強制回應頁面可以是任何一個[頁面](~/xamarin-forms/user-interface/controls/pages.md)Xamarin.Forms 所支援的類型。 若要顯示強制回應頁面應用程式會直接將其推強制回應的堆疊，其中就會變成作用中的頁面上，如下列圖表所示：

![](modal-images/pushing.png "將頁面推送到強制回應堆疊")

若要返回上一頁應用程式會顯示強制回應的堆疊，從目前的頁面和新的最上層頁面會變成作用中的頁面上，如下圖所示：

![](modal-images/popping.png "移除從強制回應堆疊頁面")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行巡覽

[`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性會在任何 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 衍生類型上公開強制回應導覽方法。 這些方法提供的功能[推送強制回應頁面](#Pushing_Pages_to_the_Modal_Stack)強制回應的堆疊，以及[顯示強制回應頁面](#Popping_Pages_from_the_Modal_Stack)從強制回應的堆疊。

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性也會公開[ `ModalStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/)屬性可以從中取得獨佔式堆疊中的強制回應頁面。 不過，沒有執行強制回應堆疊操作，或快顯至強制回應導覽中根目錄的概念。 這是因為基礎平台上普遍不支援這些作業。

> [!NOTE]
> 執行強制回應頁面瀏覽不需要 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 執行個體。

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>將頁面推送到強制回應堆疊

若要瀏覽至`ModalPage`就必須叫用[ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性目前的頁面上，為下列程式碼範例示範：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

這會導致`ModalPage`推入的強制回應的堆疊，此時報表便成為使用中的頁面，執行個體提供已在選取項目[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)上`MainPage`執行個體。 `ModalPage`執行個體在下列螢幕擷取畫面所示：

![](modal-images/modalpage.png "強制回應頁面範例")

當[ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/)叫用時，發生下列事件：

- 頁面呼叫`PushModalAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)叫用，前提是基礎平台不是 Android 覆寫。
- 正要巡覽頁面具有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)叫用的覆寫。
- `PushAsync`工作完成。

不過，這些事件的發生順序取決於平台。 如需詳細資訊，請參閱[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 活頁簿。

> [!NOTE]
> 呼叫[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)和[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)覆寫無法被視為錯誤的頁面巡覽的保證指示。 例如，在 iOS、`OnDisappearing`應用程式終止時，覆寫在使用中的頁面上呼叫。

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>強制回應的堆疊中的雜訊頁面

使用中的頁面可以從強制回應堆疊推出按*回*按鈕在裝置上，不論是否使用實體按鈕，在裝置上的或螢幕上的按鈕。

若要以程式設計的方式返回原始頁面，`ModalPage` 執行個體必須叫用 [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) 方法，如下列程式碼範例所示：

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

這會導致`ModalPage`會從堆疊中移除強制回應，與新的最上層頁面成為使用中頁面的執行個體。 當[ `PopModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)叫用時，發生下列事件：

- 頁面呼叫`PopModalAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)叫用的覆寫。
- 傳回此頁面具有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)叫用，前提是基礎平台不是 Android 覆寫。
- `PopModalAsync`工作傳回。

不過，這些事件的發生順序取決於平台。 如需詳細資訊，請參閱[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 活頁簿。

### <a name="disabling-the-back-button"></a>停用 [上一頁] 按鈕

在 Android 上，使用者可以隨時返回前一個頁面按下標準*回*裝置上的按鈕。 強制回應頁面會要求使用者各自獨立的工作完成之前離開頁面，如果應用程式必須停用*回* 按鈕。 這可藉由覆寫[ `Page.OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed/)強制回應頁面上的方法。 如需詳細資訊，請參閱[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 活頁簿。

### <a name="animating-page-transitions"></a>動畫畫面轉換

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性時，每個頁面也提供覆寫的 push 和 pop 方法，包含了`boolean`控制是否顯示頁面動畫期間瀏覽，如下列程式碼所示的參數範例：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

設定`boolean`參數`false`停用時將參數設定為相同的動畫網頁切換`true`假設基礎平台支援，可讓網頁轉換動畫。 不過，缺少此參數的 push 和 pop 方法會預設啟用動畫。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>瀏覽時傳遞資料

有時候，很有必要的頁面巡覽期間，將資料傳遞至另一個頁面。 完成這兩項技術是由頁面的建構函式，透過傳遞的資料和設定新的頁面[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)資料。 每個將現在會依次討論。

### <a name="passing-data-through-a-page-constructor"></a>將資料傳遞到頁面的建構函式

在巡覽期間，將資料傳遞至另一個頁面的最簡單技巧是透過頁面建構函式參數，而是以下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼建立`MainPage`執行個體，在目前的日期和時間 ISO8601 格式傳遞。

`MainPage`的執行個體收到的資料，透過建構函式參數，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

資料隨即顯示在頁面設定[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性。

### <a name="passing-data-through-a-bindingcontext"></a>透過其他的傳遞資料

將資料傳遞至另一個頁面中，巡覽期間的替代方式是藉由設定新的頁面[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)資料，如下列程式碼範例所示：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

這個程式碼設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的`DetailPage`執行個體`Contact`執行個體，並瀏覽至`DetailPage`。

`DetailPage`然後使用資料繫結來顯示`Contact`執行個體資料，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下列程式碼範例示範如何完成資料繫結，在 C# 中：

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

資料隨即顯示在頁面的一系列的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>總結

本文示範如何巡覽到強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。


## <a name="related-links"></a>相關連結

- [頁面巡覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [強制回應 （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
