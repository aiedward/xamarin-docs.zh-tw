---
title: Xamarin.Forms 強制回應頁面
description: Xamarin.Forms 可支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。 本文示範如何巡覽至強制回應頁面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 7a4c67f067b73873c3d1de4499abda2703217ddf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760831"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.Forms 強制回應頁面

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms 支援模態頁面。模式頁鼓勵使用者完成在任務完成或取消之前無法導航的自包含任務。本文演示如何導航到模式頁。_

本文章討論下列主題：

- [執行導覽](#Performing_Navigation) – 將頁面推送到強制回應堆疊、從強制回應堆疊中快顯頁面、停用 [上一頁] 按鈕及以動畫顯示頁面轉換。
- [巡覽時傳遞資料](#Passing_Data_when_Navigating) – 透過頁面建構函式及透過 `BindingContext` 傳遞資料。

## <a name="overview"></a>概觀

強制回應頁面可以是 Xamarin.Forms 所支援的任何一種[頁面](~/xamarin-forms/user-interface/controls/pages.md)類型。 若要顯示強制回應頁面，應用程式會將其推送到強制回應堆疊上，該頁面就會變成使用中頁面，如下圖所示：

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

若要返回上一頁，應用程式將會從強制回應堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中頁面，如下圖所示：

![](modal-images/popping.png "Popping a Page from the Modal Stack")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行導覽

模式導航方法由[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)屬性在[`Page`](xref:Xamarin.Forms.Page)任何 派生類型上公開。 這些方法可讓您[推送強制回應頁面](#Pushing_Pages_to_the_Modal_Stack)到強制回應堆疊上，以及從強制回應堆疊中[快顯強制回應頁面](#Popping_Pages_from_the_Modal_Stack)。

屬性[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)還公開一[`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)個 屬性,可以從中獲取模態堆疊中的模態頁。 不過，沒有執行強制回應堆疊操作，或快顯至強制回應導覽中根目錄的概念。 這是因為基礎平台上普遍不支援這些作業。

> [!NOTE]
> 執行[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)模式頁導航不需要實例。

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>將頁面推送到強制回應堆疊

要瀏覽`ModalPage`到 ,必須在[`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*)[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)目前的頁的屬性上調用 方法,如以下代碼範例所示:

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

這將導致將`ModalPage`實例推送到模態堆疊上,其中它成為活動頁,前提[`ListView`](xref:Xamarin.Forms.ListView)`MainPage`是在 實例上選擇了項。 `ModalPage` 執行個體如下列螢幕擷取畫面所示：

![](modal-images/modalpage.png "Modal Page Example")

呼叫[`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*)時,會發生以下事件:

- 頁面調用`PushModalAsync`[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)具有 重寫,前提是基礎平臺不是 Android。
- 要導航到的頁面已調用其[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)覆蓋。
- `PushAsync` 工作完成。

不過，發生這些事件的確切順序則取決於平台。 有關詳細資訊,請參閱查理斯·佩佐德的 Xamarin.Forms 書的第[24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

> [!NOTE]
> 對[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)和 重寫的調用不能被視為頁面導航的保證指示。 例如，在 iOS 上，當應用程式終止時，會在使用中頁面上呼叫 `OnDisappearing` 覆寫。

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>從強制回應堆疊中快顯頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁]** 按鈕都可以從強制回應堆疊中快顯使用中頁面。

要以程式設計方式傳回到原始頁`ModalPage`, 實體必須呼[`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)叫 方法,如以下代碼範例所示:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

這會使 `ModalPage` 執行個體從強制回應堆疊中移除，進而使新的最上層頁面變成使用中頁面。 呼叫[`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)時,會發生以下事件:

- 頁面調用`PopModalAsync`具有調用[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)其 重寫。
- 返回的頁面已調用其[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)覆蓋,前提是基礎平臺不是 Android。
- 傳回 `PopModalAsync` 工作。

不過，發生這些事件的確切順序則取決於平台。 有關詳細資訊,請參閱查理斯·佩佐德的 Xamarin.Forms 書的第[24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

### <a name="disabling-the-back-button"></a>停用 [上一頁] 按鈕

在 Android 上，使用者可以隨時在裝置上按下標準 [上一頁]** 按鈕來返回上一頁。 如果強制回應頁面要求使用者完成獨立的工作再離開頁面，則應用程式必須停用 [上一頁]** 按鈕。 這可以通過在模態頁上重[`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed)寫 方法來實現。 如需詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 一書的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

### <a name="animating-page-transitions"></a>以動畫顯示頁面轉換

每個[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)頁面的屬性還提供重寫的推送和彈出方法,其中包括一`boolean`個 參數,用於控制在導航期間是否顯示頁面動畫,如以下代碼範例所示:

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

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>巡覽時傳遞資料

有時必須在巡覽期間將某頁資料傳遞到另一頁。 實現此目的的兩種技術是通過頁面構造函數傳遞數據,以及將新頁面[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設置為數據。 現在將依序討論各種方法。

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

然後,通過設置[`Label.Text`](xref:Xamarin.Forms.Label.Text)屬性在頁面上顯示數據。

### <a name="passing-data-through-a-bindingcontext"></a>透過 BindingContext 傳遞資料

在導覽期間將資料傳遞到另一個頁面的另一種方法是將新[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)頁面 設定為資料,如以下代碼範例所示:

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

[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)此程式將實體設定`DetailPage`的實體設定到實體 ,`Contact`然後瀏覽到`DetailPage`。

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

然後,數據由一系列[`Label`](xref:Xamarin.Forms.Label)控件顯示在頁面上。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>總結

本文示範了如何巡覽至強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。

## <a name="related-links"></a>相關連結

- [頁面導覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal Pages (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal) (強制回應頁面 (範例))
- [Passing Data (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (傳遞資料 (範例))
