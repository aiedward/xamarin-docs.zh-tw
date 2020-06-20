---
title: 疑難排解
description: 常見的錯誤狀況和解決方式
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 237dfd637d5456636afd19ea0175cb388773d273
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135911"
---
# <a name="troubleshooting"></a>疑難排解

_常見的錯誤狀況和解決方式_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>錯誤：「找不到相容的版本 ...」 Xamarin.Forms

更新**Package Console** Xamarin.Forms 解決方案或 Xamarin.Forms Android 應用程式專案中的所有 NuGet 套件時，套件主控台視窗中可能會出現下列錯誤：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>造成此錯誤的原因為何？

Visual Studio for Mac （或 Visual Studio）可能表示更新適用于 Xamarin.Forms NuGet 套件*及其所有*相依性。 在 Xamarin Studio 中，解決方案的 [**套件**] 節點看起來可能像這樣（版本號碼可能不同）：

![](images/updates-available.png "Android Project Packages Folder")

如果您嘗試更新_所有_的封裝，可能會發生此錯誤。

這是因為當 Android 專案設定為 Android 6.0 （API 23）的目標/編譯版本時，會對 Xamarin.Forms android 支援套件的*特定*版本進行硬相依性。 雖然這些封裝的更新版本可能可供使用，但 Xamarin.Forms 不一定會與其相容。

在此情況下，您應該_只_更新 **Xamarin.Forms** 封裝，因為這可確保相依性會維持在相容的版本上。 您已新增至專案的其他套件也可能會個別更新，只要它們不會導致 Android 支援套件更新。

> [!NOTE]
> 如果您使用 Xamarin.Forms 2.3.4 或更高版本，**且**您的 android 專案的目標/編譯版本設定為 ANDROID 7.0 （API 24）或更高版本，則不會再套用上述的硬性相依性，而且您可能會在封裝之外獨立更新支援套件 Xamarin.Forms 。

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>修正：移除所有封裝，然後重新加入Xamarin.Forms

如果**支援**套件已更新為不相容的版本，最簡單的解決方法是：

1. 手動刪除 Android 專案中的所有 NuGet 套件，然後
2. 重新加入 **Xamarin.Forms** 套件。

這會自動下載其他套件的*正確*版本。

若要查看此程式的影片，請參閱此[論壇文章](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
