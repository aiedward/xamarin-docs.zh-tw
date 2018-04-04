---
title: 應用程式編製索引和深層連結
description: 應用程式編製索引，可讓會後一些使用搜尋結果中出現的停留相關否則忘記的應用程式。 深層連結，可讓應用程式來瀏覽至頁面，參考從深層連結通常包含應用程式資料的搜尋結果的回應。 本文示範如何使用應用程式的索引，以及讓 iOS 和 Android 裝置上搜尋 Xamarin.Forms 應用程式內容的深層連結。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: d302e24ee74290eecd3dd9ad3f81921d6654cc10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="application-indexing-and-deep-linking"></a>應用程式編製索引和深層連結

_應用程式編製索引，可讓會後一些使用搜尋結果中出現的停留相關否則忘記的應用程式。深層連結，可讓應用程式來瀏覽至頁面，參考從深層連結通常包含應用程式資料的搜尋結果的回應。本文示範如何使用應用程式的索引，以及讓 iOS 和 Android 裝置上搜尋 Xamarin.Forms 應用程式內容的深層連結。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**深層連結 Xamarin.Forms 和 Azure 中，藉由[Xamarin 大學](https://university.xamarin.com/)**


Xamarin.Forms 應用程式編製索引和深層連結應用程式開發介面提供應用程式編製索引，當使用者瀏覽應用程式的發行中繼資料。 索引的內容則如搜尋，Spotlight 搜尋、 在 Google 搜尋中，或是在 web 搜尋。 點選搜尋結果，其中包含深層連結會引發事件，可以由應用程式，並通常用來瀏覽至參照從深層連結的頁面。

範例應用程式示範 Todo 清單的應用程式，在本機 SQLite 資料庫中，儲存資料，如下列螢幕擷取畫面所示：

![](deep-linking-images/screenshots.png "TodoList 應用程式")

每個`TodoItem`使用者所建立的執行個體編製索引。 平台特定的搜尋可用來尋找索引的資料，從應用程式。 當使用者點選應用程式在搜尋結果項目上時，啟動應用程式，`TodoItemPage`巡覽，而`TodoItem`參考從深層連結會顯示。

如需使用 SQLite 資料庫的詳細資訊，請參閱[使用本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)。

> [!NOTE]
> Xamarin.Forms 應用程式編製索引及深層連結功能僅適用於 iOS 和 Android 平台，而且分別需要 iOS 9 和 API 23。

## <a name="setup"></a>安裝程式

下列各節提供在 iOS 和 Android 平台上使用這項功能的任何其他安裝指示。

### <a name="ios"></a>iOS

在 iOS 平台上沒有任何額外的設定，才能使用這項功能。

### <a name="android"></a>Android

Android 平台上，有一些必須符合才能使用應用程式編製索引及深層連結功能的必要條件：

1. 您的應用程式的版本必須是即時 Google Play 上。
1. 針對在 Google 開發人員主控台應用程式，必須先註冊附屬網站。 應用程式與網站相關聯時，Url 可以編製索引的網站和應用程式，然後在搜尋結果中處理該工作。 如需詳細資訊，請參閱[應用程式索引 Google 搜尋](https://support.google.com/googleplay/android-developer/answer/6041489)Google 網站上。
1. 您的應用程式必須支援 HTTP URL 對應方式上`MainActivity`告知應用程式的應用程式的 URL 配置的資料類型編製索引的類別可以回應。 如需詳細資訊，請參閱[設定意圖篩選](~/android/platform/app-linking.md#configure-intent-filter)。

一旦符合這些先決條件，下列額外的設定，才能使用 Xamarin.Forms 應用程式編製索引和 Android 平台上的深層連結：

1. 安裝[Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) Android 應用程式專案將 NuGet 封裝。
1. 在`MainActivity.cs`檔案中，匯入`Xamarin.Forms.Platform.Android.AppLinks`命名空間。
1. 在`MainActivity.OnCreate`覆寫中，加入下列程式碼下方`Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

如需詳細資訊，請參閱[深層連結內容具有 Xamarin.Forms URL 導覽](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)Xamarin 部落格上。

## <a name="indexing-a-page"></a>編製索引的頁面

索引頁，而且已公開至 Google 和 Spotlight 搜尋的程序如下所示：

1. 建立[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)包含索引頁面，以及深層連結返回頁面，使用者在搜尋結果中選取索引的內容時所需的中繼資料。
1. 註冊[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)索引搜尋的執行個體。

下列程式碼範例示範如何建立[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)執行個體：

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)執行個體會包含的數字，其值會以索引頁面，並建立深層連結所需的屬性。 [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Title/)， [ `Description` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Description/)，和[ `Thumbnail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Thumbnail/)屬性用來識別索引的內容，當它出現在搜尋結果。 [ `IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)屬性設定為`true`，表示目前檢視索引的內容。 [ `AppLinkUri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.AppLinkUri/)屬性是`Uri`，其中包含以返回至目前的頁面，並顯示目前所需的資訊`TodoItem`。 下列範例顯示範例`Uri`範例應用程式：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

這`Uri`包含啟動所需的所有資訊`deeplinking`應用程式中，瀏覽至`DeepLinking.TodoItemPage`，並顯示`TodoItem`具有`ID`的`ec38ebd1-811e-4809-8a55-0d028fce7819`。

## <a name="registering-content-for-indexing"></a>註冊內容索引

一次[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)已建立執行個體，則必須註冊才會出現在搜尋結果編製索引。 這是與[ `RegisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.RegisterLink/p/Xamarin.Forms.IAppLinkEntry/)方法，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

這樣會加入[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)應用程式的執行個體[ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/)集合。

> [!NOTE]
> `RegisterLink`方法也可用來更新已編製索引的頁面內容。

一次[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)執行個體已註冊的索引，它可以出現在搜尋結果。 下列螢幕擷取畫面顯示索引在 iOS 平台上的搜尋結果中出現的內容：

![](deep-linking-images/ios-search.png "在 iOS 上的搜尋結果中的索引的內容")

## <a name="de-registering-indexed-content"></a>取消註冊編製索引的內容

[ `DeregisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.DeregisterLink/p/Xamarin.Forms.IAppLinkEntry/)方法用來從搜尋結果中，移除索引的內容，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

這會移除[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)從應用程式的執行個體[ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/)集合。

> [!NOTE]
> 在 Android 上不可以從搜尋結果中移除索引的內容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>回應深層連結

當索引的內容會出現在搜尋結果，而且已選取的使用者，`App`類別應用程式將會收到要求，以處理`Uri`包含在索引的內容。 可以處理此要求[ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/)覆寫，如下列程式碼範例所示：

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

[ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/)方法會檢查所接收的`Uri`適用於應用程式，再剖析`Uri`至頁面，即可瀏覽至並使用參數來傳遞至網頁。 若要瀏覽至建立時，頁面的執行個體和`TodoItem`表示參數擷取的頁面。 [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)要巡覽至之頁面的會接著設為`TodoItem`。 這可確保，當`TodoItemPage`顯示[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)方法，它將會顯示`TodoItem`其`ID`包含在深層連結。

## <a name="making-content-available-for-search-indexing"></a>讓搜尋索引內容

顯示由深層連結的網頁時，每次[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)屬性可以設定為`true`。 IOS 和 Android 上這樣[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)搜尋編製索引，及僅限 iOS 上可用的執行個體，您也可以`AppLinkEntry`供遞移式的執行個體。 如需遞移式的詳細資訊，請參閱[簡介遞交](~/ios/platform/handoff.md)。

下列程式碼範例示範如何設定[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)屬性`true`中[ `Page.OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)覆寫：

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

同樣地，當深層連結所代表的頁面巡覽離開的[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)屬性可以設定為`false`。 在 iOS 和 Android，這樣會阻止[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)通告搜尋索引，然後在 iOS，it 也的執行個體停止廣告`AppLinkEntry`遞移式的執行個體。 這可以透過完成[ `Page.OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/)覆寫，如下列程式碼範例所示：

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>提供資料給遞交

在 iOS 上，應用程式特定資料可以儲存索引頁面時。 這藉由將資料加入至[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)所屬集合`Dictionary<string, string>`遞交中儲存使用的索引鍵-值組。 遞交是使用者針對其裝置的其中一個開始活動，並繼續該活動在其裝置的另一台 （由使用者的 iCloud 帳戶） 的方法。 下列程式碼顯示將儲存應用程式特定索引鍵-值組的範例：

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

中所儲存值[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)集合會儲存在索引頁面中，中繼資料，以及當使用者點選搜尋結果，其中包含深層連結 （或檢視上其他內容使用遞交時將會還原登入的裝置）。

此外，您可以指定下列索引鍵的值：

- `contentType` –`string`的指定索引的內容的統一的型別識別碼。 建議要用於此值的慣例是包含索引的內容頁面的型別名稱。
- `associatedWebPage` –`string`表示如果索引的內容也可以在網站上檢視或應用程式支援 Safari 的深層連結，請瀏覽的網頁。
- `shouldAddToPublicIndex` –`string`的其中一個`true`或`false`可控制是否要將索引的內容加入至 Apple 的公用雲端索引，然後向未在其 iOS 裝置安裝的應用程式的使用者。 不過，內容已設定公用編製索引，因為它並不表示，它就會自動加入至 Apple 的公用雲端的索引。 如需詳細資訊，請參閱[公用搜尋索引](~/ios/platform/search/nsuseractivity.md)。 請注意，這個金鑰應該設定為`false`時新增至個人資料[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)集合。

> [!NOTE]
> `KeyValues`集合不會在 Android 平台上使用。

如需遞移式的詳細資訊，請參閱[簡介遞交](~/ios/platform/handoff.md)。

## <a name="summary"></a>總結

本文示範如何使用應用程式的索引，以及讓 iOS 和 Android 裝置上搜尋 Xamarin.Forms 應用程式內容的深層連結。 應用程式編製索引，可讓應用程式保持相關會否則會忘記後一些使用的搜尋結果中出現。


## <a name="related-links"></a>相關連結

- [深層連結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [iOS 搜尋 Api](~/ios/platform/search/index.md)
- [在 Android 6.0 連結應用程式](~/android/platform/app-linking.md)
- [AppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)
- [IAppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinkEntry/)
- [IAppLinks](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinks/)
