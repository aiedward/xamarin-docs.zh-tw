---
title: "應該安裝的 Android SDK 封裝？"
ms.topic: article
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/02/2018
ms.openlocfilehash: e2b0736a9ccc4dde5c1dcdf2d99f527247040560
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="which-android-sdk-packages-should-i-install"></a>應該安裝的 Android SDK 封裝？

安裝 Android SDK，不會自動加入開發所有最小必要的的封裝。 個別的開發人員需要有所不同，通常會需要使用 Xamarin.Android 開發下列套件：

## <a name="tools"></a>工具

從 SDK manager 中的 [工具] 資料夾中安裝最新工具：

- Android SDK 工具
- Android SDK 平台工具
- Android SDK 建置工具

## <a name="android-platforms"></a>Android 平台

安裝 「 SDK 平台 」，您已設定為最小值和目標的 Android 版本。 

例如：

- 目標 API 23
- 最小 API 23

只需要安裝 SDK 平台的應用程式開發介面 23

- 目標 API 23
- 最小 API 15

若要安裝 SDK 平台 API 15 和 23 的需要。 請注意，您不需要安裝 （即使您是 backporting 至這些應用程式開發介面層級） 的最小值與目標伺服器之間的應用程式開發介面層級。

## <a name="system-images"></a>系統映像
這些只是如果您想要使用的方塊外 google Android 模擬器。 

- [如何設定預設模擬器](~/android/get-started/installation/android-emulator/index.md)

- [如何加速預設模擬器](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>額外項目
Android SDK 額外項目通常是不必要的。但是就很有用，因為它們可能需要根據您使用大小寫要知道它們的存在。

## <a name="further-reading"></a>進一步閱讀
下列指南涵蓋這些選項，然後進入之不同套件的更多詳細 SDK manager 已無法使用： [Android SDK Manager 安裝手冊](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

