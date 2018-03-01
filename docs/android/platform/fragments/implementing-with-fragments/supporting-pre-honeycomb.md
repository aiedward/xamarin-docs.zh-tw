---
title: "支援預先 Honeycomb Android 使用支援封裝"
ms.topic: article
ms.prod: xamarin
ms.assetid: DACD0C14-5DDF-7BDE-6844-80550D301307
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 9ffa3733908affb8a6f3cc5a1ae2e83c5a15f0f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="supporting-pre-honeycomb-android-using-support-packages"></a>支援預先 Honeycomb Android 使用支援封裝

*Android 支援封裝*回通訊埠部分的新 API 的程式庫所組成&ndash;例如片段&ndash;較舊版本的 Android。 因此藉由新增 Android 支援套件，我們可以執行我們的應用程式 Android 2.3 裝置上的下列畫面所示：

![片段逐步解說螢幕擷取畫面](supporting-pre-honeycomb-images/00.png)

![詳細說明活動螢幕擷取畫面](supporting-pre-honeycomb-images/01.png)

<a name="Adding_the_Support_Package" />

## <a name="adding-the-support-package"></a>新增支援套件

Android 支援封裝不會自動加入至 Xamarin.Android 應用程式。 Xamarin 提供[Android 支援的程式庫 v4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)來簡化支援程式庫加入 Xamarin.Android 應用程式。
若要將支援封裝包含您的應用程式包含的 Xamarin.Android [Android 支援的程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)元件到 Xamarin.Android 專案中，如下列螢幕擷取畫面所示：

![新增 Android 支援程式庫 v4 套件](supporting-pre-honeycomb-images/02.png)

加入封裝之後變更為 Android 2.2 或更高的目標 framework:

![變更目標 Framework 應用程式開發介面層級的螢幕擷取畫面](supporting-pre-honeycomb-images/03.png)

此外，請確定最小的 Android 版本為目標的相同應用程式開發介面層級：

![設定 Android 最小版本的螢幕擷取畫面](supporting-pre-honeycomb-images/04.png)


<a name="Change_MainActivity_to_derive_from_FragmentActivity" />

### <a name="change-mainactivity-to-derive-from-fragmentactivity"></a>變更衍生自 FragmentActivity MainActivity

使用片段任何活動都必須繼承自`Xamarin.Support.V4.App.FragmentActivity`。 這個類別是 android 的支援封裝不可或缺的一部分，因為它可讓活動，不限於任何版本所裝載的片段。 `MainActivity` 必須只有一個小變更，現在必須繼承自`Android.Support.V4.App.FragmentActivity`:

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

<a name="Change_DetailsActivity_to_derive_from_FragmentActivity" />

### <a name="change-detailsactivity-to-derive-from-fragmentactivity"></a>變更衍生自 FragmentActivity DetailsActivity

`DetailsActivity` 也會變更從`Activity`至`FragmentActivity`。 做為`FragmentManager`是與 Android 前 Honeycomb 版本不相容，Android 支援封裝包含包裝函式類別`SupportFragmentManager`，可提供回溯相容性。 每個`FragmentActivity`具有`SupportFragmentManager`屬性，以及`DetailsActivity`變更為使用`SupportFragmentManager`而不是`FragmentManager`:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("index", 0);
       var details = DetailsFragment.NewInstance(index);
       var fragmentTransaction = SupportFragmentManager.BeginTransaction(); // Notice the change from FragmentManager to SupportFragmentManager
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

我們在完成這些變更之後，我們現在有應用程式，可以執行在 Android 1.6 和更新版本，並使用片段來調整我們的 UI，我們的目標裝置的大小。


## <a name="related-links"></a>相關連結

- [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4)
