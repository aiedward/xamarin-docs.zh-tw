---
title: 應用程式索引和深層連結
description: 本文說明如何使用應用程式索引和深層連結，讓 Xamarin.Forms 應用程式內容在 iOS 和 Android 裝置上可供搜尋。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: fcd8333a0623058fceb486183ddb995e85eaf18a
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940329"
---
# <a name="application-indexing-and-deep-linking"></a>應用程式索引和深層連結

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_應用程式索引讓經過少數幾次使用就遺忘的應用程式能出現在搜尋結果中，藉此保有關聯性。深層連結可讓應用程式回應包含應用程式資料的搜尋結果，方式通常是瀏覽到參考自深層連結的頁面。本文說明如何使用應用程式索引和深層連結，讓 Xamarin.Forms 應用程式內容在 iOS 和 Android 裝置上可供搜尋。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Xamarin.Forms 和 Azure 的深層連結影片**

Xamarin.Forms 應用程式索引和深層連結提供 API，用來在使用者瀏覽應用程式的同時，為應用程式索引發佈中繼資料。 編製了索引的內容，即可在 Spotlight 搜尋、Google 搜尋或 Web 搜尋中搜尋。 點選包含深層連結的搜尋結果，會引發應用程式能夠處理的事件，而通常會用來瀏覽到參考自深層連結的頁面。

範例應用程式示範了待辦事項清單應用程式，其中資料儲存在本機 SQLite 資料庫中，如下列螢幕擷取畫面所示：

![](deep-linking-images/screenshots.png "TodoList Application")

使用者建立的每個 `TodoItem` 執行個體都編製了索引。 平台專用的搜尋可用來從應用程式中找出已編製索引的資料。 當使用者點選應用程式的搜尋結果時，應用程式隨即啟動，然後瀏覽到 `TodoItemPage`，並顯示參考自深層連結的 `TodoItem`。

如需如何使用 SQLite 的詳細資訊，請參閱 [Xamarin.Forms 本機資料庫](~/xamarin-forms/data-cloud/data/databases.md)。

> [!NOTE]
> Xamarin.Forms 應用程式索引和深層連結功能僅可在 iOS 和 Android 平台使用，且最低版本分別為 iOS 9 與 API 23。

## <a name="setup"></a>安裝程式

以下章節提供在 iOS 和 Android 平台使用此功能的任何額外設定指示。

### <a name="ios"></a>iOS

在 iOS 平台上，請確認您的 iOS 平台專案將 **Entitlements.plist** 檔案設為自訂權利檔案，以簽署套件組合。

使用 iOS Universal Links：

1. 使用 `applinks` 索引鍵將 Associated Domains 權利新增到您的應用程式，包括您的應用程式會支援的所有網域。
1. 將 Apple App Site Association 檔案新增到您的網站。
1. 將 `applinks` 索引鍵新增到 Apple App Site Association 檔案。

如需詳細資訊，請參閱 developer.apple.com 上的[Allowing Apps and Websites to Link to Your Content](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) (允許應用程式和網站連結到您的內容)。

### <a name="android"></a>Android

在 Android 平台上，必須符合數個必要條件才能使用應用程式索引和深層連結功能：

