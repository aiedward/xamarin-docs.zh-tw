---
title: 階層式導覽
description: 本文示範如何使用 NavigationPage 類別，在一堆後進先出 (LIFO) 的頁面中執行瀏覽。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9f72541bddcadabe4ce4fa71c2a4dfcae3c3c898
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374975"
---
# <a name="hierarchical-navigation"></a>階層式導覽

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_NavigationPage 類別提供階層式導覽體驗，讓使用者能夠視需要，向前及向後流覽頁面。類別會以後進先出的方式將導覽實作為頁面物件) 堆疊的後進先出 (。本文示範如何使用 NavigationPage 類別，在一堆頁面中執行導覽。_

若要從一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中頁面，如下圖所示：

![將頁面推送到導覽堆疊](hierarchical-images/pushing.png)

若要返回上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中頁面，如下圖所示：

![從導覽堆疊中快顯頁面](hierarchical-images/popping.png)

所有衍生型別上的屬性都會公開流覽方法 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) [`Page`](xref:Xamarin.Forms.Page) 。 這些方法可讓您將頁面推送到導覽堆疊上、從導覽堆疊中快顯頁面，以及執行堆疊操作。

## <a name="performing-navigation"></a>執行導覽

在階層式導覽中， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別是用來流覽物件的堆疊 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 下列螢幕擷取畫面顯示每個平台上 `NavigationPage` 的主要元件：

![NavigationPage 元件](hierarchical-images/navigationpage-components.png)

的版面配置 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 相依于平臺：

- 在 iOS 上，頁面頂端有一個導覽列，其中顯示標題，並具有可返回上一頁的 [上一頁] 按鈕。
- 在 Android 上，頁面頂端有一個導覽列，其中顯示標題、圖示，以及可返回上一頁的 [上一頁] 按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在通用 Windows 平台上，頁面頂端有一個顯示標題的導覽列。

在所有平臺上，屬性的值 [`Page.Title`](xref:Xamarin.Forms.Page.Title) 會顯示為頁面標題。 此外，您 `IconColor` 也可以將屬性設定為，以套用 [`Color`](xref:Xamarin.Forms.Color) 至巡覽列中的圖示。

> [!NOTE]
> 建議僅以 `ContentPage` 執行個體填入 `NavigationPage`。

### <a name="creating-the-root-page"></a>建立根頁面

加入至導覽堆疊中的第一頁指的是應用程式的 *根* 頁面，下列程式碼範例會示範如何完成此作業：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

