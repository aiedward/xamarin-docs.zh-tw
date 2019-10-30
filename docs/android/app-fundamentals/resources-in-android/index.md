---
title: Android 資源
description: This article introduces the concept of Android resources in Xamarin.Android and will document how to use them. It covers how to use resources in your Android application to support application localization, and multiple devices including varying screen sizes and densities.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: 1ff4d9d896aaa5f290402a49aa4b4bd1f1e00aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025051"
---
# <a name="android-resources"></a>Android 資源

_This article introduces the concept of Android resources in Xamarin.Android and will document how to use them. It covers how to use resources in your Android application to support application localization, and multiple devices including varying screen sizes and densities._

## <a name="overview"></a>總覽

An Android application is seldom just source code. There are often many other files that make up an application: video, images, fonts, and audio files just to name a few. Collectively, these non-source code files are referred to as resources and are compiled (along with the source code) during the build process and packaged as an APK for distribution and installation onto devices:

![Packaging diagram](images/packaging-diagram.png)

Resources offer several advantages to an Android application:

- **Code-Separation** &ndash; Separates source code from images,  strings, menus, animations, colors, etc. As such resources can help  considerably when localizing.

- **Target multiple devices** &ndash; Provides simpler support of  different device configurations without code changes.

- **Compile-time Checking** &ndash; Resources are static and compiled into the application. This allows the usage of the resources to be checked at compile time, when it will be easy to catch and correct the mistakes, as opposed to run-time when it is more difficult to locate and costly to correct.

When a new Xamarin.Android project is started, a special directory called Resources is created, along with some subdirectories:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Resources folder and contents](images/resources-folder-vs.png)

In the image above, the application resources are organized according to their type into these subdirectories: images will go in the **drawable** directory; views go in the **layout** subdirectory, etc.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Resources folder and contents](images/resources-folder-xs.png)

In the image above, the application resources are organized according to their type into these subdirectories: images will go in the **mipmap** directory; views go in the **layout** subdirectory, etc.

-----

There are two ways to access these resources in a Xamarin.Android application: *programmatically* in code and *declaratively* in XML using a special XML syntax.

These resources are called *Default Resources* and are used by all devices unless a more specific match is specified. Additionally, every type of resource may optionally have *Alternate Resources* that Android may use to target specific devices. For example, resources may be provided to target the user's locale, the screen size, or if the device is rotated 90 degrees from portrait to landscape, etc. In each of these cases, Android will load the resources for use by the application without any extra coding effort by the developer.

Alternate resources are specified by adding a short string, called a *qualifier*, to the end of the directory holding a given type of resources.

For example, **resources/drawable-de** will specify the images for devices that are set to a German locale, while **resources/drawable-fr** would hold images for devices set to a French locale. An example of providing alternate resources can be seen in the image below where the same application is being run with just the locale of the device changing:

![Example screens for different locales](images/localized-screenshots.png)

This article will take a comprehensive look at using resources and cover the following topics:

- **Android 資源基本概念**&ndash; 以程式設計方式和宣告方式使用預設資源，將影像和字型之類的資源類型新增至應用程式。

- **裝置特定**設定 &ndash; 在應用程式中支援不同的螢幕解析度和密度。

- **當地語系化**&ndash; 使用資源來支援應用程式可能使用的不同區域。

## <a name="related-links"></a>相關連結

- [使用 Android 資產](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [應用程式基本概念](https://developer.android.com/guide/topics/fundamentals.html)
- [應用程式資源](https://developer.android.com/guide/topics/resources/index.html)
- [支援多個畫面](https://developer.android.com/guide/practices/screens_support.html)
