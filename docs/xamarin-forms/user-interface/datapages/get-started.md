---
title: 使用 DataPages 消費者入門
description: 本文說明如何使用 DataPages 開始建立簡單的資料驅動頁面 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 325aa7be9e8a1eb6da816ef4ec6403c2882c6ed4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928909"
---
# <a name="getting-started-with-datapages"></a>使用 DataPages 消費者入門

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![此 API 目前為預覽狀態](~/media/shared/preview.png)

> [!IMPORTANT]
> DataPages 需要 Xamarin.Forms 呈現的主題參考。 這牽涉到安裝[ Xamarin.Forms 。主題。基底](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)NuGet 封裝到您的專案中，後面接著[ Xamarin.Forms 。主題 Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/)或[ Xamarin.Forms 。主題. 深色](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)NuGet 套件。

若要開始使用 DataPages Preview 建立簡單的資料驅動頁面，請遵循下列步驟。 本示範使用預覽組建中的硬式編碼樣式（「事件」），其僅適用于程式碼中的特定 JSON 格式。

[![DataPages 範例應用程式](get-started-images/demo-sml.png)](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

## <a name="1-add-nuget-packages"></a>1. 新增 NuGet 套件

將下列 NuGet 套件新增至您的 Xamarin.Forms .NET Standard 程式庫和應用程式專案：

- Xamarin.Forms.頁面
- Xamarin.Forms.主題基底
- 主題執行 NuGet （例如 Xamarin.Forms.主題 Light）

## <a name="2-add-theme-reference"></a>2. 新增主題參考

在**app.xaml**檔案中，新增主題的自訂， `xmlns:mytheme` 並確保主題會合並到應用程式的資源字典中：

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
> 您也應該遵循步驟來[載入主題元件（如下）](#troubleshooting) ，方法是將一些未定案的程式碼新增至 IOS `AppDelegate` 和 Android `MainActivity` 。 這將在未來的預覽版本中改善。

## <a name="3-add-a-xaml-page"></a>3. 新增 XAML 頁面

將新的 XAML 頁面新增至 Xamarin.Forms 應用程式，並將*基類*從變更 `ContentPage` 為 `Xamarin.Forms.Pages.ListDataPage` 。 這必須在 c # 和 XAML 中完成：

**C # 檔案**

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

除了將根項目變更為 `<p:ListDataPage>` 的自訂命名空間之外， `xmlns:p` 也必須加入：

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

變更類別的「函式」， `App` 讓 `MainPage` 設定為 `NavigationPage` 包含新的 `SessionDataPage` 。 *必須*使用導覽頁面。

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. 新增資料來源

刪除專案 `Content` ，並將它取代為 `p:ListDataPage.DataSource` ，以將資料填入頁面。 在下列範例中，會從 URL 載入遠端 Json 資料檔案。

> [!NOTE]
> 預覽*需要* `StyleClass` 屬性來提供資料來源的呈現提示。 是 `StyleClass="Events"` 指在預覽中預先定義的版面配置，並包含*硬式編碼*的樣式，以符合所使用的 JSON 資料來源。

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

示範來源的 JSON 資料範例如下所示：

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

## <a name="4-run"></a>4. 執行！

上述步驟應會產生 [可運作的資料] 頁面：

[![DataPages 範例應用程式](get-started-images/demo-sml.png)](get-started-images/demo.png#lightbox "DataPages 範例應用程式")

這是因為預先建立的樣式「**事件**」存在於淺色主題 NuGet 套件中，而且已定義符合資料來源的樣式（例如， 「標題」、「影像」、「展示者」）。

「事件」 `StyleClass` 是用來顯示 `ListDataPage` 控制項，其中包含 `CardView` 在中定義的自訂控制項 Xamarin.Forms 。頁面. `CardView`控制項有三個屬性： `ImageSource` 、 `Text` 和 `Detail` 。 此主題已硬式編碼，可將資料來源的三個欄位（來自 JSON 檔案）系結至這些屬性以供顯示。

## <a name="5-customize"></a>5. 自訂

您可以藉由指定範本和使用資料來源系結來覆寫繼承的樣式。 下列 XAML 會使用所包含的新和語法，為每個資料列宣告自訂範本 `ListItemControl` `{p:DataSourceBinding}` ** Xamarin.Forms 。分頁**NuGet：

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

藉由提供 `DataTemplate` ，此程式碼會覆寫 `StyleClass` ，而會改為使用的預設版面配置 `ListItemControl` 。

[![DataPages 範例應用程式](get-started-images/custom-sml.png)](get-started-images/custom.png#lightbox "DataPages 範例應用程式")

偏好 c # 至 XAML 的開發人員也可以建立資料來源系結（請務必包含 `using Xamarin.Forms.Pages;` 語句）：

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

從頭開始建立主題還有更多工作，但未來的預覽版本可讓您更輕鬆地完成這件事。

## <a name="troubleshooting"></a>疑難排解

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>無法載入檔案或元件 ' Xamarin.Forms 。主題 Light 或其相依性的其中之一

在預覽版本中，主題可能無法在執行時間載入。 將下方顯示的程式碼新增至相關專案，以修正此錯誤。

**iOS**

在**AppDelegate.cs**中，于之後新增下列幾行：`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**MainActivity.cs**中，于之後新增下列幾行：`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>相關連結

- [DataPagesDemo 範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
