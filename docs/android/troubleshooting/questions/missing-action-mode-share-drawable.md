---
title: Android.Support.v7.AppCompat - 找不到符合指定名稱：attr 'android:actionModeShareDrawable' 的資源
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: ff2a586773e33a1f4cf78657c3c69c22e79ed047
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670229"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - 找不到符合指定名稱：attr 'android:actionModeShareDrawable' 的資源

1. 請確定您下載最新的額外項目，以及 Android 5.0 (API 21) SDK 透過 Android SDK 管理員。

2. 請確定您正在使用設定為 21 的 compileSdkVersion 編譯您的應用程式。 您可以選擇為 21 度以及設定 targetSdkVersion。

3. 如果您需要先前的版本，例如 API 19，請下載 Nuget 頁面上找到的個別版本：

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*注意*：如果您手動安裝這透過套件管理員主控台，請確定您也可以安裝相同版本的 Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

堆疊溢位的參考： [https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>另請參閱

- [我應該安裝哪一個 Android SDK 套件？](~/android/troubleshooting/questions/install-android-sdk-packages.md)

