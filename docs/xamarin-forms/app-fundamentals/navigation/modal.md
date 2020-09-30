---
title: Xamarin.Forms 強制回應頁面
description: Xamarin.Forms 提供模式頁面的支援。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。 本文示範如何巡覽至強制回應頁面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ba5224c23eaed6b7c37312f8a765556d0263d9c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563025"
---
# <a name="no-locxamarinforms-modal-pages"></a>Xamarin.Forms 強制回應頁面

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms 提供模式頁面的支援。強制回應頁面可讓使用者完成無法離開的獨立工作，直到工作完成或取消為止。本文示範如何流覽至強制回應頁面。_

本文章討論下列主題：

- [執行導覽](#performing-navigation) – 將頁面推送到強制回應堆疊、從強制回應堆疊中快顯頁面、停用 [上一頁] 按鈕及以動畫顯示頁面轉換。
- [巡覽時傳遞資料](#passing-data-when-navigating) – 透過頁面建構函式及透過 `BindingContext` 傳遞資料。

## <a name="overview"></a>概觀

強制回應頁面可以是所支援的任何 [頁面](~/xamarin-forms/user-interface/controls/pages.md) 類型 Xamarin.Forms 。 若要顯示強制回應頁面，應用程式會將其推送到強制回應堆疊上，該頁面就會變成使用中頁面，如下圖所示：

![將頁面推送到強制回應堆疊](modal-images/pushing.png)

若要返回上一頁，應用程式將會從強制回應堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中頁面，如下圖所示：

![從強制回應堆疊中快顯頁面](modal-images/popping.png)

## <a name="performing-navigation"></a>執行導覽

強制回應導覽方法是由 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 任何衍生類型上的屬性所公開 [`Page`](xref:Xamarin.Forms.Page) 。 這些方法可讓您[推送強制回應頁面](#pushing-pages-to-the-modal-stack)到強制回應堆疊上，以及從強制回應堆疊中[快顯強制回應頁面](#popping-pages-from-the-modal-stack)。

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)屬性也會公開可從中取得強制回應 [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) 堆疊中之模式頁面的屬性。 不過，沒有執行強制回應堆疊操作，或快顯至強制回應導覽中根目錄的概念。 這是因為基礎平台上普遍不支援這些作業。

> [!NOTE]
> 執行強制回應 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 頁面導覽不需要實例。

### <a name="pushing-pages-to-the-modal-stack"></a>將頁面推送到強制回應堆疊

若要流覽至， `ModalPage` 必須在 [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 目前頁面的屬性上叫用方法，如下列程式碼範例所示：

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

這會導致 `ModalPage` 實例推送到強制回應堆疊上，而在此堆疊變成使用中頁面，但前提是已在 [`ListView`](xref:Xamarin.Forms.ListView) 實例上選取專案 `MainPage` 。 `ModalPage` 執行個體如下列螢幕擷取畫面所示：

![強制回應頁面範例](modal-images/modalpage.png)

叫用時 [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) ，會發生下列事件：

- `PushModalAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 如果基礎平臺不是 Android，則呼叫的頁面會叫用它的覆寫。
- 正在流覽的頁面已叫用其覆 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 寫。
- `PushAsync` 工作完成。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 本書的第 [24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Xamarin.Forms 。

> [!NOTE]
> [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)和 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫的呼叫無法被視為頁面導覽的保證指標。 例如，在 iOS 上，當應用程式終止時，會在使用中頁面上呼叫 `OnDisappearing` 覆寫。

### <a name="popping-pages-from-the-modal-stack"></a>從強制回應堆疊中快顯頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁]** 按鈕都可以從強制回應堆疊中快顯使用中頁面。

若要以程式設計的方式返回原始頁面， `ModalPage` 實例必須 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) 叫用方法，如下列程式碼範例所示：

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

這會使 `ModalPage` 執行個體從強制回應堆疊中移除，進而使新的最上層頁面變成使用中頁面。 叫用時 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) ，會發生下列事件：

- 呼叫的頁面會叫用它的覆 `PopModalAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 寫。
- [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)如果基礎平臺不是 Android，則傳回的頁面會叫用其覆寫。
- 傳回 `PopModalAsync` 工作。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 本書的第 [24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Xamarin.Forms 。

### <a name="disabling-the-back-button"></a>停用 [上一頁] 按鈕

在 Android 上，使用者可以隨時在裝置上按下標準 [上一頁]** 按鈕來返回上一頁。 如果強制回應頁面要求使用者完成獨立的工作再離開頁面，則應用程式必須停用 [上一頁]** 按鈕。 這可以藉由覆寫強制回應 [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) 頁面上的方法來完成。 如需詳細資訊，請參閱 Charles Petzold 本書的第 [24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Xamarin.Forms 。

### <a name="animating-page-transitions"></a>以動畫顯示頁面轉換

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)每個頁面的屬性也會提供覆寫的 push 和 pop 方法，其中包含 `boolean` 控制是否要在導覽期間顯示頁面動畫的參數，如下列程式碼範例所示：

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

只要基礎平台支援，將 `boolean` 參數設定為 `false` 會停用頁面轉換動畫，將此參數設定為 `true` 會啟用頁面轉換動畫。 不過，缺少此參數的 push 和 pop 方法預設會啟用動畫。

## <a name="passing-data-when-navigating"></a>巡覽時傳遞資料

有時必須在巡覽期間將某頁資料傳遞到另一頁。 完成這項工作的兩種方法是透過頁面的函式來傳遞資料，以及將新頁面設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 為數據。 現在將依序討論各種方法。

### <a name="passing-data-through-a-page-constructor"></a>透過頁面建構函式傳遞資料

在巡覽期間將資料傳遞到另一頁的最簡單方法，就是透過頁面建構函式參數，如下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼會建立 `MainPage` 執行個體，並以 ISO8601 格式傳入目前的日期和時間。

`MainPage` 執行個體會透過建構函式參數接收資料，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

然後藉由設定屬性，在頁面上顯示資料 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 。

### <a name="passing-data-through-a-bindingcontext"></a>透過 BindingContext 傳遞資料

在流覽期間將資料傳遞至另一個頁面的替代方法是將新頁面設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 為數據，如下列程式碼範例所示：

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

這段程式碼會將實例的設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `DetailPage` 為 `Contact` 實例，然後流覽至 `DetailPage` 。

`DetailPage` 會接著使用資料繫結來顯示 `Contact` 執行個體資料，如下列 XAML 程式碼範例所示：

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

下列程式碼範例示範如何在 C# 中完成資料繫結：

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

然後，資料會由一系列控制項顯示在頁面上 [`Label`](xref:Xamarin.Forms.Label) 。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>摘要

本文示範了如何巡覽至強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。

## <a name="related-links"></a>相關連結

- [頁面流覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal Pages (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-modal) (強制回應頁面 (範例))
- [Passing Data (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (傳遞資料 (範例))