---
title: "Android.Support.v7.AppCompat-找不到資源，符合指定名稱： attr ' android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: fea681ac3b99abed09d3d3e745bd4bf6015970df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112412"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat-找不到資源，符合指定名稱： attr ' android: actionModeShareDrawable'

1. 請確定您下載最新的額外項目，以及 Android 5.0 (API 21) SDK 透過 Android SDK 管理員。

2. 請確定您正在使用設定為 21 的 compileSdkVersion 編譯您的應用程式。 您可以選擇為 21 度以及設定 targetSdkVersion。

3. 如果您需要先前的版本，例如 API 19，請下載 Nuget 頁面上找到的個別版本：

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*請注意*： 如果您手動安裝這透過套件管理員主控台，請確定您也可以安裝相同版本的 Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

堆疊溢位的參考： [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>另請參閱

- [我應該安裝哪一個 Android SDK 套件？](~/android/troubleshooting/questions/install-android-sdk-packages.md)

