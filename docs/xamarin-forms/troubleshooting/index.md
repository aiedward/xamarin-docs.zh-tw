---
title: 疑難排解
description: 常見的錯誤狀況與解決方式
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>疑難排解

_常見的錯誤狀況與解決方式_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>錯誤: 「 無法找到 Xamarin.Forms 的版本與相容...」

下列的錯誤可以出現在**套件主控台**視窗更新所有 Nuget 封裝或 Xamarin.Forms Android 應用程式專案中的 Xamarin.Forms 方案時：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>造成這個錯誤的原因為何？

Visual Studio for Mac （或 Visual Studio） 可能表示有適用於 Xamarin.Forms Nuget 封裝時，發生更新*及其所有相依*。 在 Xamarin Studio 中，方案的**封裝**節點可能看起來像這樣 （版本號碼可能會不同）：

![](images/updates-available.png "Android 專案的封裝 資料夾")

如果您嘗試更新，可能會發生這個錯誤_所有_封裝。

這是因為 android 專案設定為 Android 6.0 (API 23) 的目標/編譯版本或以下，Xamarin.Forms 具有硬式相依性*特定*版本的 Android 支援封裝。 雖然可以使用這些封裝的更新的版本，並不一定會與它們 Xamarin.Forms。

在此情況下，您應該更新_只_ **Xamarin.Forms**封裝，因為這可確保相依性維持相容的版本上。 其他封裝，您已加入您的專案可能不會造成的 Android 支援套件，以更新完成後，只要也個別會更新。


> [!NOTE]
> 如果您使用 Xamarin.Forms 2.3.4 或更高版本**和**Android 專案的目標/編譯版本設定為 Android 7.0 (應用程式開發介面 24) 或更高版本，則套用上述不再硬式相依性，而且您可能會更新支援獨立 Xamarin.Forms 封裝的封裝。


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>修正： 移除所有的封裝，並重新加入 Xamarin.Forms

如果**Xamarin.Android.Support**不相容的版本已更新封裝，最簡單的修正方法是：

1. 手動刪除所有的 Nuget 封裝中的 Android 專案然後
2. 重新加入**Xamarin.Forms**封裝。

這將會自動下載*正確*其他封裝的版本。

若要查看此程序的影片，請參閱此[論壇張貼](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
