---
title: 在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 21fd8eff64d374e52e64194524a8c096cdf4d90e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027033"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？

## <a name="finding-usb-drivers"></a>尋找 USB 驅動程式

在 Windows 中開發時在 Android 設備上調試;您需要安裝相容的 USB 驅動程式。 默認情況下,Android SDK 管理器包括"谷歌 USB 驅動程式",它添加了對 Nexus 設備的支援,如下所示:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

其他設備需要設備製造商專門發佈的USB驅動程式。 本指南中包括了最常見的製造商的一些連結:[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代方案

根據製造器的不同,很難找到所需的精確 USB 驅動程式。 用於測試 Windows 中開發的 Android 應用的一些替代方法,包括使用 Android 模擬器或使用外部測試服務。 其中包含：

- [應用中心測試](https://docs.microsoft.com/appcenter/test-cloud/)- 雲測試服務運行在數百個真正的Android設備上。

- [適用於 Android 的視覺工作室模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [在 Android Emulator 上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)
