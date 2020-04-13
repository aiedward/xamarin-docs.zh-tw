---
title: 應該安裝哪一個 Android SDK 套件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292472"
---
# <a name="which-android-sdk-packages-should-i-install"></a>應該安裝哪一個 Android SDK 套件？

安裝 Android SDK 不會自動包含開發所需的所有軟體包。 雖然各個開發人員的需求各不相同,但使用 Xamarin.Android 進行開發通常需要以下套件:

## <a name="tools"></a>工具

在 SDK 管理員中從「工具」資料夾中安裝最新工具:

- Android SDK 工具
- Android SDK 平台工具
- Android SDK 建置工具

## <a name="android-platforms"></a>安卓平臺

安裝"SDK 平臺",適用於已設置為最低&目標的 Android 版本。

範例：

- 目標 API 23
- 最小 API 23

只需為 API 23 安裝 SDK 平台

- 目標 API 23
- 最小 API 15

需要為 API 15 和 23 安裝 SDK 平臺。 請注意,您不需要在最小值和目標級別之間安裝 API 級別(即使您正在回移植到這些 API 級別)。

## <a name="system-images"></a>系統映像

只有當您想要使用 Google 的開箱即用的 Android 模擬器時,才需要這些模擬器。 有關詳細資訊,請參閱[Android 模擬器設定](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>額外項目
通常不需要 Android SDK 附加功能;但是了解它們很有用,因為根據您的用例,可能需要它們。
