---
title: 提供與 Android 支援套件的回溯相容性
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027303"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供與 Android 支援套件的回溯相容性

片段的實用性會受到限制，而不會有與 Android 之前3.0 （API 層級11）裝置的回溯相容性。 為了提供這項功能，Google 引進了[支援程式庫](https://developer.android.com/sdk/compatibility-library.html)（原先在發行時稱為*android 相容性程式庫*），它會從較新版本的 android 反向移植一些 Api 到舊版的 android。 這是 Android 支援套件，可讓執行 Android 1.6 （API 層級4）的裝置2.3.3 至 Android。 （API 層級10）。

> [!NOTE]
> 只有 `ListFragment` 和 `DialogFragment` 可透過 Android 支援套件取得。 Android 支援套件中不支援任何其他片段子類別，例如 `PreferenceFragment,`。 它們無法在 Android 之前的3.0 應用程式中使用。 

## <a name="adding-the-support-package"></a>新增支援封裝

Android 支援套件不會自動新增至 Xamarin Android 應用程式。 Xamarin 提供[Android 支援程式庫 V4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)，以簡化將支援程式庫新增至 Xamarin Android 應用程式的工作。若要將支援套件包含在您的 Xamarin 中，請在您的 Xamarin Android 專案中包含[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)元件，如下列螢幕擷取畫面所示： 

[![要新增至專案的 Android 支援程式庫 v4 套件的螢幕擷取畫面](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

執行這些步驟之後，就可以在舊版的 Android 中使用片段。 在這些舊版中，片段 Api 現在會使用相同的功能，但有下列例外狀況： 

- &ndash; 應用程式不再需要以 Android 3.0 或更高版本為目標，請**變更 android 的最低版本**，如下所示： 

    [![在 Android 資訊清單下設定之最小 Android 目標的螢幕擷取畫面](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **擴充 FragmentActivity** &ndash; 裝載片段的活動現在必須繼承自 `Android.Support.V4.App.FragmentActivity`，而不是 `Android.App.Activity`。 

- &ndash; 繼承自 `Android.App.Fragment` 的類別**更新命名空間**，現在必須從 `Android.Support.V4.App.Fragment` 繼承。 移除原始程式碼檔案頂端的 using 語句 "`using Android.App;`"，並將它取代為 "`using Android.Support.V4.App`"。 

- **使用 SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` 會公開必須用來取得 `FragmentManager` 參考的 `SupportingFragmentManager` 屬性。 例如： 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

有了這些變更之後，就可以在 Android 1.6 或2.x 上執行以片段為基礎的應用程式，以及 Honeycomb 和霜淇淋三明治。 

## <a name="related-links"></a>相關連結

- [Android 支援程式庫 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
