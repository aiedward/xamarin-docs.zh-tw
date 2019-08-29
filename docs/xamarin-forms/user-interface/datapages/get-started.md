---
title: Getting Started with DataPages
description: 這篇文章說明如何開始建置使用 Xamarin.Forms DataPages 的簡單資料驅動頁面。
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e6f26fb96c0a538543c8e0b0574461ea99709631
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119481"
---
# <a name="getting-started-with-datapages"></a>Getting Started with DataPages

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "此 API 目前為預覽狀態")

> [!IMPORTANT]
> DataPages 需要有 Xamarin 主題參考才能呈現。 這牽涉到將 [Xamarin.Forms.Theme.Base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)nuget 套件安裝到您的專案中, 後面接著 [Xamarin.Forms.Theme.Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) 或 [Xamarin.Forms.Theme.Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)。

若要開始建置使用 DataPages 預覽的簡單資料驅動頁面，請遵循下列步驟。 在預覽中的硬式編碼樣式 （「 事件 」） 建置的這個示範會使用僅適用於特定程式碼中的 JSON 格式。

[![](get-started-images/demo-sml.png "DataPages 範例應用程式")](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

## <a name="1-add-nuget-packages"></a>1.新增 NuGet 封裝

將這些 Nuget 套件新增至 Xamarin.Forms.NET Standard 程式庫和應用程式專案中：

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- 佈景主題的實作 （例如 Nuget Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2.新增佈景主題參考

在  **App.xaml**檔案中，新增自訂`xmlns:mytheme`佈景主題，並確定 佈景主題已合併到應用程式的資源字典：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> 您也應該遵循步驟來[載入主題元件 (如下)](#loadtheme) , 方法是將一些未定案的程式`AppDelegate`代碼新增`MainActivity`至 iOS 和 Android。 這會改善未來的預覽版。


## <a name="3-add-a-xaml-page"></a>3.新增 XAML 頁面

將新的 XAML 頁面新增至 Xamarin.Forms 應用程式，並*基底類別變更*從`ContentPage`至`Xamarin.Forms.Pages.ListDataPage`。 此值必須在 C# 和 XAML 中完成：

**C# 檔案**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**XAML 檔案**

除了變更至根項目`<p:ListDataPage>`的自訂命名空間`xmlns:p`也必須加入：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**應用程式子類別**

變更`App`類別建構函式，讓`MainPage`設為`NavigationPage`包含新`SessionDataPage`。 導覽頁*必須*使用。

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3.新增資料來源

刪除`Content`項目並將它取代為`p:ListDataPage.DataSource`來填入資料的頁面。 在下面的遠端的 Json 範例資料檔案從 URL 載入。

> [!NOTE]
> 預覽*需要* `StyleClass`屬性來提供資料來源的呈現提示。 `StyleClass="Events"`預先定義在預覽中，並包含樣式的配置是指*硬式編碼*以符合所使用的 JSON 資料來源。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**JSON 資料**

從 JSON 資料的範例[示範來源](http://demo3143189.mockable.io/sessions)如下所示：

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4.執行 ！

上述的步驟應該會導致工作資料頁面：

[![](get-started-images/demo-sml.png "DataPages 範例應用程式")](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

這是因為預先建置的樣式 **「 事件 」** Light 佈景主題的 Nuget 套件中存在，而且有定義的樣式，將資料來源 （例如比對。 "title"、"image"、"主持人 」）。

「 事件 」`StyleClass`建置用來顯示`ListDataPage`自訂控制項`CardView`控制項中所定義的 Xamarin.Forms.Pages。 `CardView`控制項有三個屬性： `ImageSource`， `Text`，和`Detail`。 佈景主題是硬式編碼繫結資料來源的三個欄位 （來自 JSON 檔案中） 來顯示這些屬性。

## <a name="5-customize"></a>5.自訂

可以覆寫繼承的樣式，藉由指定範本，並使用資料來源繫結。 下列 XAML 會宣告每個資料列，使用新的自訂範本`ListItemControl`並`{p:DataSourceBinding}`中所含的語法**Xamarin.Forms.Pages** Nuget:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

藉由提供`DataTemplate`這段程式碼會覆寫`StyleClass`，並改為使用的預設版面配置`ListItemControl`。

[![](get-started-images/custom-sml.png "DataPages 範例應用程式")](get-started-images/custom.png#lightbox "DataPages 範例應用程式")

開發人員偏好在 C# XAML 可以建立資料來源繫結太 (請記得包含`using Xamarin.Forms.Pages;`陳述式):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


從頭開始建立主題還有更多工作, 但未來的預覽版本可讓您更輕鬆地完成這件事。


## <a name="troubleshooting"></a>疑難排解

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>無法載入檔案或組件 'Xamarin.Forms.Theme.Light' 或其中一個相依性

在預覽版本中，佈景主題可能無法在執行階段載入。 新增下面顯示相關的專案。 若要修正此錯誤的程式碼。

**iOS**

在  **AppDelegate.cs**新增下列行之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在  **MainActivity.cs**新增下列行之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>相關連結

- [DataPagesDemo 範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
