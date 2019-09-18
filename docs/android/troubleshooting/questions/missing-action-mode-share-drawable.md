---
title: Android.Support.v7.AppCompat - 找不到符合指定名稱：attr 'android:actionModeShareDrawable' 的資源
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 3bce50169919df83ba1ab76c29d1ecc5c1da0bd6
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021125"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - 找不到符合指定名稱：attr 'android:actionModeShareDrawable' 的資源

1. 請確定您已透過 Android SDK Manager 下載最新的額外專案，以及 Android 5.0 （API 21） SDK。

2. 請確定您是使用設定為21的 compileSdkVersion 來編譯應用程式。 您也可以選擇性地將 targetSdkVersion 設定為21。

3. 如果您需要舊版的 API 19 之類的版本，請下載 Nuget 頁面上找到的個別版本：

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

> [!NOTE]
> 如果您透過 [套件管理員主控台] 手動安裝此功能，請確定您也要安裝相同版本的 Xamarin. Android. 支援 v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Stack Overflow 參考：[https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>另請參閱

- [我應該安裝哪一個 Android SDK 套件？](~/android/troubleshooting/questions/install-android-sdk-packages.md)
