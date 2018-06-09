---
title: Xamarin.Forms 應用程式類別
description: 這篇文章說明功能的預設應用程式類別，其中包含屬性設定為應用程式的起始頁，持續性的字典值和儲存簡單整個生命週期的狀態變更。
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 15fb866d2cde9409f401d6d021b22b8cb0468795
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240537"
---
# <a name="xamarinforms-app-class"></a>Xamarin.Forms 應用程式類別

`Application`基底類別提供下列功能，在您的專案預設會公開`App`子類別：

* A`MainPage`屬性，這是設定應用程式的起始頁的位置。
* 持續性[`Properties`字典](#Properties_Dictionary)來儲存整個生命週期的狀態變更的簡單值。
* 靜態`Current`屬性，其中包含目前的應用程式物件的參考。

它也會公開[存留週期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)例如`OnStart`， `OnSleep`，和`OnResume`以及強制回應巡覽事件。

在您選擇根據哪一個範本，`App`類別可定義於兩種方式之一：

* **C#**，或
* **XAML 和 C#**

若要建立**應用程式**類別使用 XAML，預設值**應用程式**類別都必須取代 XAML**應用程式**類別和相關聯的程式碼後置，如下列程式碼範例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

下列程式碼範例會顯示相關程式碼後置：

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

設定以及[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)屬性，程式碼後置必須也會呼叫`InitializeComponent`方法來載入及剖析相關聯的 XAML。

## <a name="mainpage-property"></a>MainPage 屬性

`MainPage`屬性`Application`類別設定的根頁面的 應用程式。

例如，您可以建立邏輯中的您`App`類別來顯示不同的頁面，取決於是否使用者已登入與否。

`MainPage`屬性應該設定`App`建構函式

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>屬性字典

`Application`子類別有靜態`Properties`字典可用來儲存資料，特別用於`OnStart`， `OnSleep`，和`OnResume`方法。 這可從任何位置，在 Xamarin.Forms 中的程式碼使用`Application.Current.Properties`。

`Properties`字典會使用`string`金鑰及儲存區`object`值。

比方說，您可以設定持續`"id"`您的程式碼中的任何位置的屬性 (項目中選取時，頁面`OnDisappearing`方法，或在`OnSleep`方法) 以這種方式：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在`OnStart`或`OnResume`您接著可以使用此值來重新建立使用者的經驗，在某些方面的方法。 `Properties`字典存放區`object`s，因此您需要使用它之前轉換其值。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

一定要檢查存在的機碼才能存取，以避免發生意外的錯誤。

> [!NOTE]
> `Properties`字典只能序列化存放裝置的基本類型。 嘗試儲存其他類型 (例如`List<string>`) 可能會以無訊息模式失敗。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持續性

`Properties`字典就會自動儲存到裝置。
當應用程式會傳回與背景或重新啟動時，即使資料加入至字典將可使用。

Xamarin.Forms 1.4 上導入額外的方法`Application`類別- `SavePropertiesAsync()` -主動保存呼叫的目標`Properties`字典。 這可讓您儲存重要的更新之後的內容，而不是它們未取得序列化出因為損毀或遭到刪除作業系統的風險。

您可以找到參考使用`Properties`字典中的**建立行動應用程式使用 Xamarin.Forms**書籍章節[6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)， [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)，和[20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)，和在相關聯[範例](https://github.com/xamarin/xamarin-forms-book-preview-2)。



## <a name="the-application-class"></a>Application 類別

完整`Application`類別實作如下所示的參考：

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

這個類別會具現化每個平台專屬專案中，然後傳遞至`LoadApplication`方法即 where`MainPage`載入及顯示給使用者。
每個平台程式碼是以下列各節所示。 最新的 Xamarin.Forms 方案範本已包含所有此程式碼，預先設定的應用程式。


### <a name="ios-project"></a>iOS 的專案

IOS`AppDelegate`類別會繼承自`FormsApplicationDelegate`。 它應該：

* 呼叫`LoadApplication`與執行個體`App`類別。

* 一律傳回`base.FinishedLaunching (app, options);`。

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

在 Android`MainActivity`現在繼承自`FormsApplicationActivity`。 在`OnCreate`覆寫`LoadApplication`方法呼叫的執行個體`App`類別。

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

> [!NOTE]
> 沒有在較新[ `FormsAppCompatActivity` ](~/xamarin-forms/platform/android/appcompat.md)基底類別，可用來更妥善支援 Android 材料設計。
> 這會在未來，成為預設 Android 範本，但是您可以依照[這些指示](~/xamarin-forms/platform/android/appcompat.md)更新現有的 Android 應用程式。

### <a name="universal-windows-project-uwp-for-windows-10"></a>適用於 Windows 10 的通用 Windows 專案 (UWP)

請參閱[安裝 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)UWP 支援 Xamarin.Forms 中的相關資訊。

UWP 專案中的主頁面應該繼承自`WindowsPage`。 這表示 XAML 和 C# 的`MainPage`參考`FormsApplicationPage`類別所示。

XAML 會使用自訂的命名空間，讓根項目會反映`FormsApplicationPage`類別：

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C# 程式碼後置的建構必須呼叫`LoadApplication`來建立執行個體的程式 Xamarin.Forms `App`。 請注意，它是很好的作法，明確地使用應用程式命名空間來限定`App`因為 UWP 應用程式也有自己`App`Xamarin.Forms 無關的類別。

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

請注意，`Forms.Init()`必須呼叫**App.xaml.cs**大約第 63。
