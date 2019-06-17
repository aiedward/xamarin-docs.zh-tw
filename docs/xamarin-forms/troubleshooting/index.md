---
title: 疑難排解
description: 常見的錯誤狀況，以及如何解決這些問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850432"
---
# <a name="troubleshooting"></a>疑難排解

_常見的錯誤狀況，以及如何解決這些問題_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>錯誤：「 找不到 Xamarin.Forms 的版本與相容...」

下列的錯誤可以出現在**套件主控台**視窗更新所有 Nuget 套件或 Xamarin.Forms Android 應用程式專案中的 Xamarin.Forms 方案時：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>造成此錯誤的原因為何？

Visual Studio for Mac （或 Visual Studio） 可能表示更新可供使用 Xamarin.Forms Nuget 套件*及其所有相依性*。 在 Xamarin Studio 中，解決方案的**封裝**節點可能看起來像這樣 （版本號碼可能不同）：

![](images/updates-available.png "Android 專案的 Packages 資料夾")

如果您嘗試更新，可能會發生此錯誤_所有_封裝。

這是因為 Android 專案設為 Android 6.0 (API 23) 的目標/編譯版本或以下，Xamarin.Forms 對硬式相依性*特定*的 Android 版本支援的套件。 雖然可能可以使用這些套件的更新的版本，不一定相容與其 Xamarin.Forms。

在此情況下，您應該更新_僅_ **Xamarin.Forms**封裝，因為這可確保在相容的版本上，維持相依性。 您已加入至專案的其他套件可能也更新個別，只要它們不會使 Android 支援套件，以更新。


> [!NOTE]
> 如果您使用 Xamarin.Forms 2.3.4 或更新版本**和**Android 專案的目標/編譯版本設為 Android 7.0 (API 24) 或更高，則不會再上述硬式相依性套用，而且您可能會更新支援獨立 Xamarin.Forms 套件的封裝。


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>修正：移除所有的封裝，再重新加入 Xamarin.Forms

如果**Xamarin.Android.Support**套件已更新為不相容的版本，最簡單的修正方法是：

1. 手動刪除然後在 Android 專案中，所有 Nuget 套件
2. 重新新增**Xamarin.Forms**封裝。

這將會自動下載*正確*其他封裝的版本。

若要查看此程序的影片，請參閱此[論壇文章](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
