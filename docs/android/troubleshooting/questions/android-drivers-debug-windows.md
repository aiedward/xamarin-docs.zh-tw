---
title: 我需要哪些 USB 驅動程式在 Windows 上的 Android 偵錯？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8b996a1cc89acedc47c7169ec579dfb99dae788f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114531"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>我需要哪些 USB 驅動程式在 Windows 上的 Android 偵錯？

## <a name="finding-usb-drivers"></a>找出 USB 驅動程式

在 Windows; 中開發時，在 Android 裝置上偵錯您要安裝相容的 USB 驅動程式。 Android SDK 管理員會包含 「 Google USB 驅動程式 」，根據預設，新增如下所述的 Nexus 裝置的支援： [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

其他裝置則需要特別發佈由裝置製造商的 USB 驅動程式。 本指南中包含一些最常見的製造商的連結： [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代項目

根據 manfacturer，很難以追蹤所需的確切 USB 驅動程式。 包括使用 Android 模擬器，或使用外部測試服務的 Windows 開發測試 Android 應用程式的一些其他替代方案。 其中包括：

- [App Center 測試](https://docs.microsoft.com/appcenter/test-cloud/)-雲端測試的數百個實際 Android 裝置上執行的服務。

- [Android 版 Visual Studio 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Run Apps on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上執行應用程式)

