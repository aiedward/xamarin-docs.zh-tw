---
title: 應該安裝哪一個 Android SDK 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157360"
---
# <a name="which-android-sdk-packages-should-i-install"></a>應該安裝哪一個 Android SDK 套件？

安裝 Android SDK 不會自動包含開發所有最小必要的封裝。 雖然個別的開發人員必須不盡相同，通常會需要使用 Xamarin.Android 開發下列套件：

## <a name="tools"></a>工具

從 SDK manager 中的 [工具] 資料夾中安裝最新的工具：

- Android SDK Tools
- Android SDK 平台工具
- Android SDK 建置工具

## <a name="android-platforms"></a>Android 平台

您已設定為最小值和目標 Android 版本，請安裝 「 SDK 平台 」。 

例如：

- 目標 API 23
- 最小的 API 23

只需要安裝 SDK 平台的 API 23

- 目標 API 23
- 最小的 API 15

需要安裝的 SDK 平台 API 15 和 23。 請注意，您不需要安裝之間的最小值和目標 API 層級 （即使您是以這些 API 層級的 backporting）。

## <a name="system-images"></a>系統映像

這些只是需要您如果想要使用的立即可用來自 Google Android 模擬器。 如需詳細資訊，請參閱[Android Emulator 安裝](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>額外項目
Android SDK 額外項目通常是不必要的資訊;但您最好要知道它們的存在，因為它們可能需要視您的使用案例而定。

## <a name="further-reading"></a>進一步閱讀
下列指南說明這些選項，然後進入不同的套件的詳細 SDK 管理員具有可用：[Android SDK Manager 安裝指南](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

