---
title: Android 的資源
description: 本文介紹 Android Xamarin.Android 中資源的概念，並將文件的使用方式。 它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式當地語系化，與多個裝置，包括各種不同的螢幕大小和密度。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 7b6ba9cdc222019bfa2e1cb9a61b54e290e69bba
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="android-resources"></a>Android 的資源

_本文介紹 Android Xamarin.Android 中資源的概念，並將文件的使用方式。它涵蓋了如何使用 Android 應用程式中的資源，以支援應用程式當地語系化，與多個裝置，包括各種不同的螢幕大小和密度。_


## <a name="overview"></a>總覽

Android 應用程式很少只原始程式碼。 通常在很多構成應用程式的其他檔案： 視訊、 影像、 字型和音訊檔案，只是為了提供幾個。 整體而言，這些非原始程式碼檔案稱為 「 資源和編譯 （以及的原始程式碼） 建置程序期間和封裝為 APK 散發和安裝到裝置：

![封裝的圖表](images/packaging-diagram.png)

資源提供 Android 應用程式的幾個好處：

-  **程式碼分離**&ndash;分開映像、 字串、 功能表、 動畫、 色彩等的原始程式碼。這類資源可協助大幅當地語系化時。

-  **針對多個裝置**&ndash;簡單支援不同的裝置組態，不需要任何程式碼變更。

-  **編譯時間檢查**&ndash;資源是靜態和編譯至應用程式。 這可讓在編譯時期，當它很容易就能捕捉和更正錯誤，而不是執行階段更難以找到且更正昂貴時要檢查資源的使用量。

當啟動新的 Xamarin.Android 專案時，會為特殊稱為資源建立目錄，以及一些子目錄：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![[資源] 資料夾和內容](images/resources-folder-vs.png)

在上圖中，應用程式資源會根據其類型到這些子目錄組織： 映像就會進入**drawable**目錄; 檢視移**配置**等子目錄。
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![[資源] 資料夾和內容](images/resources-folder-xs.png)

在上圖中，應用程式資源會根據其類型到這些子目錄組織： 映像就會進入**mipmap**目錄; 檢視移**配置**等子目錄。
 
-----

有兩種方式來存取這些資源 Xamarin.Android 應用程式中：*以程式設計方式*在程式碼和*以宣告方式*中使用特殊的 XML 語法的 XML。

這些資源稱為*預設資源*並且除非已指定更具體的相符項，可供所有裝置。 此外，每個資源類型可以選擇性地擁有*替代資源*使用 Android 時，可能會以特定的裝置為目標。 例如，資源可以提供給目標使用者的地區設定的螢幕大小，或裝置是否旋轉 90 度，從直向變成橫向，等等。在每個這種情況下，Android 會載入應用程式，而不需要任何額外程式碼撰寫的工作，開發人員所使用的資源。

所加入的短字串，呼叫指定替代資源*限定詞*，具有給定的類型的資源目錄結尾。

例如，**資源/drawable de**將指定的裝置設為德文地區設定、 映像時**資源/drawable fr**裝置設定為法文的地區設定會保存映像。 下圖，只要變更裝置的地區設定以執行相同的應用程式可以看到提供替代資源的範例：

![不同的地區設定的範例螢幕](images/localized-screenshots.png)

這篇文章會查看全方位使用資源，並涵蓋下列主題：

-  **Android 資源基本概念**&ndash;使用預設資源以程式設計的方式和以宣告方式，應用程式中加入資源類型，例如影像和字型。

-  **裝置特定組態**&ndash;應用程式中支援的不同螢幕解析度和密度。

-  **當地語系化**&ndash;使用資源來支援應用程式可能使用不同的區域。


## <a name="related-links"></a>相關連結

- [使用 Android 資產](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [應用程式基本概念](http://developer.android.com/guide/topics/fundamentals.html)
- [應用程式資源](http://developer.android.com/guide/topics/resources/index.html)
- [支援多個螢幕](http://developer.android.com/guide/practices/screens_support.html)
