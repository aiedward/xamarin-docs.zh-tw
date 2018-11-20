---
title: 使用 App Center 自動化 Xamarin.Forms 測試
description: Xamarin UITest 元件可搭配 Xamarin.Forms 使用來撰寫 UI 測試，以在雲端中的數百個裝置上執行。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: b674db3d-c526-4e31-a9f4-b6d6528ce7a9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/31/2016
ms.openlocfilehash: 215aa88b7e97a67242bd991dde0e66c445b47b0a
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526400"
---
# <a name="automate-xamarinforms-testing-with-app-center"></a>使用 App Center 自動化 Xamarin.Forms 測試

_Xamarin UITest 元件可搭配 Xamarin.Forms 使用來撰寫 UI 測試，以在雲端中的數百個裝置上執行。_

## <a name="overview"></a>總覽

**[App Center 測試](/appcenter/test-cloud/)** \(英文\) 可讓開發人員撰寫適用於 iOS 和 Android 應用程式的自動化使用者介面測試。 經過一些調整後，Xamarin.Forms 應用程式可使用 Xamarin.UITest 進行測試，包括共用相同的測試程式碼。 本文會介紹搭配使用 Xamarin.UITest 和 Xamarin.Forms 的特定提示。

本指南假設您已熟悉 Xamarin.UITest。 建議您參考下列指南以熟悉 Xamarin.UITest：

- [App Center 測試簡介](/appcenter/test-cloud/) \(英文\)
- [UITest 簡介](/appcenter/test-cloud/preparing-for-upload/uitest/) \(英文\)

一旦將 UITest 專案加入到 Xamarin.Forms 解決方案之後，針對 Xamarin.Forms 應用程式撰寫和執行測試的步驟，將會和 Xamarin.Android 或 Xamarin.iOS 應用程式的步驟相同。

## <a name="requirements"></a>需求

請參閱 [Xamarin.UITest](/appcenter/test-cloud/uitest/)，以確認您的專案已做好進行自動化 UI 測試的準備。

## <a name="adding-uitest-support-to-xamarinforms-apps"></a>在 Xamarin.Forms 應用程式中加入 UITest 支援

UITest 可藉由啟用畫面上的控制項，並在使用者一般會與應用程式互動的任何位置執行輸入，以自動化使用者介面。 為啟用可*按下按鈕*或*在方塊中輸入文字*的測試，測試程式碼需要方法來識別畫面上的控制項。

若要讓 UITest 程式碼參考控制項，每個控制項都需要唯一識別碼。 在 Xamarin.Forms 中，設定此識別碼的建議方式是利用 `AutomationId` 屬性，如下所示：

```csharp
var b = new Button {
    Text = "Click me",
    AutomationId = "MyButton"
};
var l = new Label {
    Text = "Hello, Xamarin.Forms!",
    AutomationId = "MyLabel"
};
```

也可以在 XAML 中設定 `AutomationId` 屬性：

```xaml
<Button x:Name="b" AutomationId="MyButton" Text="Click me"/>
<Label x:Name="l" AutomationId="MyLabel" Text="Hello, Xamarin.Forms!" />
```

測試需要的所有控制項 (包括按鈕、文字項目和其值可能需要進行查詢的標籤) 都應加入唯一的 `AutomationId`。

> [!NOTE]
> 請注意，如果嘗試設定 `Element` 的 `AutomationId` 屬性超過一次以上，將會擲回 `InvalidOperationException`。

### <a name="ios-application-project"></a>iOS 應用程式專案

