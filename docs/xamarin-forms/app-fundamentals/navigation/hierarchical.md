---
title: 階層式導覽
description: 本文示範如何使用 NavigationPage 類別，在一堆後進先出 (LIFO) 的頁面中執行瀏覽。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 11ad1fb18d1263eb77ef037350a3633510934c42
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305112"
---
# <a name="hierarchical-navigation"></a>階層式導覽

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_NavigationPage 類別提供階層式導覽體驗，讓使用者能夠視需要，向前和向後流覽頁面。類別會將導覽實作為頁面物件的後進先出（LIFO）堆疊。本文示範如何使用 NavigationPage 類別，在一堆頁面中執行導覽。_

若要從一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中頁面，如下圖所示：

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

若要返回上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中頁面，如下圖所示：

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性會在任何 [`Page`](xref:Xamarin.Forms.Page) 衍生類型上公開導覽方法。 這些方法可讓您將頁面推送到導覽堆疊上、從導覽堆疊中快顯頁面，以及執行堆疊操作。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行導覽

在階層式導覽中，[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別用來導覽一疊 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。 下列螢幕擷取畫面顯示每個平台上 `NavigationPage` 的主要元件：

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的配置取決於平台：

- 在 iOS 上，頁面頂端有一個導覽列，其中顯示標題，並具有可返回上一頁的 [上一頁] 按鈕。
- 在 Android 上，頁面頂端有一個導覽列，其中顯示標題、圖示，以及可返回上一頁的 [上一頁] 按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在通用 Windows 平台上，頁面頂端有一個顯示標題的導覽列。

在所有平台上，[`Page.Title`](xref:Xamarin.Forms.Page.Title) 屬性的值會顯示為頁面標題。

> [!NOTE]
> 建議僅以 `NavigationPage` 執行個體填入 `ContentPage`。

### <a name="creating-the-root-page"></a>建立根頁面

加入至導覽堆疊中的第一頁指的是應用程式的*根*頁面，下列程式碼範例會示範如何完成此作業：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

這會導致 `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage)實例推送至導覽堆疊上，而它會成為應用程式的使用中頁面和根頁面。 如下列螢幕擷取畫面所示：

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) 執行個體的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 屬性可讓您存取導覽堆疊中的第一頁。

### <a name="pushing-pages-to-the-navigation-stack"></a>將頁面推送到導覽堆疊

若要巡覽至 `Page2Xaml`，必須在目前頁面的 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 屬性上叫用 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 方法，如下列程式碼範例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

這會使 `Page2Xaml` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 如下列螢幕擷取畫面所示：

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

叫用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法時，會發生下列事件：

- 呼叫 `PushAsync` 的頁面叫用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 覆寫。
- 要巡覽的頁面叫用其 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫。
- `PushAsync` 工作完成。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 一書的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

> [!NOTE]
> 呼叫 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 和 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫無法保證一定是指頁面導覽。 例如，在 iOS 上，當應用程式終止時，會在使用中頁面上呼叫 `OnDisappearing` 覆寫。

### <a name="popping-pages-from-the-navigation-stack"></a>從導覽堆疊中快顯頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。

若要以程式設計的方式返回原始頁面，`Page2Xaml` 執行個體必須叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下列程式碼範例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

這會使 `Page2Xaml` 執行個體從導覽堆疊中移除，進而使新的最上層頁面變成使用中的頁面。 叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法時，會發生下列事件：

- 呼叫 `PopAsync` 的頁面叫用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 覆寫。
- 要返回的頁面叫用其 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫。
- 傳回 `PopAsync` 工作。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 一書的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

除了 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 和 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，每頁的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性還會提供 [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) 方法，如下列程式碼範例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

此方法會從導覽堆疊中快顯根 [`Page`](xref:Xamarin.Forms.Page) 以外的所有頁面，因此讓應用程式的根頁面成為使用中頁面。

### <a name="animating-page-transitions"></a>以動畫顯示頁面轉換

每頁的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性還會提供覆寫的 push 和 pop 方法，其中包含控制是否在巡覽期間顯示頁面動畫的 `boolean` 參數，如下列程式碼範例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

只要基礎平台支援，將 `boolean` 參數設定為 `false` 會停用頁面轉換動畫，將此參數設定為 `true` 會啟用頁面轉換動畫。 不過，缺少此參數的 push 和 pop 方法預設會啟用動畫。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>巡覽時傳遞資料

有時必須在巡覽期間將某頁資料傳遞到另一頁。 有兩種方法可以完成此作業：透過頁面建構函式來傳遞資料，以及透過設定新頁面與資料的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)。 現在將依序討論各種方法。

### <a name="passing-data-through-a-page-constructor"></a>透過頁面建構函式傳遞資料

在巡覽期間將資料傳遞到另一頁的最簡單方法，就是透過頁面建構函式參數，如下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼會建立 `MainPage` 執行個體，並以 ISO8601 格式傳入目前的日期和時間，這會包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體中。

`MainPage` 執行個體會透過建構函式參數接收資料，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

接著可設定 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性在頁面上顯示資料，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>透過 BindingContext 傳遞資料

在巡覽期間將資料傳遞到另一頁的另一個方法，就是設定新頁面與資料的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，如下列程式碼範例所示：

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

此程式碼會將 [ 執行個體的 `BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)`SecondPage` 設定為 `Contact` 執行個體，然後巡覽至 `SecondPage`。