1. 您的應用程式版本必需可從 Google Play 取得。
1. 隨附網站必須向 Google Developer Console 中的應用程式註冊。 在應用程式與網站建立關聯之後，為 URL 編製的索引即可兼用於網站和應用程式，接著在搜尋結果中提供。 如需詳細資訊，請參閱 Google 網站上的[將應用程式編入 Google 搜尋索引](https://support.google.com/googleplay/android-developer/answer/6041489)。
1. 您的應用程式必須在 `MainActivity` 類別支援 HTTP URL 意圖，這會讓應用程式索引知道應用程式可以回應哪些類型的 URL 資料配置。 如需詳細資訊，請參閱[設定意圖篩選](~/android/platform/app-linking.md#configure-intent-filter)。

符合這些必要條件之後，就必須執行下列額外設定，以在 Android 平台上使用 Xamarin.Forms 應用程式索引和深層連結：

1. 將 [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) NuGet 套件安裝到 Android 應用程式專案中。
1. 在 **MainActivity.cs** 檔案中新增宣告以使用 `Xamarin.Forms.Platform.Android.AppLinks` 命名空間。
1. 在 **MainActivity.cs** 檔案中新增宣告以使用 `Firebase` 命名空間。
1. 在網頁瀏覽器中，透過 [Firebase 控制台](https://console.firebase.google.com/)建立新專案。
1. 在 Firebase 控制台中，將 Firebase 新增到您的 Android 應用程式，然後輸入必填資料。
1. 下載產生的 **google-services.json** 檔案。
1. 將 **google-services.json** 檔案新增到 Android 專案的根目錄，並將其 [建置動作]  設為 **GoogleServicesJson**。
1. 在 `MainActivity.OnCreate` 覆寫中，在 `Forms.Init(this, bundle)` 下方新增下列程式碼：

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

當 **google-services.json** 新增到專案 (並已設定 *GoogleServicesJson** 建置動作) 時，建置流程會擷取用戶端識別碼和 API 金鑰，然後將這些認證新增到所產生的資訊清單檔。

> [!NOTE]
> 在此文章中，「應用程式連結」與「深層連結」這兩個詞彙通常會交替使用。 不過，在 Android 上，這些詞彙有不同的意義。 在 Android 上，「深層連結」是意圖篩選條件，可讓使用者直接進入應用程式中的特定活動。 按一下深層連結可能會開啟去除混淆對話方塊，讓使用者選取可處理 URL 的多個應用程式之一。 Android 應用程式連結是以您的網站 URL 為基礎的深層連結，其已驗證為屬於您的網站。 按一下應用程式連結會開啟您的應用程式 (如果已安裝)，而不會開啟去除混淆對話方塊。

如需詳細資訊，請參閱 Xamarin 部落格上的 [Deep Link Content with Xamarin.Forms URL Navigation](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) (使用 Xamarin.Forms URL 瀏覽的深層連結內容)。

## <a name="indexing-a-page"></a>編製頁面索引

為頁面編製索引然後向 Google 和 Spotlight 搜尋公開的流程如下：

1. 建立 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry)，其中包含編製頁面索引所需的中繼資料，以及當使用者選取搜尋結果中已編製索引的內容時，會傳回頁面的深層連結。
1. 註冊 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體來為其編製索引，以供搜尋。

下列程式碼範例示範如何建立 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體：

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

[`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體包含多個屬性，其值均為編製頁面索引和建立深層連結的必要項。 [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title)、[`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) 和 [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) 屬性可在已編製索引的內容出現在搜尋結果中時加以辨識。 [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 屬性會設為 `true`，指出目前正在檢視已編製索引的內容。 [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) 屬性是 `Uri`，包含傳回目前頁面和顯示目前 `TodoItem` 的必要屬性。 下列範例顯示了應用程式範例的範例 `Uri`：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

這個 `Uri` 包含所有必要資訊，能夠啟動 `deeplinking` 應用程式、瀏覽到 `DeepLinking.TodoItemPage` 並顯示 `ID` 為 2 的 `TodoItem`。

## <a name="registering-content-for-indexing"></a>註冊內容以編製索引

[`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體一經建立，就必須註冊索引，才能出現在搜尋結果中。 這會透過 [`RegisterLink`](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) 完成，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

這會將 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體新增到應用程式的 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 集合。

> [!NOTE]
> `RegisterLink` 方法也可用來更新已為頁面編製索引的內容。

只要 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體已註冊索引，就可以顯示在搜尋結果中。 下列螢幕擷取畫面顯示了顯示 iOS 平台上搜尋結果中已編製索引的內容：

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>取消註冊已編製索引的內容

[`DeregisterLink`](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) 方法可用來從搜尋結果移除已編製索引的內容，如下列程式碼範例中所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

這會從應用程式的 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 集合移除 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體。

> [!NOTE]
> 在 Android 上，無法從搜尋結果中移除已編製索引的內容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>回應深層連結

當已編製索引的內容顯示在搜尋內容中，而且使用者加以選取時，應用程式的 `App` 類別會收到要求，處理已編製索引的內容中包含的 `Uri`。 這項要求可在 [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) 覆寫中處理，如下列程式碼範例所示：

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

[`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) 方法會檢查所收到的 `Uri` 是否要用於應用程式，之後才將 `Uri` 剖析成要瀏覽的目標頁面，以及要傳遞到頁面的參數。 系統會建立瀏覽目標頁面的執行個體，以及擷取頁面參數所代表的 `TodoItem`。 瀏覽目標頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 便會設為 `TodoItem`。 如此可確保當 [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) 方法顯示了 `TodoItemPage` 時，其會顯示 `ID` 包含在深層連結中的 `TodoItem`。

## <a name="making-content-available-for-search-indexing"></a>讓內容可用於搜尋索引

每當深層連結所代表的頁面顯示時，[`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 屬性皆可設為 `true`。 在 iOS 和 Android 上，這樣做會讓 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體可用於搜尋索引；在 iOS 上，`AppLinkEntry` 執行個體則可用於 Handoff。 如需遞交的詳細資訊，請參閱 [Handoff 簡介](~/ios/platform/handoff.md)。

下列程式碼範例示範了如何在 [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 覆寫中將 [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 屬性設為 `true`：

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

同樣地，當瀏覽到深層連結所代表的頁面之外時，[`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 屬性可設為 `false`。 在 iOS 和 Android 上，這樣做會停止公告 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 執行個體可用於搜尋索引；在 iOS 上，也會停止公告 `AppLinkEntry` 執行個體進行 Handoff。 這可在 [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 覆寫中完成，如下列程式碼範例所示：

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>提供資料給 Handoff

在 iOS 上，應用程式專屬的資料可在編製頁面索引時儲存。 將資料新增到 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合即可達成這個目的，也就是 `Dictionary<string, string>`，可用於儲存要在 Handoff 中使用的索引鍵/值組。 Handoff 這種方法讓使用者可以在其中一部裝置上開始活動，然後在另一部裝置上接續該活動 (以使用者的 iCloud 帳戶識別)。 下列程式碼顯示了儲存應用程式專屬索引鍵/值組的範例：

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

儲存在 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合中的值會儲存在編製索引頁面的中繼資料中，並會在使用者點選包含深層連結的搜尋結果時 (或在另一部已登入的裝置上使用 Handoff 來檢視內容時) 還原。

此外，您可指定下列索引鍵的值：

- `contentType`：`string`，指定索引內容的統一類型識別碼。 用於此值的建議慣例是包含索引內容的頁面類型名稱。
- `associatedWebPage`：`string`，若索引內容也可在 Web 上檢視，或應用程式支援 Safari 的深層連結，即代表要瀏覽的網頁。
- `shouldAddToPublicIndex`：`true` 或 `false` 的 `string`，控制是否要將索引內容新增到 Apple 的公用雲端索引，以向尚未在 iOS 裝置上安裝應用程式的使用者呈現。 不過，即使內容已為公用索引經過設定，也不代表會自動新增到 Apple 的公用雲端索引。 如需詳細資料，請參閱[公用搜尋索引](~/ios/platform/search/nsuseractivity.md)。 請注意，在將個人資料新增到 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合時，此索引鍵應設為 `false`。

> [!NOTE]
> Android 平台未使用 `KeyValues` 集合。

如需遞交的詳細資訊，請參閱 [Handoff 簡介](~/ios/platform/handoff.md)。

## <a name="summary"></a>總結

本文說明如何使用應用程式索引和深層連結，讓 Xamarin.Forms 應用程式內容在 iOS 和 Android 裝置上可供搜尋。 應用程式索引讓經過少數幾次使用就遺忘的應用程式能出現在搜尋結果中，藉此保有關聯性。

## <a name="related-links"></a>相關連結

- [深層連結 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)
- [iOS 搜尋 API](~/ios/platform/search/index.md)
- [Android 6.0 中的應用程式連結](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
