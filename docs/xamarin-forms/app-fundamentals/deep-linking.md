---
title: 應用程式索引和深層連結
description: 本文說明如何使用應用程式索引和深層連結，讓 Xamarin.Forms 應用程式內容在 iOS 和 Android 裝置上可供搜尋。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1eb2bd3f6b996ece3388c3ce8ad8fceca4c31957
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751349"
---
# <a name="application-indexing-and-deep-linking"></a>應用程式索引和深層連結

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/deeplinking)

Xamarin.Forms 應用程式索引和深層連結提供 API，可在使用者流覽應用程式時，發行應用程式編制索引的中繼資料。 編製了索引的內容，即可在 Spotlight 搜尋、Google 搜尋或 Web 搜尋中搜尋。 點選包含深層連結的搜尋結果，會引發應用程式能夠處理的事件，而通常會用來瀏覽到參考自深層連結的頁面。

範例應用程式示範了待辦事項清單應用程式，其中資料儲存在本機 SQLite 資料庫中，如下列螢幕擷取畫面所示：

![ToDoList 應用程式](deep-linking-images/screenshots.png)

使用者建立的每個 `TodoItem` 執行個體都編製了索引。 平台專用的搜尋可用來從應用程式中找出已編製索引的資料。 當使用者點選應用程式的搜尋結果時，應用程式隨即啟動，然後瀏覽到 `TodoItemPage`，並顯示參考自深層連結的 `TodoItem`。

如需有關使用 SQLite 資料庫的詳細資訊，請參閱[ Xamarin.Forms 本機資料庫](~/xamarin-forms/data-cloud/data/databases.md)。

> [!NOTE]
> Xamarin.Forms 應用程式索引和深層連結功能僅適用于 iOS 和 Android 平臺，而且至少需要 iOS 9 和 API 23。

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

符合這些必要條件之後，就必須執行下列額外的設定，才能 Xamarin.Forms 在 Android 平臺上使用應用程式索引和深層連結：

1. 安裝[ Xamarin.Forms 。](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/)將 NuGet 套件 AppLinks 至 Android 應用程式專案。
1. 在 **MainActivity.cs** 檔案中新增宣告以使用 `Xamarin.Forms.Platform.Android.AppLinks` 命名空間。
1. 在 **MainActivity.cs** 檔案中新增宣告以使用 `Firebase` 命名空間。
1. 在網頁瀏覽器中，透過 [Firebase 控制台](https://console.firebase.google.com/)建立新專案。
1. 在 Firebase 控制台中，將 Firebase 新增到您的 Android 應用程式，然後輸入必填資料。
1. 下載產生的 **google-services.json** 檔案。
1. 將 **google-services.json** 檔案新增到 Android 專案的根目錄，並將其 [建置動作] 設為 **GoogleServicesJson**。
1. 在 `MainActivity.OnCreate` 覆寫中，在 `Forms.Init(this, bundle)` 下方新增下列程式碼：

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

當 **google-services.json** 新增到專案 (並已設定 *GoogleServicesJson** 建置動作) 時，建置流程會擷取用戶端識別碼和 API 金鑰，然後將這些認證新增到所產生的資訊清單檔。

> [!NOTE]
> 在此文章中，「應用程式連結」與「深層連結」這兩個詞彙通常會交替使用。 不過，在 Android 上，這些詞彙有不同的意義。 在 Android 上，「深層連結」是意圖篩選條件，可讓使用者直接進入應用程式中的特定活動。 按一下深層連結可能會開啟去除混淆對話方塊，讓使用者選取可處理 URL 的多個應用程式之一。 Android 應用程式連結是以您的網站 URL 為基礎的深層連結，其已驗證為屬於您的網站。 按一下應用程式連結會開啟您的應用程式 (如果已安裝)，而不會開啟去除混淆對話方塊。

如需詳細資訊，請參閱 Xamarin blog 上的 [深層連結內容與 Xamarin.Forms URL 導覽](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) 。

## <a name="indexing-a-page"></a>編製頁面索引

為頁面編製索引然後向 Google 和 Spotlight 搜尋公開的流程如下：

1. 建立 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) ，其中包含為頁面編制索引所需的中繼資料，以及在使用者于搜尋結果中選取索引內容時返回頁面的深層連結。
1. 註冊 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 實例來為其編制索引以供搜尋。

下列程式碼範例示範如何建立 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 實例：

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

