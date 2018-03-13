---
title: "提供回溯相容性的 Android 支援封裝"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/12/2017
ms.openlocfilehash: 670ec465843bbe819b41a53fff71b01ab78b0059
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供回溯相容性的 Android 支援封裝

片段的用處會受限於而不向後與預先 Android 3.0 (API 層級 11) 裝置的相容性。 為了提供這項功能，Google 導入了[支援程式庫](http://developer.android.com/sdk/compatibility-library.html)(原先稱為*Android Compatibility Library*發行時) 哪些 backports 一些較新版本的應用程式開發介面較舊版本的 Android android。 它是 Android 支援套件，可讓執行 Android 2.3.3 Android 1.6 （API 層級 4） 的裝置。 （API 層級 10）。

> [!NOTE]
> 只有`ListFragment`和`DialogFragment`可透過 Android 的支援封裝。 沒有其他片段子類別，例如`PreferenceFragment,`支援 Android 支援封裝中。 它們不適用於預先 Android 3.0 的應用程式。 


## <a name="adding-the-support-package"></a>新增支援套件

Android 支援封裝不會自動加入至 Xamarin.Android 應用程式。 Xamarin 提供[Android 支援的程式庫 v4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)來簡化支援程式庫加入 Xamarin.Android 應用程式。若要將支援封裝包含您的應用程式包含的 Xamarin.Android [Android 支援的程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)元件到 Xamarin.Android 專案中，如下列螢幕擷取畫面所示： 

[![螢幕擷取畫面的 Android 支援程式庫 v4 封裝加入至專案](providing-backwards-compatibility-images/02.png)](providing-backwards-compatibility-images/02.png#lightbox)

在執行這些步驟後，就能夠在舊版的 Android 使用片段。 片段 Api 運作相同現在在這些舊的版本，但有下列例外狀況： 

-   **變更最低的 Android 版本**&ndash;應用程式不再需要為目標 Android 3.0 或更新版本，如下所示： 

    [![正在設定應用程式屬性底下的螢幕擷取畫面的最小值 Android 目標](providing-backwards-compatibility-images/03.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **擴充 FragmentActivity** &ndash;裝載片段的活動必須現在是繼承自`Android.Support.V4.App.FragmentActivity`，而不是從`Android.App.Activity`。 

-   **更新命名空間**&ndash;類別繼承自`Android.App.Fragment`現在必須繼承自`Android.Support.V4.App.Fragment`。 使用移除陳述式" `using Android.App;` "上方的原始程式碼檔案，並將它取代為" `using Android.Support.V4.App` "。 

-   **使用 SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity`公開`SupportingFragmentManager`必須用來取得參考的屬性`FragmentManager`。 例如:  

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

在進行這些變更，則可能在 Android 1.6 或 2.x 和 Honeycomb 和冰淇淋三明治上執行的片段型應用程式。 


## <a name="related-links"></a>相關連結

- [Android 支援程式庫 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
