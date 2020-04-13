---
title: 提供與 Android 支援套件的向後相容性
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027303"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供與 Android 支援套件的向後相容性

片段的有用性將受到限制,而不會向後相容前 Android 3.0 (API 級別 11) 設備。 為了提供此功能,Google 引入了[支援庫](https://developer.android.com/sdk/compatibility-library.html)(最初在發佈時稱為*Android 相容性庫*),將一些 API 從較新版本的 Android 備份到舊版本的 Android。 它是Android支援包,使運行Android 1.6(API級別4)的設備到Android2.3.3。 (API 級別 10)。

> [!NOTE]
> 只有`ListFragment``DialogFragment`和 可通過 Android 支援包獲得。 Android 支援套件中不支援其他片段子類`PreferenceFragment,`, 如 。 它們在安卓前 3.0 應用程式中不起作用。 

## <a name="adding-the-support-package"></a>新增支援套件

Android 支援包不會自動添加到 Xamarin.Android 應用程式。 Xamarin 提供[Android 支援庫 v4 NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/),以簡化將支援庫添加到 Xamarin.Android 應用程式。要將支援套件包含在 Xamarin.Android 應用程式中,請將[Android 支援庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)元件包含在 Xamarin.Android 專案中,如下圖所示: 

[![新增到專案中的 Android 支援函式庫 v4 套件的螢幕擷圖](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

執行這些步驟后,可以在早期版本的 Android 中使用片段。 片段 API 現在在這些早期版本中將同樣工作,但以下例外情況除外: 

- **更改最小 Android 版本**&ndash;應用程式不再需要以 Android 3.0 或更高版本為目標,如下所示: 

    [![在 Android 清單下設定的最低 Android 目標的螢幕截圖](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **擴展片段活動**&ndash;承載片段的活動現在必須`Android.Support.V4.App.FragmentActivity`從 繼承,`Android.App.Activity`而不是從繼承。 

- **更新**&ndash;繼承`Android.App.Fragment`的 命名空間類現在`Android.Support.V4.App.Fragment`必須從 繼承。 刪除原始碼檔頂部的`using Android.App;`using 語句",並將其替換為" `using Android.Support.V4.App` 

- **使用支援片段管理員**&ndash;`Android.Support.V4.App.FragmentActivity`公開必須`SupportingFragmentManager`用於獲取對`FragmentManager`的引用的屬性。 例如： 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

有了這些更改,就可以在 Android 1.6 或 2.x 以及蜂巢和霜淇淋三明治上運行基於片段的應用程式。 

## <a name="related-links"></a>相關連結

- [安卓支援庫 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