[`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry)實例包含許多屬性，這些屬性的值是索引頁面和建立深層連結的必要值。 [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title)、 [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) 和屬性會 [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) 用來識別出現在搜尋結果中的索引內容。 [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)屬性設定為， `true` 表示目前正在查看索引內容。 [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri)屬性為 `Uri` ，其中包含返回目前頁面並顯示目前頁面所需的資訊 `TodoItem` 。 下列範例顯示了應用程式範例的範例 `Uri`：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

這個 `Uri` 包含所有必要資訊，能夠啟動 `deeplinking` 應用程式、瀏覽到 `DeepLinking.TodoItemPage` 並顯示 `ID` 為 2 的 `TodoItem`。

## <a name="registering-content-for-indexing"></a>註冊內容以編製索引

一旦 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 建立實例之後，就必須註冊，才能在搜尋結果中顯示其索引。 這可透過 [ `RegisterLink` ] (x：來完成 Xamarin.Forms 。IAppLinks. RegisterLink (Xamarin.Forms 。IAppLinkEntry) ) 方法，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

這會將 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 實例加入至應用程式的 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 集合。

> [!NOTE]
> `RegisterLink` 方法也可用來更新已為頁面編製索引的內容。

一旦 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 註冊實例以進行索引，它就會出現在搜尋結果中。 下列螢幕擷取畫面顯示了顯示 iOS 平台上搜尋結果中已編製索引的內容：

![iOS 上搜尋結果中已編製索引的內容](deep-linking-images/ios-search.png)

## <a name="de-registering-indexed-content"></a>取消註冊已編製索引的內容

[ `DeregisterLink` ] (x： Xamarin.Forms 。IAppLinks. DeregisterLink (Xamarin.Forms 。IAppLinkEntry) ) 方法是用來從搜尋結果移除已編制索引的內容，如下列程式碼範例所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

這會 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 從應用程式的集合中移除實例 [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) 。

> [!NOTE]
> 在 Android 上，無法從搜尋結果中移除已編製索引的內容。

## <a name="responding-to-a-deep-link"></a>回應深層連結

當已編製索引的內容顯示在搜尋內容中，而且使用者加以選取時，應用程式的 `App` 類別會收到要求，處理已編製索引的內容中包含的 `Uri`。 您可以在覆寫中處理此要求 [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) ，如下列程式碼範例所示：

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

[`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri))方法會先檢查所收到的 `Uri` 是否適用于應用程式，然後再將剖析 `Uri` 成要導覽的頁面，以及要傳遞至頁面的參數。 系統會建立瀏覽目標頁面的執行個體，以及擷取頁面參數所代表的 `TodoItem`。 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)接著，會將要流覽之頁面的設定為 `TodoItem` 。 這可確保 `TodoItemPage` [ `PushAsync` ] (x：顯示時 Xamarin.Forms 。INavigation. PushAsync (Xamarin.Forms 。頁面) ) 方法，它會顯示 `TodoItem` `ID` 包含在深層連結中的。

## <a name="making-content-available-for-search-indexing"></a>讓內容可用於搜尋索引

每次顯示深層連結所代表的頁面時， [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 屬性就可以設定為 `true` 。 在 iOS 和 Android 上，這會讓 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 實例可用於搜尋索引，而僅在 ios 上，它也會讓 `AppLinkEntry` 實例可供交付。 如需遞交的詳細資訊，請參閱 [Handoff 簡介](~/ios/platform/handoff.md)。

下列程式碼範例將示範如何 [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 在覆寫中將屬性設為 `true` [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ：

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

同樣地，當深層連結所代表的頁面離開時， [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) 可以將屬性設定為 `false` 。 在 iOS 和 Android 上，這 [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) 會停止針對搜尋索引進行公告的實例，而且只有在 ios 上，它也會停止通告 `AppLinkEntry` 實例以進行遞交。 這可以在覆寫中完成 [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) ，如下列程式碼範例所示：

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

在 iOS 上，應用程式專屬的資料可在編製頁面索引時儲存。 這是藉由將資料新增至 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 集合來達成，這是用 `Dictionary<string, string>` 來儲存用於提交的索引鍵/值組的。 Handoff 這種方法讓使用者可以在其中一部裝置上開始活動，然後在另一部裝置上接續該活動 (以使用者的 iCloud 帳戶識別)。 下列程式碼顯示了儲存應用程式專屬索引鍵/值組的範例：

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

儲存在集合中的值 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 會儲存在索引頁面的中繼資料中，並且會在使用者按下包含深層 (連結的搜尋結果時還原，或在使用遞交來查看另一個已登入裝置) 的內容時還原。

此外，您可指定下列索引鍵的值：

- `contentType`：`string`，指定索引內容的統一類型識別碼。 用於此值的建議慣例是包含索引內容的頁面類型名稱。
- `associatedWebPage`：`string`，若索引內容也可在 Web 上檢視，或應用程式支援 Safari 的深層連結，即代表要瀏覽的網頁。
- `shouldAddToPublicIndex`：`true` 或 `false` 的 `string`，控制是否要將索引內容新增到 Apple 的公用雲端索引，以向尚未在 iOS 裝置上安裝應用程式的使用者呈現。 不過，即使內容已為公用索引經過設定，也不代表會自動新增到 Apple 的公用雲端索引。 如需詳細資料，請參閱[公用搜尋索引](~/ios/platform/search/nsuseractivity.md)。 請注意， `false` 在將個人資料新增至集合時，應該將此機碼設為 [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) 。

> [!NOTE]
> Android 平台未使用 `KeyValues` 集合。

如需遞交的詳細資訊，請參閱 [Handoff 簡介](~/ios/platform/handoff.md)。

## <a name="related-links"></a>相關連結

- [深層連結 (範例)](/samples/xamarin/xamarin-forms-samples/deeplinking)
- [iOS 搜尋 API](~/ios/platform/search/index.md)
- [Android 6.0 中的應用程式連結](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
