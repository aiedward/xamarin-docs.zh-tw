---
title: Android 資源
description: 本文介紹在 Xamarin.Android 中的 Android 資源的概念，並將文件的使用方式。 它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式的當地語系化，並包括各種螢幕大小和密度的多個裝置。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: d9cd6bf3ae51c6e27be88481e412995bd4113c17
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117251"
---
# <a name="android-resources"></a>Android 資源

_本文介紹在 Xamarin.Android 中的 Android 資源的概念，並將文件的使用方式。它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式的當地語系化，並包括各種螢幕大小和密度的多個裝置。_


## <a name="overview"></a>總覽

Android 應用程式很少只是原始程式碼。 通常有許多其他檔案可構成應用程式： 影片、 映像、 字型和音訊檔案，只是為了等等。 整體而言，這些非原始程式碼檔案稱為資源編譯 （和原始程式碼），建置程序期間和是封裝成散發和裝置上的安裝 APK:

![封裝的圖表](images/packaging-diagram.png)

資源提供了 Android 應用程式的數個優點：

-  **程式碼分開置放**&ndash;分開映像、 字串、 功能表、 動畫、 色彩等的原始程式碼。這類資源可以協助大幅當地語系化時。

-  **以多個裝置為目標**&ndash;簡化支援不同的裝置設定，無須變更程式碼。

-  **編譯時間檢查**&ndash;資源是靜態和編譯到應用程式。 這可讓在編譯時期，當它很容易就能捕捉和更正錯誤，而不是執行階段更難以找出並修正高成本時要檢查資源的使用量。

啟動新的 Xamarin.Android 專案時，名為資源的特殊目錄時會建立，以及一些子目錄：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![[資源] 資料夾和內容](images/resources-folder-vs.png)

在上圖中，應用程式資源會根據其類型，這些子目錄到組織： 映像就會進入**drawable**目錄; 檢視進**版面配置**子目錄，以此類推。
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![[資源] 資料夾和內容](images/resources-folder-xs.png)

在上圖中，應用程式資源會根據其類型，這些子目錄到組織： 映像就會進入**mipmap**目錄; 檢視進**版面配置**子目錄，以此類推。
 
-----

有兩種方式來存取這些資源在 Xamarin.Android 應用程式中：*以程式設計方式*中的程式碼並*以宣告方式*中使用特殊的 XML 語法的 XML。

這些資源稱為*預設資源*且除非另有更特定的符合項目會指定由所有裝置。 此外，每種資源類型可能會選擇*替代資源*Android 可用來以特定的裝置為目標。 例如，資源可能會提供在目標使用者的地區設定中，螢幕大小，或裝置是否旋轉 90 度，從直向變成橫向，等等。在這些情況下，Android 會載入應用程式，而不需要任何額外程式碼撰寫的工作，開發人員所使用的資源。

替代的資源指定藉由新增一個簡短的字串，稱為*限定詞*，保留指定的資源類型的目錄的結尾。

例如，**資源/drawable de**將指定的映像的裝置設為德文地區設定，而**資源/drawable fr**裝置設定為法文的地區設定會保存映像。 在相同的應用程式正在執行的只是變更裝置的地區設定下圖中可以看到範例提供替代資源：

![針對不同地區設定的範例畫面](images/localized-screenshots.png)

這篇文章會需要使用資源的完整介紹，並涵蓋下列主題：

-  **Android 資源基本概念**&ndash;使用預設資源以程式設計方式和以宣告方式，新增對應用程式的資源類型，例如影像和字型。

-  **裝置特定的組態**&ndash;應用程式中支援的不同螢幕解析度和密度。

-  **當地語系化**&ndash;使用資源來支援不同的區域可能使用應用程式。


## <a name="related-links"></a>相關連結

- [使用 Android 資產](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [應用程式基本概念](http://developer.android.com/guide/topics/fundamentals.html)
- [應用程式資源](http://developer.android.com/guide/topics/resources/index.html)
- [支援多個畫面](http://developer.android.com/guide/practices/screens_support.html)
