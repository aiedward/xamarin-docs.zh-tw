---
title: Xamarin.Forms 強制回應頁面
description: Xamarin.Forms 可支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。 這篇文章會示範如何瀏覽至強制回應頁面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 44aee8500c7de2ae56b59049368d6025ec49cc5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994813"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.Forms 強制回應頁面

_Xamarin.Forms 可支援強制回應頁面。強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。這篇文章會示範如何瀏覽至強制回應頁面。_

這篇文章討論下列主題：

- [執行瀏覽](#Performing_Navigation)– 將頁面推送至強制回應堆疊上，從強制回應堆疊彈出頁面、 停用 [上一頁] 按鈕，並以動畫顯示轉換頁面。
- [瀏覽時，將資料傳遞](#Passing_Data_when_Navigating)– 傳遞資料透過頁面的建構函式，以及透過`BindingContext`。

## <a name="overview"></a>總覽

強制回應頁面可以是任一[網頁](~/xamarin-forms/user-interface/controls/pages.md)Xamarin.Forms 所支援的類型。 若要顯示強制回應頁面應用程式會將其推送至強制回應堆疊，其中它會變成作用中的頁面上，如下圖所示：

![](modal-images/pushing.png "將頁面推送至強制回應堆疊")

若要傳回至上一頁應用程式將會顯示強制回應堆疊上，從目前的頁面和新的最上層頁面變成使用中的頁面，如下圖所示：

![](modal-images/popping.png "快顯強制回應堆疊的頁面")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行導覽

[`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) 屬性會在任何 [`Page`](xref:Xamarin.Forms.Page) 衍生類型上公開強制回應導覽方法。 這些方法會提供能夠[推送強制回應頁面](#Pushing_Pages_to_the_Modal_Stack)強制回應堆疊，並[快顯強制回應頁面](#Popping_Pages_from_the_Modal_Stack)從強制回應堆疊。

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性也會公開[ `ModalStack` ](xref:Xamarin.Forms.INavigation.ModalStack)可以從中取得獨佔式堆疊中的強制回應頁面的屬性。 不過，沒有執行強制回應堆疊操作，或快顯至強制回應導覽中根目錄的概念。 這是因為基礎平台上普遍不支援這些作業。

> [!NOTE]
> 執行強制回應頁面瀏覽不需要 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體。

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>將頁面推送至強制回應堆疊

若要瀏覽至`ModalPage`就必須叫用[ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性目前的頁面上，如下列程式碼範例所示：

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

這會導致`ModalPage`執行個體推送至強制回應堆疊，而變成使用中的頁面，可讓您提供已在選取項目[ `ListView` ](xref:Xamarin.Forms.ListView)上`MainPage`執行個體。 `ModalPage`執行個體在下列螢幕擷取畫面所示：

![](modal-images/modalpage.png "強制回應頁面範例")

當[ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*)叫用時，會發生下列事件：

- 頁面上呼叫`PushModalAsync`有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)叫用，前提是基礎平台不是 Android 的覆寫。
- 巡覽至頁面具有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)叫用的覆寫。
- `PushAsync`工作完成。

不過，發生這些事件的確切順序取決於平台。 如需詳細資訊，請參閱 <<c0> [ 第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

> [!NOTE]
> 呼叫[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)並[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)覆寫無法被視為保證找出的頁面巡覽的跡象。 例如，在 iOS 上，`OnDisappearing`應用程式終止時，將會呼叫使用中頁面的覆寫。

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>從強制回應堆疊彈出頁面

使用中的頁面可以從強制回應堆疊推出，藉由按下*回*按鈕在裝置上，不論這是否在裝置上的實體按鈕或螢幕上的按鈕。

若要以程式設計的方式返回原始頁面，`ModalPage` 執行個體必須叫用 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) 方法，如下列程式碼範例所示：

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

這會導致`ModalPage`與新的最上層頁面變成使用中的頁面，從強制回應堆疊移除的執行個體。 當[ `PopModalAsync` ](xref:Xamarin.Forms.INavigation.PopModalAsync)叫用時，會發生下列事件：

- 頁面上呼叫`PopModalAsync`有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)叫用的覆寫。
- 所傳回的頁面具有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)叫用，前提是基礎平台不是 Android 的覆寫。
- `PopModalAsync`工作傳回。

不過，發生這些事件的確切順序取決於平台。 如需詳細資訊，請參閱 <<c0> [ 第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

### <a name="disabling-the-back-button"></a>停用 [上一頁] 按鈕

在 Android 上，使用者可以隨時返回前一頁按標準*回*裝置上的按鈕。 如果強制回應頁面要求使用者完成各自獨立的工作之前離開頁面時，必須停用應用程式*回* 按鈕。 這可藉由覆寫[ `Page.OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed)強制回應頁面上的方法。 如需詳細資訊，請參閱[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

### <a name="animating-page-transitions"></a>以動畫顯示轉換頁面

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)每個頁面的屬性也會提供覆寫的 push 和 pop 方法，包括`boolean`控制是否要顯示的頁面動畫期間瀏覽，如下列程式碼所示的參數範例：

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

設定`boolean`參數來`false`會停用時將參數設定的頁面轉換動畫`true`前提是它支援基礎平台，可讓頁面轉換動畫。 不過，缺少此參數的 push 和 pop 方法會依預設啟用動畫。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>瀏覽時將資料傳遞

有時是必要的頁面，即可將資料傳遞至另一個頁面中，在巡覽期間。 兩個技術可完成這項作業是由傳遞的資料，透過頁面的建構函式，並藉由設定新的頁面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)資料。 每個會現在會依次討論。

### <a name="passing-data-through-a-page-constructor"></a>將資料傳遞到網頁的建構函式

將資料傳遞至另一個頁面中，在巡覽期間的最簡單技巧是透過頁面的建構函式參數中的下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼會建立`MainPage`執行個體，傳入目前的日期和時間 ISO8601 格式。

`MainPage`的執行個體收到的資料，透過建構函式參數，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

資料隨即顯示在頁面上設定[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)屬性。

### <a name="passing-data-through-a-bindingcontext"></a>將資料傳遞到 BindingContext

將資料傳遞至另一個頁面中，在巡覽期間的替代方式是藉由設定新的頁面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)資料，如下列程式碼範例所示：

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

此程式碼會設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`DetailPage`執行個體`Contact`執行個體，並瀏覽至`DetailPage`。

`DetailPage`接著會使用資料繫結顯示`Contact`執行個體資料，如下列 XAML 程式碼範例所示：

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

資料隨即顯示在頁面的一系列[ `Label` ](xref:Xamarin.Forms.Label)控制項。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>總結

這篇文章會示範如何瀏覽至強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。


## <a name="related-links"></a>相關連結

- [頁面巡覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [強制回應 （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
