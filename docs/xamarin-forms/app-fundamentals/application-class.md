---
title: Xamarin.Forms 應用程式類別
description: 這篇文章說明功能的預設應用程式類別，其中包含要設定應用程式的初始頁面的屬性，以及持續性的字典儲存簡單的值在生命週期狀態變更。
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 6de4380f2ce2d19df4ff912b7c86b75ca9e7821b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38999057"
---
# <a name="xamarinforms-app-class"></a>Xamarin.Forms 應用程式類別

`Application`基底類別提供下列功能，其公開於您專案的預設`App`子類別：

* A`MainPage`屬性，這是要設定應用程式的起始頁的位置。
* 持續性[`Properties`字典](#Properties_Dictionary)來儲存整個生命週期狀態變更的簡單值。
* 靜態`Current`屬性，其中包含目前的應用程式物件的參考。

它也會公開[存留週期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)這類`OnStart`， `OnSleep`，和`OnResume`以及強制回應導覽事件。

根據的範本選擇，`App`類別可定義於兩種方式之一：

* **C#**，或
* **XAML 和 C#**

若要建立**應用程式**類別使用 XAML，預設值**應用程式**類別必須取代 XAML**應用程式**類別和相關聯的程式碼後置，如下列程式碼範例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

下列程式碼範例會顯示相關聯的程式碼後置：

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

以及設定[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)屬性，程式碼後置還必須呼叫`InitializeComponent`方法來載入及剖析相關聯的 XAML。

## <a name="mainpage-property"></a>MainPage 屬性

`MainPage`屬性上的`Application`類別設定的應用程式的 [根] 頁面。

比方說，您可以在其中建立邏輯，在您`App`類別，以顯示不同的頁面，取決於是否使用者登入與否。

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

`Application`子類別有靜態`Properties`可以用來儲存資料，特別是，以用於字典`OnStart`， `OnSleep`，和`OnResume`方法。 這可從任何地方在 Xamarin.Forms 中的程式碼使用`Application.Current.Properties`。

`Properties` Dictionary 會使用`string`金鑰及儲存區`object`值。

例如，您可以在其中設定持續`"id"`您的程式碼中的任何位置的屬性 (項目中選取時，頁面的`OnDisappearing`方法，或在`OnSleep`方法) 如下所示：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在 `OnStart`或`OnResume`您接著可以使用此值來重新建立使用者的體驗，以某種方式的方法。 `Properties`字典存放區`object`s，因此您需要使用它之前轉換其值。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

務必檢查索引鍵的目前狀態存取，以免發生意外的錯誤之前。

> [!NOTE]
> `Properties`字典只可以序列化為儲存體的基本類型。 嘗試儲存其他類型 (例如`List<string>`) 可能會以無訊息模式失敗。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持續性

`Properties`字典就會自動儲存至裝置。
當應用程式傳回從背景，或甚至在它重新啟動之後，才加入至字典的資料將可使用。

Xamarin.Forms 1.4 在中引進額外的方法`Application`類別- `SavePropertiesAsync()` -這可以呼叫以主動保存`Properties`字典。 這可讓您儲存重要的更新之後的屬性，而不是它們不進行序列化時因為損毀或被終止 OS 的風險。

您可以尋找使用的參考`Properties`中的字典**使用 Xamarin.Forms 建立行動應用程式**書籍章節[6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)， [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)，以及[20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)，並在相關聯[範例](https://github.com/xamarin/xamarin-forms-book-preview-2)。



## <a name="the-application-class"></a>Application 類別

完整`Application`類別實作會如下所示的參考：

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

這個類別是然後具現化每個平台專屬專案中，傳遞至`LoadApplication`方法，這是 where`MainPage`會載入並顯示給使用者。
每個平台的程式碼是以下列各節所示。 最新的 Xamarin.Forms 方案範本已經包含所有此程式碼，預先設定的應用程式。


### <a name="ios-project"></a>iOS 專案

IOS`AppDelegate`類別繼承自`FormsApplicationDelegate`。 它應該：

* 呼叫`LoadApplication`的執行個體`App`類別。

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

Android`MainActivity`繼承自`FormsAppCompatActivity`。 在 `OnCreate`覆寫`LoadApplication`方法呼叫的執行個體`App`類別。

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

### <a name="universal-windows-project-uwp-for-windows-10"></a>適用於 Windows 10 的通用 Windows 專案 (UWP)

請參閱[安裝 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)如需有關 Xamarin.Forms UWP 支援資訊。

在 UWP 專案的主頁面應該繼承自`WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C# 程式碼後置的建構必須呼叫`LoadApplication`來建立您的 Xamarin.Forms 的執行個體`App`。 請注意，它是很好的作法，明確地使用應用程式命名空間來限定`App`因為 UWP 應用程式也有自己`App`Xamarin.Forms 不相關的類別。

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

請注意，`Forms.Init()`必須呼叫**App.xaml.cs**周圍第 63 行。
