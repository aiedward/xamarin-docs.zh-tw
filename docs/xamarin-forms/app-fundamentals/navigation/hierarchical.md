---
title: 階層式導覽
description: NavigationPage 類別會提供使用者所在的能向前及向後，視需要執行頁面，瀏覽階層式導覽體驗。 類別會實作瀏覽後進先出 (LIFO) 堆疊的頁面物件。 本文示範如何使用 NavigationPage 類別來執行瀏覽的頁面堆疊中。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 49be5b812c5ef66907e7ebf74906c25bfbb076b6
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847159"
---
# <a name="hierarchical-navigation"></a>階層式導覽

_NavigationPage 類別會提供使用者所在的能向前及向後，視需要執行頁面，瀏覽階層式導覽體驗。類別會實作瀏覽後進先出 (LIFO) 堆疊的頁面物件。本文示範如何使用 NavigationPage 類別來執行瀏覽的頁面堆疊中。_

本文將討論下列主題：

- [執行巡覽](#Performing_Navigation)– 建立根頁面、 將頁面推送到巡覽堆疊、 取出巡覽堆疊中，從頁面和動畫畫面轉換。
- [瀏覽時，將資料傳遞](#Passing_Data_when_Navigating)– 傳遞資料透過頁面的建構函式，以及透過`BindingContext`。
- [操作在巡覽堆疊](#Manipulating_the_Navigation_Stack)– 藉由插入或移除頁面管理堆疊。

## <a name="overview"></a>總覽

從一頁移到另一個，應用程式將推送至巡覽堆疊中，新的頁面位置就會變成作用中的頁面上，如下列圖表所示：

![](hierarchical-images/pushing.png "將頁面推送到巡覽堆疊")

若要傳回至前一頁，應用程式就會出現在巡覽堆疊中，目前的網頁和新的最上層頁面會變成作用中的頁面上，如下列圖表所示：

![](hierarchical-images/popping.png "取出巡覽這個頁面的頁面")

所公開的巡覽方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)上任何屬性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)衍生型別。 這些方法提供推送 pop 頁面巡覽堆疊中，從網頁瀏覽堆疊，並執行堆疊操作的能力。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>執行巡覽

在階層式導覽中，[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 類別用來導覽一疊 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 物件。 下列螢幕擷取畫面顯示的主要元件`NavigationPage`上每個平台：

![](hierarchical-images/navigationpage-components.png "NavigationPage 元件")

配置[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)取決於平台：

- 在 iOS 上，出現在頁面會顯示標題，並具有的最上方導覽列就*回*回到前一頁的按鈕。
- 在 Android 上，導覽列是出現在頂端顯示標題、 圖示、 頁面和*回*回到前一頁的按鈕。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。
- 通用 Windows 平台上，導覽列是出現在頁面頂端的顯示標題。 

所有平台上，值[ `Page.Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)屬性將會顯示為頁面標題。

> [!NOTE]
> 建議`NavigationPage`應該填入`ContentPage`僅限執行個體。

### <a name="creating-the-root-page"></a>在建立根頁面

加入至導覽堆疊中的第一頁指的是應用程式的*根*頁面，下列程式碼範例會示範如何完成此作業：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

這會導致`Page1Xaml` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)推入巡覽堆疊，此時報表便成為使用中的頁面和應用程式的根頁面的執行個體。 下列螢幕擷取畫面所示：

![](hierarchical-images/mainpage.png "根頁面的巡覽堆疊")

> [!NOTE]
> [ `RootPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.RootPage/)屬性[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體會提供存取權的巡覽堆疊中的第一頁。

### <a name="pushing-pages-to-the-navigation-stack"></a>將頁面推送到巡覽堆疊

若要瀏覽至`Page2Xaml`，就必須叫用[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性目前的頁面上，為下列程式碼範例示範：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

這會使 `Page2Xaml` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 下列螢幕擷取畫面所示：

![](hierarchical-images/secondpage.png "在巡覽堆疊推入頁面")

當[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)叫用方法時，發生下列事件：

- 頁面呼叫`PushAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)叫用的覆寫。
- 正要巡覽頁面具有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)叫用的覆寫。
- `PushAsync`工作完成。

不過，這些事件會發生的順序取決於平台。 如需詳細資訊，請參閱[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 活頁簿。

> [!NOTE]
> 呼叫[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)和[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)覆寫無法被視為錯誤的頁面巡覽的保證指示。 例如，在 iOS、`OnDisappearing`應用程式終止時，覆寫在使用中的頁面上呼叫。

### <a name="popping-pages-from-the-navigation-stack"></a>在巡覽堆疊中的雜訊頁面

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。

若要以程式設計的方式返回原始頁面，`Page2Xaml` 執行個體必須叫用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如下列程式碼範例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

這會使 `Page2Xaml` 執行個體從導覽堆疊中移除，進而使新的最上層頁面變成使用中的頁面。 當[ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)叫用方法時，發生下列事件：

- 頁面呼叫`PopAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)叫用的覆寫。
- 傳回此頁面具有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)叫用的覆寫。
- `PopAsync`工作傳回。

不過，這些事件會發生的順序取決於平台。 如需詳細資訊，請參閱[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 活頁簿。

以及[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)和[ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性時，每個頁面也提供[ `PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopToRootAsync()/)方法，下列程式碼範例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

這個方法會將根目錄以外的所有[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)導覽堆疊，因此讓應用程式使用中頁面的 [根] 頁面。

### <a name="animating-page-transitions"></a>動畫畫面轉換

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性時，每個頁面也提供覆寫的 push 和 pop 方法，包含了`boolean`控制是否顯示頁面動畫期間瀏覽，如下列程式碼所示的參數範例：

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

設定`boolean`參數`false`停用時將參數設定為相同的動畫網頁切換`true`假設基礎平台支援，可讓網頁轉換動畫。 不過，缺少此參數的 push 和 pop 方法會預設啟用動畫。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>瀏覽時傳遞資料

有時候，很有必要的頁面巡覽期間，將資料傳遞至另一個頁面。 完成這兩項技術會傳遞資料透過頁面的建構函式，並藉由設定新的頁面[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)資料。 每個將現在會依次討論。

### <a name="passing-data-through-a-page-constructor"></a>將資料傳遞到頁面的建構函式

在巡覽期間，將資料傳遞至另一個頁面的最簡單技巧是透過頁面建構函式參數，而是以下列程式碼範例所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此程式碼建立`MainPage`執行個體，將目前的日期和時間的 ISO8601 格式中，這會包裝在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體。

`MainPage`的執行個體收到的資料，透過建構函式參數，如下列程式碼範例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

資料隨即顯示在頁面設定[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-constructor.png "透過頁面的建構函式傳遞資料")

### <a name="passing-data-through-a-bindingcontext"></a>透過其他的傳遞資料

將資料傳遞至另一個頁面中，巡覽期間的替代方式是藉由設定新的頁面[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)資料，如下列程式碼範例所示：

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

這個程式碼設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的`SecondPage`執行個體`Contact`執行個體，並瀏覽至`SecondPage`。

`SecondPage`然後使用資料繫結來顯示`Contact`執行個體資料，如下列 XAML 程式碼範例所示：

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

資料隨即顯示在頁面的一系列的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項，如下列螢幕擷取畫面所示：

![](hierarchical-images/passing-data-bindingcontext.png "傳遞到其他資料")

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作在巡覽堆疊

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性會公開[ `NavigationStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/)可以從中取得的頁面巡覽堆疊中的屬性。 雖然 Xamarin.Forms 維護巡覽堆疊，存取`Navigation`屬性提供[ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/)和[ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/)管理堆疊所插入的方法頁面，或移除它們。

[ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/)方法會插入之前現有的指定頁面上，在巡覽堆疊中指定的頁面如下圖所示：

![](hierarchical-images/insert-page-before.png "在巡覽堆疊中插入頁面")

[ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/)方法會移除指定的頁面巡覽堆疊，如下列圖表所示：

![](hierarchical-images/remove-page.png "在巡覽堆疊中移除頁面")

這些方法可讓自訂瀏覽經驗，例如取代新的頁面上，下列登入成功的登入頁面。 下列程式碼範例示範此案例：

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

前提是使用者的認證正確無誤，`MainPage`執行個體插入至目前的頁面之前瀏覽堆疊。 [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)方法然後移除目前的頁面巡覽堆疊中，從具有`MainPage`成為使用中頁面的執行個體。

## <a name="summary"></a>總結

本文示範如何使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行瀏覽的頁面堆疊中的類別。 這個類別會提供使用者所在的能向前及向後，視需要執行頁面，瀏覽的階層式導覽體驗。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 物件導覽。


## <a name="related-links"></a>相關連結

- [頁面巡覽](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [階層 （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [如何建立 Xamarin.Forms （Xamarin 大學影片） 範例中的螢幕流程中的正負號](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [如何建立 Xamarin.Forms （Xamarin 大學影片） 中的螢幕流程中的正負號](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)
