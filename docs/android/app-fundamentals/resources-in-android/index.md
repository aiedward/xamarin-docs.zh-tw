---
title: Android 資源
description: 本文介紹 Xamarin Android 資源的概念，並說明如何使用它們。 其中涵蓋如何使用 Android 應用程式中的資源來支援應用程式當地語系化，以及多個裝置，包括各種不同的螢幕大小和密度。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: ec1cb6fcce320ed5ea9154b42d0a5361940c1015
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755020"
---
# <a name="android-resources"></a>Android 資源

_本文介紹 Xamarin Android 資源的概念，並說明如何使用它們。其中涵蓋如何使用 Android 應用程式中的資源來支援應用程式當地語系化，以及多個裝置，包括各種不同的螢幕大小和密度。_

## <a name="overview"></a>總覽

Android 應用程式不常是原始程式碼。 通常會有許多其他檔案組成應用程式：影片、影像、字型和音訊檔案，只需要命名一些檔案。 這些非原始程式碼檔案統稱為資源，並會在建立程式期間進行編譯（連同原始程式碼），並封裝為 APK 以散發和安裝到裝置上：

![封裝圖表](images/packaging-diagram.png)

資源提供 Android 應用程式的幾個優點：

- **程式碼分隔**&ndash;將原始程式碼與影像、字串、功能表、動畫、色彩等區隔開。當當地語系化時，這類資源可提供很大的説明。

- 以**多個裝置為目標**&ndash;提供不同裝置設定的較簡單支援，而不需要變更程式碼。

- **編譯時期檢查**&ndash;資源是靜態的，而且會編譯到應用程式中。 這可讓您在編譯時期檢查資源的使用，這是很容易攔截和更正錯誤的情況，而不是比執行時間更難找到和修正成本更高的時候。

當新的 Xamarin Android 專案啟動時，會建立稱為 Resources 的特殊目錄，以及一些子目錄：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Resources 資料夾和內容](images/resources-folder-vs.png)

在上圖中，應用程式資源會根據其類型組織成下列子目錄：影像將會放在可**繪製**的目錄中;views 會進入**版面**配置子目錄等等。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Resources 資料夾和內容](images/resources-folder-xs.png)

在上圖中，應用程式資源會根據其類型組織成下列子目錄：影像將會放在**mipmap**目錄中;views 會進入**版面**配置子目錄等等。

-----

有兩種方式可存取 Xamarin. Android 應用程式中的這些資源：以程式設計*方式* *，在程式碼中以*程式設計方式使用特殊 xml 語法。

這些資源稱為*預設資源*，除非指定更明確的相符項，否則所有裝置都會使用這些資源。 此外，每種類型的資源都可以選擇性地擁有 Android 可能用來以特定裝置為目標的*替代資源*。 例如，可能會提供資源以使用者的地區設定、螢幕大小，或裝置旋轉90度（從直向到橫向）等等。在上述每一種情況下，Android 都會載入資源供應用程式使用，而不需要由開發人員進行任何額外的編碼工作。

替代資源的指定方式，是將簡短字串（稱為辨識*符號*）新增至保存指定資源類型之目錄的結尾。

例如，**資源/可繪製的**會針對設定為德文地區設定的裝置指定映射，而**資源/可繪製-fr**則會保留設定為法文地區設定之裝置的影像。 下圖所示的是提供替代資源的範例，其中只有裝置的地區設定變更時，才會執行相同的應用程式：

![不同地區設定的範例畫面](images/localized-screenshots.png)

本文將全面探討如何使用資源，並涵蓋下列主題：

- **Android 資源基本概念**&ndash;以程式設計方式和宣告方式使用預設資源，將影像和字型之類的資源類型新增至應用程式。

- **裝置特定**設定&ndash;在應用程式中支援不同的螢幕解析度和密度。

- **當地語系化**&ndash;使用資源來支援應用程式可能會使用的不同區域。

## <a name="related-links"></a>相關連結

- [使用 Android 資產](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [應用程式基本概念](https://developer.android.com/guide/topics/fundamentals.html)
- [應用程式資源](https://developer.android.com/guide/topics/resources/index.html)
- [支援多個畫面](https://developer.android.com/guide/practices/screens_support.html)
