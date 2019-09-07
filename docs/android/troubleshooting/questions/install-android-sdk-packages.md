---
title: 應該安裝哪一個 Android SDK 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: bd0f2a7704e5d666f6b32d4ccc489e069ec6ade6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757255"
---
# <a name="which-android-sdk-packages-should-i-install"></a>應該安裝哪一個 Android SDK 套件？

安裝 Android SDK 不會自動包含開發所需的所有必要套件。 雖然個別的開發人員需要不同，但一般會需要下列套件，才能使用 Xamarin 進行開發：

## <a name="tools"></a>工具

從 SDK 管理員中的 [工具] 資料夾安裝最新的工具：

- Android SDK Tools
- Android SDK 平臺-工具
- Android SDK 組建工具

## <a name="android-platforms"></a>Android 平臺

針對您已設定為最小 & 目標的 Android 版本，安裝「SDK 平臺」。 

例如：

- 目標 API 23
- 最低 API 23

只需要安裝適用于 API 23 的 SDK 平臺

- 目標 API 23
- 最小 API 15

需要安裝適用于 API 15 和23的 SDK 平臺。 請注意，您不需要在最小和目標之間安裝 API 層級（即使您後期才考慮至這些 API 層級）。

## <a name="system-images"></a>系統映射

只有當您想要使用 Google 的現成 Android 模擬器時，才需要這些元件。 如需詳細資訊，請參閱[Android Emulator 安裝程式](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>額外項目
通常不需要 Android SDK 額外專案;但請注意，因為它們可能需要視您的使用案例而定。

## <a name="further-reading"></a>進一步閱讀
下列指南涵蓋這些選項，並詳細說明 SDK manager 有哪些不同的套件可用：[Android SDK Manager 安裝指南](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)
