---
title: "開始使用 DataPages"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 3b5346b6d556f6437d9c9fc17897180fd0b41b1e
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="getting-started-with-datapages"></a>開始使用 DataPages

![](~/media/shared/preview.png "這個 API 目前處於預覽狀態")

> [!IMPORTANT]
> 需要 DataPages [Xamarin.Forms 佈景主題](~/xamarin-forms/user-interface/themes/index.md)來呈現的參考。


若要開始建置簡單的資料磁碟機頁面使用 DataPages Preview，請遵循下列步驟。 此示範會使用硬式編碼中的樣式 （「 事件 」） 預覽組建，只能搭配特定程式碼中的 JSON 格式。

[![](get-started-images/demo-sml.png "DataPages 範例應用程式")](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

## <a name="1-add-nuget-packages"></a>1.新增 NuGet 封裝

將下列 Nuget 封裝加入至您 Xamarin.Forms PCL 和應用程式專案：

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* 佈景主題的實作 （例如 Nuget Xamarin.Forms.Themes.Light)

## <a name="2-add-theme-reference"></a>2.將佈景主題參考

在**App.xaml**檔案中，新增自訂`xmlns:mytheme`佈景主題，並確定佈景主題會合併到應用程式的資源字典：

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

**重要事項：**您也應該遵循的步驟[載入佈景主題 （下方） 的組件](#loadtheme)的未定案程式碼加入至 iOS`AppDelegate`和 Android `MainActivity`。 這將在未來的預覽版本中改進。


## <a name="3-add-a-xaml-page"></a>3.加入 XAML 頁面

Xamarin.Forms 應用程式中，加入新的 XAML 頁面和*基底類別變更*從`ContentPage`至`Xamarin.Forms.Pages.ListDataPage`。 這會有執行在 C# 和 XAML 的動作：

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

除了變更根項目之外`<p:ListDataPage>`的自訂命名空間`xmlns:p`也必須加入：

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

變更`App`類別建構函式，讓`MainPage`設`NavigationPage`包含新`SessionDataPage`。 瀏覽頁面*必須*使用。

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3.新增資料來源

刪除`Content`項目並將它取代為`p:ListDataPage.DataSource`來填入資料的頁面。 在下列遠端 Json 範例資料檔案從 URL 載入。

**注意：**預覽*需要*`StyleClass`對資料來源提供呈現提示的屬性。 `StyleClass="Events"`指的預先定義的預覽中，其中包含樣式的配置*硬式編碼*以符合所使用的 JSON 資料來源。

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

上述的步驟應該產生的工作資料頁：

[![](get-started-images/demo-sml.png "DataPages 範例應用程式")](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

因為預先建立的樣式**「 事件 」**淺色佈景主題的 Nuget 封裝中存在，而且有定義符合資料來源 （例如，樣式 "title"、"image"、"展示器 」）。

「 事件 」`StyleClass`建置用來顯示`ListDataPage`具有自訂控制項`CardView`控制也就是定義在 Xamarin.Forms.Pages。 `CardView`控制項有三個屬性： `ImageSource`， `Text`，和`Detail`。 是以硬式編碼繫結資料來源的三個欄位 （從 JSON 檔案） 來顯示這些屬性。

## <a name="5-customize"></a>5.自訂

可以覆寫繼承的樣式，藉由指定的範本，並使用資料來源繫結。 以下的 XAML 宣告每個資料列使用新的自訂範本`ListItemControl`和`{p:DataSourceBinding}`語法隨附於**Xamarin.Forms.Pages** Nuget:

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

藉由提供`DataTemplate`這段程式碼會覆寫`StyleClass`而改為使用預設的配置`ListItemControl`。

[![](get-started-images/custom-sml.png "DataPages 範例應用程式")](get-started-images/custom.png#lightbox "DataPages 範例應用程式")

開發人員偏好 C# xaml 可以建立資料來源繫結太 (請記得以`using Xamarin.Forms.Pages;`陳述式):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


這是要從頭開始建立佈景主題的更多工作 (請參閱[佈景主題的指南](~/xamarin-forms/user-interface/themes/index.md))，但未來的預覽版本將能夠更容易執行。


## <a name="troubleshooting"></a>疑難排解

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>無法載入檔案或組件 'Xamarin.Forms.Theme.Light' 或其中一個相依性

在預覽版本中，可能無法在執行階段載入佈景主題。 加入程式碼如下所示到相關的專案以修正這個錯誤。

**iOS**

在**d**新增以下行列之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**Weatherapp**新增以下行列之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>相關連結

- [DataPagesDemo 範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