若要在 iOS 上執行測試，必須將 [Xamarin Test Cloud Agent NuGet 套件](https://www.nuget.org/packages/Xamarin.TestCloud.Agent/) \(英文\) 加入到專案。 加入該套件之後，請將下列程式碼複製到 `AppDelegate.FinishedLaunching` 方法：

```csharp
#if ENABLE_TEST_CLOUD
// requires Xamarin Test Cloud Agent
Xamarin.Calabash.Start();
#endif
```

Calabash 組件會使用非公用的 Apple API，這會導致 App Store 拒絕該應用程式。 但如果未在程式碼中明確參考 Calabash 組件，則 Xamarin.iOS 連結器會從最終 IPA 中移除它。

> [!NOTE]
> 發行組建並未包含 `ENABLE_TEST_CLOUD` 編譯器變數，這會導致從應用程式套件組合中移除 Calabash 組件。 但偵錯組建定義了編譯器指示詞，因此連結器無法移除該組件。

下列螢幕擷取畫面顯示針對偵錯組建設定的 `ENABLE_TEST_CLOUD` 編譯器變數：

::: zone pivot="windows"

![](uitest-and-test-cloud-images/12-compiler-directive-vs.png "組建選項")

::: zone-end
::: zone pivot="macos"

![](uitest-and-test-cloud-images/11-compiler-directive-xs.png "編譯器選項")

::: zone-end

### <a name="android-application-project"></a>Android 應用程式專案

不同於 iOS，Android 專案不需要任何特殊的啟始程式碼。

## <a name="writing-uitests"></a>撰寫 UITest

如需撰寫 UITest 的資訊，請參閱 [UITest 文件](/appcenter/test-cloud/preparing-for-upload/uitest/) \(英文\)。 下列步驟為摘要，具體說明建置 [Xamarin.Forms 示範 **UsingUITest**](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/) 的方式。

### <a name="use-automationid-in-the-xamarinforms-ui"></a>在 Xamarin.Forms UI 中使用 AutomationId

Xamarin.Forms 應用程式使用者介面必須處於可撰寫指令碼的狀態，才可以撰寫任何 UITest。 請確定使用者介面中的所有控制項都具有 `AutomationId`，以在測試程式碼中參考它們。

#### <a name="referring-to-the-automationid-in-uitests"></a>在 UITest 中參考 AutomationId

撰寫 UITest 時，`AutomationId` 值會在每個平台上以不同的方式公開：

- **iOS** 會使用 `id` 欄位。
- **Android** 會使用 `label` 欄位。

若要撰寫會在 iOS 和 Android 上尋找 `AutomationId` 的跨平台 UITest，請使用 `Marked` 測試查詢：

```csharp
app.Query(c=>c.Marked("MyButton"))
```

較短形式的 `app.Query("MyButton")` 也適用。

### <a name="adding-a-uitest-project-to-an-existing-solution"></a>將 UITest 專案加入到現有方案

::: zone pivot="windows"

Visual Studio 有提供範本，以協助將 Xamarin.UITest 專案加入到現有的 Xamarin.Forms 方案：

1. 以滑鼠右鍵按一下方案，然後選取 [檔案] > [新增專案]。
1. 從 [Visual C#] 範本中選取 [測試] 類別。 選取 [UI 測試應用程式] > [跨平台] 範本：

    ![新增專案](uitest-and-test-cloud-images/08-new-uitest-project-vs.png "新增專案")

    這會將含有 **NUnit**、**Xamarin.UITest** 和 **NUnitTestAdapter** NuGet 套件的新專案加入到方案：

    ![NuGet 套件管理員](uitest-and-test-cloud-images/09-new-uitest-project-xs.png "NuGet 套件管理員")

    **NUnitTestAdapter** 是可讓 Visual Studio 從 Visual Studio 執行 NUnit 測試的協力廠商測試執行器。

    新專案也包含兩個類別。 **AppInitializer** 包含程式碼，可協助初始化和設定測試。 另一個類別 **Tests** 包含未定案程式碼，可協助開始 UITest。

1. 將來自 UITest 專案的專案參考加入到 Xamarin.Android 專案：

    ![專案參考管理員](uitest-and-test-cloud-images/10-test-apps-vs.png "專案參考管理員")

    這可讓 **NUnitTestAdapter** 從 Visual Studio 執行適用於 Android 應用程式的 UITest。

::: zone-end
::: zone pivot="macos"

您可手動將新的 Xamarin.UITest 專案加入到現有的方案：

1. 選取方案，按一下 [檔案] > [新增專案] 以開始新增專案。 在 [新增專案] 對話方塊中，選取 [跨平台] > [測試] > [Xamarin Test Cloud] > [UI 測試應用程式]：

    ![選擇範本](uitest-and-test-cloud-images/02-new-uitest-project-xs.png "選擇範本")

    這會在方案中加入已具有 **NUnit** 和 **Xamarin.UITest** NuGet 套件的新專案：

    ![Xamarin UITest NuGet 套件](uitest-and-test-cloud-images/03-new-uitest-project-xs.png "Xamarin UITest NuGet 套件")

    新專案也包含兩個類別。 **AppInitializer** 包含程式碼，可協助初始化和設定測試。 另一個類別 **Tests** 包含未定案程式碼，可協助開始 UITest。

1. 選取 [檢視] > [板] > [單元測試] 以顯示 [單元測試] 板。 展開 [UsingUITest] > [UsingUITest.UITests] > [測試應用程式]：

    ![單元測試面板](uitest-and-test-cloud-images/04-unit-test-pad-xs.png "單元測試面板")

1. 以滑鼠右鍵按一下 [測試應用程式]，按一下 [新增應用程式專案]，在出現的對話方塊中選取 iOS 和 Android 專案：

    ![測試應用程式對話方塊](uitest-and-test-cloud-images/05-add-test-apps-xs.png "測試應用程式對話方塊")

    [單元測試] 板現在應該有針對 iOS 和 Android 專案的參考。 這可讓 Visual Studio for Mac 測試執行器在本機針對兩個 Xamarin.Forms 專案執行 UITest。

#### <a name="adding-uitest-to-the-ios-app"></a>將 UITest 加入到 iOS 應用程式

在 Xamarin.UITest 可以運作之前，必須先對 iOS 應用程式執行一些額外的變更：

1. 加入 **Xamarin Test Cloud Agent** NuGet 套件。 以滑鼠右鍵按一下 [套件]，選取 [新增套件]，搜尋 **Xamarin Test Cloud Agent** 的 NuGet，並將它加入到 Xamarin.iOS 專案：

    ![新增 NuGet 套件](uitest-and-test-cloud-images/07-add-test-cloud-agent-xs.png "新增 NuGet 套件")

1. 編輯 **AppDelegate** 類別的 `FinishedLaunching` 方法，以在 iOS 應用程式啟動時初始化 Xamarin Test Cloud Agent，並設定檢視的 `AutomationId` 屬性。 `FinishedLaunching` 方法應類似於下列程式碼範例：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    #if ENABLE_TEST_CLOUD
    Xamarin.Calabash.Start();
    #endif

    global::Xamarin.Forms.Forms.Init();

    LoadApplication(new App());

    return base.FinishedLaunching(app, options);
}
```

::: zone-end

將 Xamarin.UITest 加入到 Xamarin.Forms 方案後，就可以建立 UITest，在本機執行它們，並將它們提交到 Xamarin Test Cloud。

## <a name="summary"></a>總結

透過簡單的機制將 `AutomationId` 公開為測試自動化的唯一檢視識別碼，就能輕鬆使用 **Xamarin.UITest** 來測試 Xamarin.Forms 應用程式。 一旦將 UITest 專案加入到 Xamarin.Forms 解決方案之後，針對 Xamarin.Forms 應用程式撰寫和執行測試的步驟，將會和 Xamarin.Android 或 Xamarin.iOS 應用程式的步驟相同。

如需如何將測試提交至 App Center 測試的相關資訊，請參閱[提交 UITest](/appcenter/test-cloud/preparing-for-upload/uitest/) \(英文\)。 如需 UITest 的詳細資訊，請參閱 [App Center 測試文件](/appcenter/test-cloud/) \(英文\)。

## <a name="related-links"></a>相關連結

- [UITestSample](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)
- [App Center 測試](/appcenter/test-cloud/)
- [Xamarin.UITest](/appcenter/test-cloud/uitest/)
- [NUnit](http://www.nunit.org)
