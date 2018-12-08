---
title: 應用程式編製索引和深層連結
description: 這篇文章說明如何使用應用程式編製索引和深層連結，讓 Xamarin.Forms 應用程式內容的可搜尋在 iOS 和 Android 裝置上。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: c4e634ce51080ad38b093e1355767c73c72e837a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059622"
---
# <a name="application-indexing-and-deep-linking"></a>應用程式編製索引和深層連結

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)

_應用程式編製索引，可讓會否則會被遺忘後幾個使用的搜尋結果中出現掌握新知的應用程式。深層連結，可讓應用程式，以回應包含應用程式資料，通常由巡覽至頁面的深層連結從參考的搜尋結果。這篇文章說明如何使用應用程式編製索引和深層連結，讓 Xamarin.Forms 應用程式內容的可搜尋在 iOS 和 Android 裝置上。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**深層連結 Xamarin.Forms 和 Azure，藉由[Xamarin University](https://university.xamarin.com/)**


Xamarin.Forms 應用程式編製索引和深層連結提供的 API 應用程式編製索引，當使用者瀏覽應用程式的發行中繼資料。 索引的內容則搜尋的在 Spotlight 搜尋中、 在 Google 搜尋中，或在 web 搜尋。 點選搜尋結果，其中包含的深層連結就會引發事件，可由應用程式，並通常用來瀏覽至參照從深層連結的頁面。

範例應用程式會示範在本機 SQLite 資料庫中，儲存資料的待辦事項清單應用程式，如下列螢幕擷取畫面所示：

![](deep-linking-images/screenshots.png "TodoList 應用程式")

每個`TodoItem`使用者所建立的執行個體也會編製索引。 平台特有的搜尋可用來找出索引的資料，從應用程式。 當使用者點選應用程式在搜尋結果項目上時，啟動應用程式，`TodoItemPage`巡覽，而`TodoItem`參考從深層連結會顯示。

如需使用 SQLite 資料庫的詳細資訊，請參閱[Xamarin.Forms 本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)。

> [!NOTE]
> Xamarin.Forms 應用程式編製索引和深層連結功能只適用於 iOS 和 Android 平台，而且分別需要 iOS 9 和 API 23 的最小值。

## <a name="setup"></a>安裝程式

下列各節提供在 iOS 和 Android 平台上使用這項功能的任何其他安裝指示。

### <a name="ios"></a>iOS

在 iOS 平台，請確定您的 iOS 平台專案設定**Entitlements.plist**與自訂權利檔案來簽署套件組合的檔案。

若要使用 iOS 通用連結：

1. 新增您的應用程式的相關聯的網域權利`applinks`您的應用程式將支援索引鍵，包括所有定義域。
1. 將 Apple 應用程式的站台關聯的檔案新增至您的網站。
1. 新增`applinks`Apple 應用程式的站台關聯的檔案索引鍵。

如需詳細資訊，請參閱 <<c0> [ 可讓應用程式和連結至您內容的網站](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content)developer.apple.com 上。

### <a name="android"></a>Android

Android 平台上，有一些必須符合，以使用應用程式編製索引和深層連結功能的必要條件：

1. 您的應用程式的版本必須是即時在 Google Play 上。
1. 指南網站必須向 Google 開發人員主控台中的應用程式。 應用程式與網站相關聯時，可以是 Url 的網站和應用程式，然後在搜尋結果中提供服務可編製索引。 如需詳細資訊，請參閱 <<c0> [ 應用程式編製索引，在 Google 搜尋](https://support.google.com/googleplay/android-developer/answer/6041489)Google 網站上。
1. 您的應用程式必須支援上的 HTTP URL 意圖`MainActivity`告知編製索引的 URL 配置的資料類型的應用程式的應用程式的類別可以回應。 如需詳細資訊，請參閱 <<c0> [ 設定意圖篩選](~/android/platform/app-linking.md#configure-intent-filter)。

一旦符合這些必要條件，下列的其他設定，才能使用 Xamarin.Forms 應用程式編製索引和深層連結的 Android 平台：

1. 安裝[Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/)到 Android 應用程式專案的 NuGet 套件。
1. 在  **MainActivity.cs**檔案中，將宣告新增至使用`Xamarin.Forms.Platform.Android.AppLinks`命名空間。
1. 在  **MainActivity.cs**檔案中，將宣告新增至使用`Firebase`命名空間。
1. 在 web 瀏覽器中，會建立新的專案，透過[Firebase 主控台](https://console.firebase.google.com/)。
1. 在 Firebase 主控台中，將 Firebase 新增至 Android 應用程式，並輸入所需的資料。
1. 下載產生**google-services.json**檔案。
1. 新增**google-services.json**檔案至 Android 專案的根目錄，並設定其**建置動作**來**GoogleServicesJson**。
1. 在 `MainActivity.OnCreate`覆寫，加入下列這一行下方的程式碼`Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

當**google-services.json**加入至專案 (而*GoogleServicesJson** 建置動作設定)，建置程序會擷取用戶端識別碼和 API 金鑰，並將新增到這些認證產生的資訊清單檔案。

如需詳細資訊，請參閱 <<c0> [ 深層連結內容的 URL 導覽 Xamarin.Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) Xamarin 部落格上。

## <a name="indexing-a-page"></a>編製索引的頁面

編製索引的頁面，並將其公開到 Google 和 Spotlight 搜尋的程序如下所示：

1. 建立[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)包含 [索引] 頁面，以及當使用者在搜尋結果中選取索引的內容傳回至頁面的深層連結的必要中繼資料。
1. 註冊[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)索引來搜尋的執行個體。

下列程式碼範例示範如何建立[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)執行個體：

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)執行個體會包含其值是 [索引] 頁面及建立所需的深層連結的屬性數目。 [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title)， [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description)，以及[ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail)屬性用來識別索引的內容，當它出現在搜尋結果。 [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)屬性設定為`true`表示目前檢視索引的內容。 [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri)屬性是`Uri`，其中包含要傳回至目前頁面並顯示目前所需的資訊`TodoItem`。 下列範例示範`Uri`範例應用程式：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

這`Uri`包含啟動所需的所有資訊`deeplinking`應用程式中，瀏覽至`DeepLinking.TodoItemPage`，並顯示`TodoItem`具有`ID`為 2。

## <a name="registering-content-for-indexing"></a>註冊內容編製索引

一次[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)已建立執行個體，則必須註冊才會出現在搜尋結果編製索引。 這可完成[ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry))方法，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

這會新增[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)應用程式的執行個體[ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks)集合。

> [!NOTE]
> `RegisterLink`方法也可用來更新已編製索引頁面的內容。

一次[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)執行個體已註冊的編製索引，它可以出現在搜尋結果中。 下列螢幕擷取畫面顯示索引在 iOS 平台上的搜尋結果中顯示的內容：

![](deep-linking-images/ios-search.png "在 iOS 上的搜尋結果中的索引的內容")

## <a name="de-registering-indexed-content"></a>取消註冊編製索引的內容

[ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry))方法用來從搜尋結果中，移除索引的內容，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

這會移除[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)從應用程式的執行個體[ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks)集合。

> [!NOTE]
> 在 Android 上不可能從搜尋結果中移除索引的內容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>回應的深層連結

當索引的內容會出現在搜尋結果，而且已選取使用者，`App`類別的應用程式將會收到要處理的要求`Uri`包含在索引的內容。 可以處理此要求[ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri))覆寫，如下列程式碼範例所示：

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

[ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri))方法會檢查所接收`Uri`適用於應用程式時，剖析之前`Uri`到頁面，即可瀏覽至並使用參數來傳遞至頁面。 若要瀏覽至建立時，page 的執行個體和`TodoItem`表示參數擷取的頁面。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)頁面，即可瀏覽至然後設為`TodoItem`。 這可確保，當`TodoItemPage`會顯示[ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))方法，它將會顯示`TodoItem`其`ID`包含在深層連結。

## <a name="making-content-available-for-search-indexing"></a>將內容提供給搜尋索引編製

表示的深層連結的網頁會顯示，每次[ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)屬性可以設定為`true`。 IOS 和 Android 上這樣[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)搜尋索引編製，以及僅在 iOS 上可使用的執行個體，它也會使`AppLinkEntry`適用於遞移式的執行個體。 如需遞移式的詳細資訊，請參閱[遞移式簡介](~/ios/platform/handoff.md)。

下列程式碼範例示範如何設定[ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)屬性設`true`中[ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)覆寫：

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

同樣地，當您巡覽離開的表示的深層連結的網頁是，才[ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)屬性可以設定為`false`。 在 iOS 和 Android 上，這樣會阻止[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)通告搜尋索引，然後在 iOS，it 也的執行個體停止廣告`AppLinkEntry`遞移式的執行個體。 這可在[ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)覆寫，如下列程式碼範例所示：

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>提供資料給遞交

在 iOS 上，應用程式專屬資料可以儲存在索引頁面時。 做法是將資料加入至[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)集合，也就是`Dictionary<string, string>`將使用的索引鍵 / 值組儲存在遞移式。 遞移式是方法，以讓使用者針對其裝置的其中一個開始活動，並繼續該活動在其裝置的另一個 （識別使用者的 iCloud 帳戶）。 下列程式碼顯示將儲存應用程式特定索引鍵 / 值組的範例：

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

中所儲存值[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)集合會儲存在 [索引] 頁面的中繼資料，並在使用者點選的搜尋結果，其中包含的深層連結 （或是遞移式使用時，另一個檢視內容時，將會還原登入的裝置）。

此外，您可以指定下列索引鍵的值：

- `contentType` – `string` ，指定索引的內容的統一的型別識別項。 建議的慣例，以使用此值是包含索引的內容頁面的型別名稱。
- `associatedWebPage` –`string`表示索引的內容也可以檢視在 web 上，則應用程式支援 Safari 的深層連結瀏覽的網頁。
- `shouldAddToPublicIndex` –`string`任一`true`或`false`可控制是否要將索引的內容新增至 Apple 的公用雲端索引，然後向尚未在其 iOS 裝置安裝的應用程式的使用者。 不過，只是在內容已設定公用編製索引，因為它並不表示，它將會自動加入至 Apple 的公用雲端的索引。 如需詳細資訊，請參閱 <<c0> [ 公開搜尋索引編製](~/ios/platform/search/nsuseractivity.md)。 請注意，此索引鍵應該設定為`false`新增至個人資料時[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)集合。

> [!NOTE]
> `KeyValues`集合不會使用的 Android 平台。

如需遞移式的詳細資訊，請參閱[遞移式簡介](~/ios/platform/handoff.md)。

## <a name="summary"></a>總結

這篇文章說明如何使用應用程式編製索引和深層連結，讓 Xamarin.Forms 應用程式內容的可搜尋在 iOS 和 Android 裝置上。 應用程式編製索引，可讓應用程式，以藉由將否則會忘記後幾個使用的搜尋結果中出現掌握新知。

## <a name="related-links"></a>相關連結

- [深層連結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [iOS 搜尋 Api](~/ios/platform/search/index.md)
- [在 Android 6.0 應用程式連結](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
