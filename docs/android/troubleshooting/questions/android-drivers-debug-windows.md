---
title: 在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8be3f1b8803aa7e052ebc89af51dad3b659f95f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757322"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？

## <a name="finding-usb-drivers"></a>尋找 USB 驅動程式

在 Windows 中進行開發時，在 Android 裝置上進行偵錯工具;您必須安裝相容的 USB 驅動程式。 Android SDK Manager 預設會包含「Google USB 驅動程式」，這會新增對結點裝置的支援，如下所述：[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

其他裝置則需要裝置製造商特別發佈的 USB 驅動程式。 本指南包含一些最常見製造商的連結：[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代項目

視 manfacturer 而定，可能很難以追蹤所需的確切 USB 驅動程式。 測試在 Windows 中開發之 Android 應用程式的一些替代方案，包括使用 Android 模擬器或使用外部測試服務。 其中包括：

- [App Center 測試](https://docs.microsoft.com/appcenter/test-cloud/)-雲端測試服務會在數百個實際的 Android 裝置上執行。

- [Android 版 Visual Studio 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Run Apps on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上執行應用程式)
