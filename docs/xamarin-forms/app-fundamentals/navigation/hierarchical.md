---
title: 階層式導覽
description: 這篇文章會示範如何使用 NavigationPage 類別來執行導覽一疊後進先出 (LIFO) 頁面中。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: f8f8f9b4e5755e8b1707178fef633321b64e4e94
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994672"
---
# <a name="hierarchical-navigation"></a>階層式導覽

_NavigationPage 類別會提供使用者所在能向前及向後，視需要執行頁面，瀏覽的階層式導覽體驗。此類別會實作後進先出 (LIFO) 堆疊的頁面物件導覽。這篇文章會示範如何使用 NavigationPage 類別來執行導覽一疊頁面中。_

這篇文章討論下列主題：

- [執行瀏覽](#Performing_Navigation)– 建立根頁面、 將頁面推送至導覽堆疊、 拉出頁面從導覽堆疊上，以及以動畫顯示轉換頁面。
- [瀏覽時，將資料傳遞](#Passing_Data_when_Navigating)– 傳遞資料透過頁面的建構函式，以及透過`BindingContext`。
- [操作在巡覽堆疊](#Manipulating_the_Navigation_Stack)– 管理堆疊插入或移除頁面。

## <a name="overview"></a>總覽

若要從一頁移到另一個，應用程式會推送到導覽堆疊上，新的頁面，它會變成作用中的頁面上，如下圖所示：

![](hierarchical-images/pushing.png "將頁面推送至導覽堆疊")

若要傳回至上一頁，應用程式會快顯目前的頁面，從導覽堆疊上，以及新的最上層頁面會變成作用中的頁面上，如下圖所示：

![](hierarchical-images/popping.png "快顯的頁面從導覽堆疊")

瀏覽方法會藉由公開[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性上任何[ `Page` ](xref:Xamarin.Forms.Page)衍生型別。 這些方法可讓您將頁面到導覽堆疊上，推送到導覽堆疊上，從 pop 的頁面，以及執行堆疊操作。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行導覽

在階層式導覽中，[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別用來導覽一疊 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。 下列螢幕擷取畫面顯示的主要元件`NavigationPage`每個平台：

![](hierarchical-images/navigationpage-components.png "NavigationPage 元件")

版面配置[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)取決於平台：

- 在 iOS 上，瀏覽列會出現在頁面頂端的顯示標題，且具有*回*回到前一頁的按鈕。
- 在 Android 上，瀏覽列會出現在頁面頂端的顯示標題、 圖示和*回*回到前一頁的按鈕。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。
- 通用 Windows 平台上，瀏覽列則會出現在頁面頂端的顯示標題項目。

所有平台上的值[ `Page.Title` ](xref:Xamarin.Forms.Page.Title)屬性將會顯示為頁面標題。

> [!NOTE]
> 建議`NavigationPage`應該填入`ContentPage`只執行個體。

### <a name="creating-the-root-page"></a>建立根頁面

加入至導覽堆疊中的第一頁指的是應用程式的*根*頁面，下列程式碼範例會示範如何完成此作業：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

這會導致`Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)推送到導覽堆疊上，而變成使用中的頁面和 [根] 頁面的應用程式的執行個體。 這是由下列的螢幕擷取畫面所示：

![](hierarchical-images/mainpage.png "根頁面的巡覽堆疊")

> [!NOTE]
> [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage)屬性[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體可存取在巡覽堆疊中的第一頁。

### <a name="pushing-pages-to-the-navigation-stack"></a>將頁面推送至導覽堆疊

若要瀏覽至`Page2Xaml`，就必須叫用[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性目前的頁面上，如下列程式碼範例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

這會使 `Page2Xaml` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 這是由下列的螢幕擷取畫面所示：

![](hierarchical-images/secondpage.png "頁面推送至導覽堆疊")

當[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)叫用方法時，會發生下列事件：

- 頁面上呼叫`PushAsync`有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)叫用的覆寫。
- 巡覽至頁面具有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)叫用的覆寫。
- `PushAsync`工作完成。

不過，發生這些事件的確切順序取決於平台。 如需詳細資訊，請參閱 <<c0> [ 第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

> [!NOTE]
> 呼叫[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)並[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)覆寫無法被視為保證找出的頁面巡覽的跡象。 例如，在 iOS 上，`OnDisappearing`應用程式終止時，將會呼叫使用中頁面的覆寫。

### <a name="popping-pages-from-the-navigation-stack"></a>從導覽堆疊彈出頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。

若要以程式設計的方式返回原始頁面，`Page2Xaml` 執行個體必須叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下列程式碼範例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

這會使 `Page2Xaml` 執行個體從導覽堆疊中移除，進而使新的最上層頁面變成使用中的頁面。 當[ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)叫用方法時，會發生下列事件：

- 頁面上呼叫`PopAsync`有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)叫用的覆寫。
- 所傳回的頁面具有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)叫用的覆寫。
- `PopAsync`工作傳回。

不過，發生這些事件的確切順序取決於平台。 如需詳細資訊，請參閱[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

以及[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)並[ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性的每一頁也會提供[ `PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync)方法，以下列程式碼範例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

此方法會顯示根目錄以外的所有[ `Page` ](xref:Xamarin.Forms.Page)關閉導覽堆疊上，因此進行應用程式的使用中頁面的 [根] 頁面。

### <a name="animating-page-transitions"></a>以動畫顯示轉換頁面

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)每個頁面的屬性也會提供覆寫的 push 和 pop 方法，包括`boolean`控制是否要顯示的頁面動畫期間瀏覽，如下列程式碼所示的參數範例：

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

設定`boolean`參數來`false`會停用時將參數設定的頁面轉換動畫`true`前提是它支援基礎平台，可讓頁面轉換動畫。 不過，缺少此參數的 push 和 pop 方法會依預設啟用動畫。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>瀏覽時將資料傳遞

有時是必要的頁面，即可將資料傳遞至另一個頁面中，在巡覽期間。 兩個技術可完成這項作業會將資料透過頁面的建構函式，並藉由設定新的頁面傳遞[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)資料。 每個會現在會依次討論。

### <a name="passing-data-through-a-page-constructor"></a>將資料傳遞到網頁的建構函式

將資料傳遞至另一個頁面中，在巡覽期間的最簡單技巧是透過頁面的建構函式參數中的下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼會建立`MainPage`執行個體，傳入目前的日期和時間 ISO8601 格式，其中會包裝在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體。

`MainPage`的執行個體收到的資料，透過建構函式參數，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

資料隨即顯示在頁面上設定[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)屬性，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-constructor.png "透過頁面的建構函式傳遞的資料")

### <a name="passing-data-through-a-bindingcontext"></a>將資料傳遞到 BindingContext

將資料傳遞至另一個頁面中，在巡覽期間的替代方式是藉由設定新的頁面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)資料，如下列程式碼範例所示：

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

此程式碼會設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`SecondPage`執行個體`Contact`執行個體，並瀏覽至`SecondPage`。

`SecondPage`接著會使用資料繫結顯示`Contact`執行個體資料，如下列 XAML 程式碼範例所示：

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

下列程式碼範例示範如何完成資料繫結，在 C# 中：

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

資料隨即顯示在頁面的一系列[ `Label` ](xref:Xamarin.Forms.Label)控制項，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-bindingcontext.png "透過 BindingContext 傳遞資料")

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作在巡覽堆疊

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性會公開[ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack)屬性可以從中取得導覽堆疊中的頁面。 Xamarin.Forms 會維護存取導覽堆疊上，而`Navigation`屬性會提供[ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*)並[ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*)方法，以插入操作堆疊頁面或移除它們。

[ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*)方法會插入之前現有的指定頁面上，在巡覽堆疊中指定的頁面如下圖所示：

![](hierarchical-images/insert-page-before.png "在巡覽堆疊中插入頁面")

[ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*)方法會移除指定的頁面從導覽堆疊上，如下圖所示：

![](hierarchical-images/remove-page.png "從導覽堆疊中移除頁面")

這些方法可讓自訂的瀏覽體驗，例如登入頁面取代為新的頁面上，遵循成功的登入。 下列程式碼範例示範此案例：

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

前提是使用者的認證正確無誤，`MainPage`執行個體插入至導覽堆疊上目前頁面的上一頁。 [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)方法然後移除目前的頁面上，從導覽堆疊上，具有`MainPage`成為使用中頁面的執行個體。

## <a name="summary"></a>總結

這篇文章示範如何使用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行導覽一疊頁面中的類別。 這個類別會提供使用者所在能向前及向後，視需要執行頁面，瀏覽的階層式導覽體驗。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](xref:Xamarin.Forms.Page) 物件導覽。


## <a name="related-links"></a>相關連結

- [頁面巡覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [階層 （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [如何在 Xamarin.Forms （Xamarin University 影片） 範例中的畫面 Flow 中建立一個符號](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [如何在 Xamarin.Forms （Xamarin University 影片） 中的畫面 Flow 中建立一個符號](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
