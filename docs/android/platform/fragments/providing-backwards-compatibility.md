---
title: 提供回溯相容性與 Android 支援封裝
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 18dac665eec1c5d3ac64065c37e73022670c1ba5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108843"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供回溯相容性與 Android 支援封裝

片段的用處會受限於而不需要回溯預先 Android 3.0 (API 層級 11) 裝置的相容性。 若要提供這項功能，Google 導入了[支援程式庫](http://developer.android.com/sdk/compatibility-library.html)(原本稱為*Android 的相容性程式庫*發行時) 的反向移植一些從較新版本的 Api較舊版本的 Android android。 它是 Android 支援套件，可讓執行 Android 2.3.3 Android 1.6 （API 層級 4） 的裝置。 （API 層級 10）。

> [!NOTE]
> 只有`ListFragment`而`DialogFragment`可透過 Android 的支援封裝。 沒有其他片段子類別，例如`PreferenceFragment,`支援 Android 支援封裝中。 它們不適用於預先 Android 3.0 的應用程式。 


## <a name="adding-the-support-package"></a>新增支援封裝

Android 支援封裝不會自動新增至 Xamarin.Android 應用程式。 Xamarin 提供[Android 支援程式庫 v4 NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)來簡化將支援程式庫新增至 Xamarin.Android 應用程式。若要將包含您的 Xamarin.Android 應用程式包含支援封裝[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)元件到 Xamarin.Android 專案中，如下列螢幕擷取畫面所示： 

[![要加入至專案的螢幕擷取畫面的 Android 支援程式庫 v4 封裝](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

在執行這些步驟後，就可以在舊版的 Android 中使用片段。 片段 Api 會處理相同現在在這些舊的版本，但有下列例外狀況： 

-   **變更最低 Android 版本**&ndash;應用程式不再需要為目標 Android 3.0 或更新版本，如下所示： 

    [![在 Android 資訊清單所設定的螢幕擷取畫面的最低 Android 目標](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **擴充 FragmentActivity** &ndash;要用來裝載片段的活動現在必須繼承自`Android.Support.V4.App.FragmentActivity`，而不是從`Android.App.Activity`。 

-   **更新命名空間**&ndash;類別繼承自`Android.App.Fragment`現在必須繼承自`Android.Support.V4.App.Fragment`。 移除使用陳述式 」 `using Android.App;` "上方的原始程式碼檔案，並將它取代為" `using Android.Support.V4.App` 」。 

-   **使用 SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity`公開`SupportingFragmentManager`必須用來取得參考的屬性`FragmentManager`。 例如:  

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

與這些變更之後，它可以執行的片段型應用程式，以及 Honeycomb 和 Ice Cream Sandwich 上 Android 1.6 或 2.x。 


## <a name="related-links"></a>相關連結

- [Android 支援程式庫 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
