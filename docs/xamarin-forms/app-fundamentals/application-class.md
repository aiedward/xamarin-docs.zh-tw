---
title: Xamarin.Forms應用程式類別
description: 本文說明預設 App 類別的功能，此類別包含要設定為應用程式初始頁面的屬性，以及用來跨生命週期狀態變更來儲存簡單值的持續性字典。
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
ms.custom: video
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e761830fb08662a82a8bf98b4544aafaa6eccf7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573348"
---
# <a name="xamarinforms-app-class"></a>Xamarin.Forms應用程式類別

`Application` 基底類別提供下列功能，它們公開於您專案的預設 `App` 子類別：

* `MainPage` 屬性，這是要設定應用程式起始頁的位置。
* 用來儲存生命週期狀態變更之間簡單值的持續性[ `Properties` 字典](#properties-dictionary)。
* 靜態的 `Current` 屬性，其中包含目前應用程式物件的參考。

它也會公開[生命週期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)，例如、和，以及強制回應 `OnStart` `OnSleep` `OnResume` 導覽事件。

根據您選擇的範本，`App` 類別可以用兩種方式之一來定義：

* **C#** 或
* **XAML & C#**

若要使用 XAML 建立 **App** 類別，預設的 **App** 類別必須取代為 XAML **App** 類別和相關聯的程式碼後置，如下列程式碼範例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

</Application>
```

下列程式碼範例顯示相關聯的程式碼後置：

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

除了設定 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性之外，程式碼後置還必須呼叫 `InitializeComponent` 方法來載入及剖析相關聯的 XAML。

## <a name="mainpage-property"></a>MainPage 屬性

`Application` 類別的 `MainPage` 屬性會設定應用程式的根頁面。

比方說，您可以在 `App` 類別中建立邏輯，以視使用者登入與否顯示不同的頁面。

`MainPage` 屬性應該設定於 `App` 建構函式中，

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

## <a name="properties-dictionary"></a>Properties 字典

`Application` 子類別有靜態的 `Properties` 字典可以用來儲存資料，特別是用於 `OnStart`、`OnSleep` 和 `OnResume` 方法。 您可以使用，從程式碼中的任何位置存取這項功能 Xamarin.Forms `Application.Current.Properties` 。

`Properties` 字典使用 `string` 索引鍵並儲存 `object` 值。

例如，您可以在程式碼中的任何位置設定持續性的 `"id"` 屬性 (項目選取時，在頁面的 `OnDisappearing` 方法，或在 `OnSleep` 方法中) 如下所示：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在 `OnStart` 或 `OnResume` 方法中，您便可以使用此值，以某種方式重新建立使用者的體驗。 `Properties` 字典會儲存 `object`，因此您需要先轉換其值才能使用。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

請務必先檢查索引鍵是否存在才存取它，以避免發生意外的錯誤。

> [!NOTE]
> `Properties` 字典只可序列化基本類型以便儲存。 嘗試儲存其他類型 (例如 `List<string>`) 可能會以無訊息模式失敗。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持續性

`Properties` 字典會自動儲存至裝置。
新增至字典的資料將在應用程式從背景返回時可用，或甚至在它重新啟動之後才可使用。

Xamarin.Forms1.4 在類別上引進了其他方法 `Application` - `SavePropertiesAsync()` -可以呼叫它來主動保存 `Properties` 字典。 這樣可讓您在重要更新之後儲存屬性，而不必冒著它們因為損毀或被 OS 終止 OS 而未序列化的風險。

在 `Properties` ** Xamarin.Forms 建立 Mobile Apps**中，您可以使用書籍[第 6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)、 [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)和[20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)，以及相關聯的[範例](https://github.com/xamarin/xamarin-forms-book-preview-2)中的字典來尋找參考。

## <a name="the-application-class"></a>Application 類別

完整的 `Application` 類別實作顯示如下供您參考：

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}
```

這個類別接著便會在每個平台特定專案中具現化，並傳遞至 `LoadApplication` 方法，在這裡會載入 `MainPage` 並顯示給使用者。
每個平台的程式碼顯示在下列各節。 最新的 Xamarin.Forms 解決方案範本已包含所有此程式碼，預先設定了您的應用程式。

### <a name="ios-project"></a>iOS 專案

iOS `AppDelegate` 類別繼承自 `FormsApplicationDelegate`。 它應該：

* 使用 `App` 類別的執行個體呼叫 `LoadApplication`。

* 一律傳回 `base.FinishedLaunching (app, options);`。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Android 專案

Android `MainActivity` 繼承自 `FormsAppCompatActivity`。 在 `OnCreate` 覆寫中，使用 `App` 類別的執行個體呼叫 `LoadApplication` 方法。

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>適用於 Windows 10 的通用 Windows 平台 (UWP)

在 UWP 專案中的主頁面應該繼承自 `WindowsPage`：

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C # 程式碼後置結構必須呼叫 `LoadApplication` ，才能建立的實例 Xamarin.Forms `App` 。 請注意，明確地使用應用程式命名空間來限定，是很好的作法， `App` 因為 UWP 應用程式也有自己的 `App` 類別與無關 Xamarin.Forms 。

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

請注意，必須從 UWP 專案中的 **App.xaml.cs** 呼叫 `Forms.Init()`。

如需詳細資訊，請參閱[設定 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)，其中包含將 uwp 專案新增至不以 UWP 為目標之現有解決方案的步驟 Xamarin.Forms 。

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
