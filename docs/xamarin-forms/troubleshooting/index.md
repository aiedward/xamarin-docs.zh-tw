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
ms.openlocfilehash: 93cab36b21e2fe73a0e6890140b5ebaeb32f7951
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760038"
---
# <a name="troubleshooting"></a>疑難排解

_常見的錯誤狀況和解決方式_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>錯誤：「找不到 Xamarin 的版本。與相容的表單 ...」

更新 Xamarin 中的所有 Nuget 套件時，或在 Xamarin 的 Android 應用程式專案中，可能會在**套件主控台**視窗中出現下列錯誤：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>造成此錯誤的原因為何？

Visual Studio for Mac （或 Visual Studio）可能表示更新適用于 Xamarin. form Nuget 套件*及其所有*相依性。 在 Xamarin Studio 中，解決方案的 [**套件**] 節點看起來可能像這樣（版本號碼可能不同）：

![](images/updates-available.png "Android 專案套件資料夾")

如果您嘗試更新_所有_的封裝，可能會發生此錯誤。

這是因為當 Android 專案設定為 Android 6.0 （API 23）或更低版本的目標/編譯時，Xamarin 就會對*特定*版本的 android 支援套件進行硬相依性。 雖然這些套件的更新版本可能可供使用，但是 Xamarin 不一定會與其相容。

在此情況下，您應該_只_更新**Xamarin Forms**封裝，因為這可確保相依性會維持在相容的版本上。 您已新增至專案的其他套件也可能會個別更新，只要它們不會導致 Android 支援套件更新。

> [!NOTE]
> 如果您使用的是2.3.4 或更高版本 **，且**您的 android 專案的目標/編譯版本設定為 android 7.0 （API 24）或更高版本，則不會再套用上述的硬性相依性，而且您可以獨立更新支援封裝Xamarin. Forms 封裝。

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>補丁移除所有套件，然後重新加入 Xamarin. Forms

如果**支援**套件已更新為不相容的版本，最簡單的解決方法是：

1. 手動刪除 Android 專案中的所有 Nuget 套件，然後
2. 重新加入**Xamarin. Forms**封裝。

這會自動下載其他套件的*正確*版本。

若要查看此程式的影片，請參閱此[論壇文章](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