這會使 `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體推送至導覽堆疊上，而變成使用中頁面及應用程式的根頁面。 如下列螢幕擷取畫面所示：

![導覽堆疊的根頁面](hierarchical-images/mainpage.png)

> [!NOTE]
> [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage)實例的屬性 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 可讓您存取導覽堆疊中的第一頁。

### <a name="pushing-pages-to-the-navigation-stack"></a>將頁面推送到導覽堆疊

若要流覽至 `Page2Xaml` ，必須 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 在 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 目前頁面的屬性上叫用方法，如下列程式碼範例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

這會使 `Page2Xaml` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 如下列螢幕擷取畫面所示：

![推送到導覽堆疊上的頁面](hierarchical-images/secondpage.png)

叫用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法時，會發生下列事件：

- 呼叫的頁面會叫用它的覆 `PushAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 寫。
- 正在流覽的頁面已叫用其覆 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 寫。
- `PushAsync` 工作完成。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 本書的第 [24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Xamarin.Forms 。

> [!NOTE]
> [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)和 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫的呼叫無法被視為頁面導覽的保證指標。 例如，在 iOS 上，當應用程式終止時，會在使用中頁面上呼叫 `OnDisappearing` 覆寫。

### <a name="popping-pages-from-the-navigation-stack"></a>從導覽堆疊中快顯頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。

若要以程式設計的方式返回原始頁面， `Page2Xaml` 實例必須 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 叫用方法，如下列程式碼範例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

這會使 `Page2Xaml` 執行個體從導覽堆疊中移除，進而使新的最上層頁面變成使用中的頁面。 叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法時，會發生下列事件：

- 呼叫的頁面會叫用它的覆 `PopAsync` [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 寫。
- 要傳回的頁面，其覆寫已叫用 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 。
- 傳回 `PopAsync` 工作。

不過，發生這些事件的確切順序則取決於平台。 如需詳細資訊，請參閱 Charles Petzold 本書的第 [24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Xamarin.Forms 。

除了 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 和 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 之外，每個頁面的屬性也 [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) 會提供方法，如下列程式碼範例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

這個方法會在流覽堆疊之外取出所有的根目錄 [`Page`](xref:Xamarin.Forms.Page) ，因此會讓應用程式的根頁面成為使用中的頁面。

### <a name="animating-page-transitions"></a>以動畫顯示頁面轉換

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)每個頁面的屬性也會提供覆寫的 push 和 pop 方法，其中包含 `boolean` 控制是否要在導覽期間顯示頁面動畫的參數，如下列程式碼範例所示：

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

## <a name="passing-data-when-navigating"></a>巡覽時傳遞資料

有時必須在巡覽期間將某頁資料傳遞到另一頁。 完成這項工作的兩種技巧是透過頁面的函式來傳遞資料，以及將新頁面設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 為數據。 現在將依序討論各種方法。

### <a name="passing-data-through-a-page-constructor"></a>透過頁面建構函式傳遞資料

在巡覽期間將資料傳遞到另一頁的最簡單方法，就是透過頁面建構函式參數，如下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此 `MainPage` 程式碼會建立實例，並以 ISO8601 格式傳入目前的日期和時間，此格式會包裝在實例中 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。

`MainPage` 執行個體會透過建構函式參數接收資料，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

然後藉由設定屬性，在頁面上顯示資料 [`Label.Text`](xref:Xamarin.Forms.Label.Text) ，如下列螢幕擷取畫面所示：

![透過頁面建構函式傳遞的資料](hierarchical-images/passing-data-constructor.png)

### <a name="passing-data-through-a-bindingcontext"></a>透過 BindingContext 傳遞資料

在流覽期間將資料傳遞至另一個頁面的替代方法是將新頁面設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 為數據，如下列程式碼範例所示：

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

這段程式碼會將實例的設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `SecondPage` 為 `Contact` 實例，然後流覽至 `SecondPage` 。

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

然後，資料會由一系列控制項顯示在頁面上 [`Label`](xref:Xamarin.Forms.Label) ，如下列螢幕擷取畫面所示：

![透過 BindingContext 傳遞的資料](hierarchical-images/passing-data-bindingcontext.png)

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="manipulating-the-navigation-stack"></a>操作導覽堆疊

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)屬性 [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) 會公開可以從中取得導覽堆疊中頁面的屬性。 雖然 Xamarin.Forms 維持對流覽堆疊的存取權，但 `Navigation` 屬性會提供 [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 和方法，藉 [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) 由插入頁面或移除它們來操作堆疊。

方法會將 [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) 指定的頁面插入導覽堆疊中的現有指定頁面之前，如下圖所示：

![將頁面插入導覽堆疊](hierarchical-images/insert-page-before.png)

[`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*)方法會從導覽堆疊中移除指定的頁面，如下圖所示：

![從導覽堆疊中移除頁面](hierarchical-images/remove-page.png)

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

若使用者的認證正確，`MainPage` 執行個體會插入導覽堆疊中目前頁面之前。 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync)然後，方法會從導覽堆疊中移除目前的頁面，並將實例變成使用中的 `MainPage` 頁面。

## <a name="displaying-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

任何 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 可以顯示在的巡覽列中 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 將 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性設定為，即可完成此作業 `View` 。 此附加屬性可以在任何上設定 [`Page`](xref:Xamarin.Forms.Page) ，當 `Page` 推送至時 `NavigationPage` ， `NavigationPage` 將會遵守屬性的值。

下列範例擷取自[標題檢視範例](/samples/xamarin/xamarin-forms-samples/navigation-titleview)，示範如何從 XAML 設定 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性：

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

這會導致在的 [`Slider`](xref:Xamarin.Forms.Slider) 巡覽列中顯示 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ：

[![滑杆 TitleView](hierarchical-images/titleview-small.png "滑杆 TitleView")](hierarchical-images/titleview-large.png#lightbox "滑杆 TitleView")

> [!IMPORTANT]
> 除非使用和屬性指定了視圖的大小，否則許多視圖都不會出現在巡覽列中 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 。 或者，您可以將視圖包裝在中， [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 並將和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定為適當的值。

請注意，由於 [`Layout`](xref:Xamarin.Forms.Layout) 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別，因此 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 可以將附加屬性設定為顯示包含多個視圖的版面配置類別。 在 iOS 和通用 Windows 平台 (UWP) 上，無法變更導覽列的高度，因此如果導覽列中所顯示檢視大於導覽列的預設大小，則會發生裁剪。 不過，在 Android 上，您可以藉由將可系結屬性設定為，來變更導覽列的高度， [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) 以 `double` 代表新的高度。 如需詳細資訊，請參閱[設定 NavigationPage 上的導覽列高度](~/xamarin-forms/platform/android/navigationpage-bar-height.md)。

或者，您可以將一些內容放在導覽列中，並將一些內容放在色彩符合導覽列的頁面內容頂端檢視中，來建議擴充導覽列。 此外，在 iOS 上，您可以藉由將可系結屬性設定為，來移除位於巡覽列底部的分隔線和陰影 [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) `true` 。 如需詳細資訊，請參閱[隱藏 NavigationPage 上的導覽列分隔線](~/xamarin-forms/platform/ios/navigation-bar-separator.md)。

> [!NOTE]
> [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)、、 [`Title`](xref:Xamarin.Forms.Page.Title) [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) 和 [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 屬性都可以定義佔用巡覽列空間的值。 由於導覽列大小會依平台和螢幕大小而有所不同，因此設定上述所有屬性將會由於可用空間有限而導致衝突。 與其嘗試使用這些屬性的組合，您可能會發現單獨設定 `TitleView` 屬性更容易取得您想要的導覽列設計。

### <a name="limitations"></a>限制

在的導覽列中顯示時，有一些要注意的限制 [`View`](xref:Xamarin.Forms.View) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ：

- 在 iOS 上，放在 `NavigationPage` 導覽列中的檢視會根據是否啟用大型標題，出現在不同的位置。 如需啟用大型標題的詳細資訊，請參閱[顯示大型標題](~/xamarin-forms/platform/ios/page-large-title.md)。
- 在 Android 上，只有在使用 app-compat 的應用程式中，才能成功將檢視放在 `NavigationPage` 的導覽列中。
- 不建議將大型和複雜的視圖（例如 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`TableView`](xref:Xamarin.Forms.TableView) ）放在的巡覽列中 `NavigationPage` 。

## <a name="related-links"></a>相關連結

- [頁面流覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical Navigation](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical) (階層式導覽 (範例))
- [Passing Data (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (傳遞資料 (範例))
- [LoginFlow (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [Title View (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-titleview) (標題檢視 (範例))
- [如何在影片中建立登入畫面流程 Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)