`SecondPage` 會接著使用資料繫結來顯示 `Contact` 執行個體資料，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下列程式碼範例示範如何在 C# 中完成資料繫結：

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

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
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

接著可透過一連串的 [`Label`](xref:Xamarin.Forms.Label) 控制項在頁面上顯示資料，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作導覽堆疊

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性會公開可以從中取得導覽堆疊中頁面的 [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) 屬性。 Xamarin.Forms 會維持導覽堆疊的存取，而 `Navigation` 屬性則提供 [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 和 [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) 方法，透過插入或移除頁面來操作堆疊。

[`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 方法會將指定的頁面插入導覽堆疊中現有指定頁面之前，如下圖所示：

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

[`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) 方法會從導覽堆疊中移除指定的頁面，如下圖所示：

![](hierarchical-images/remove-page.png "Removing a Page from the Navigation Stack")

這些方法會啟用自訂導覽體驗，例如在成功登入之後，以新頁面取代登入頁面。 下列程式碼範例將示範此情節：

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

若使用者的認證正確，`MainPage` 執行個體會插入導覽堆疊中目前頁面之前。 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法會接著從導覽堆疊中移除目前頁面，進而使 `MainPage` 執行個體變成使用中頁面。

## <a name="displaying-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

所有 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 都會顯示在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 的導覽列中。 這是透過將 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性設定為 `View` 來完成。 此附加屬性可以在任何 [`Page`](xref:Xamarin.Forms.Page) 上設定，而且當 `Page` 推送到 `NavigationPage` 時，`NavigationPage` 會採用該屬性的值。

下列範例擷取自[標題檢視範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)，示範如何從 XAML 設定 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

以下是對等的 C# 程式碼：

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

這會導致在 [`Slider`](xref:Xamarin.Forms.Slider) 的導覽列中顯示 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)：

[![滑杆 TitleView](hierarchical-images/titleview-small.png "滑杆 TitleView")](hierarchical-images/titleview-large.png#lightbox "滑杆 TitleView")

> [!IMPORTANT]
> 除非使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性指定檢視的大小，否則許多檢視不會出現在導覽列中。 或者，您可以將檢視包裝在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中，並將 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定為適當的值。

請注意，由於 [`Layout`](xref:Xamarin.Forms.Layout) 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別，因此可設定 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性，顯示包含多個檢視的配置類別。 在 iOS 和通用 Windows 平台 (UWP) 上，無法變更導覽列的高度，因此如果導覽列中所顯示檢視大於導覽列的預設大小，則會發生裁剪。 不過，在 Android 上，則可以變更導覽列的高度，方法是將 [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) 可繫結屬性設定為表示新高度的 `double`。 如需詳細資訊，請參閱[設定 NavigationPage 上的導覽列高度](~/xamarin-forms/platform/android/navigationpage-bar-height.md)。

或者，您可以將一些內容放在導覽列中，並將一些內容放在色彩符合導覽列的頁面內容頂端檢視中，來建議擴充導覽列。 此外，在 iOS 上，您可以將 [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) 可繫結屬性設定為 `true`，來移除導覽列底部的分隔線和陰影。 如需詳細資訊，請參閱[隱藏 NavigationPage 上的導覽列分隔線](~/xamarin-forms/platform/ios/navigation-bar-separator.md)。

> [!NOTE]
> [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)、[`Title`](xref:Xamarin.Forms.Page.Title)、[`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) 和 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 屬性全都能定義佔用導覽列上空間的值。 由於導覽列大小會依平台和螢幕大小而有所不同，因此設定上述所有屬性將會由於可用空間有限而導致衝突。 與其嘗試使用這些屬性的組合，您可能會發現單獨設定 `TitleView` 屬性更容易取得您想要的導覽列設計。

### <a name="limitations"></a>限制

在 [`View`](xref:Xamarin.Forms.View) 的導覽列中顯示 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 時有一些要注意的限制：

- 在 iOS 上，放在 `NavigationPage` 導覽列中的檢視會根據是否啟用大型標題，出現在不同的位置。 如需啟用大型標題的詳細資訊，請參閱[顯示大型標題](~/xamarin-forms/platform/ios/page-large-title.md)。
- 在 Android 上，只有在使用 app-compat 的應用程式中，才能成功將檢視放在 `NavigationPage` 的導覽列中。
- 不建議將大型且複雜的檢視 (例如 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`TableView`](xref:Xamarin.Forms.TableView)) 放在 `NavigationPage` 的導覽列中。

## <a name="related-links"></a>相關連結

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) (頁面導覽)
- [Hierarchical Navigation](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical) (階層式導覽 (範例))
- [Passing Data (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (傳遞資料 (範例))
- [LoginFlow (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [Title View (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview) (標題檢視 (範例))
- [如何在 Xamarin.Forms 中建立登入畫面流程影片](https://www.youtube.com/watch?v=qKQ7pyyG1fo) \(英文\)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
