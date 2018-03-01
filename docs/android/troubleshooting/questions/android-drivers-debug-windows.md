---
title: "若要偵錯在 Windows 上的 Android 需要哪些 USB 驅動程式？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 97803c0496c7f5f6ea40e86023caad66c675037e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>若要偵錯在 Windows 上的 Android 需要哪些 USB 驅動程式？

## <a name="finding-usb-drivers"></a>尋找 USB 驅動程式

若要偵錯 Android 裝置上開發 Windows; 中時您必須安裝相容的 USB 驅動程式。 Android SDK Manager 包含 「 Google USB 驅動程式 」 依預設，加入 Nexus 裝置的支援，如下所述： [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

其他裝置則需要特別裝置製造商所發行的 USB 驅動程式。 本指南中包含一些最常見的製造商的連結： [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代項目

根據 manfacturer，可能很難追蹤需要的確切 USB 驅動程式。 包括使用的 Android 模擬器，或使用外部測試服務的 Windows 中，開發測試 Android 應用程式的一些其他替代方案。 其中包括：

- [Xamarin Test Cloud](https://xamarin.com/test-cloud) -雲端測試在數百真正 Android 裝置上執行的服務。

- [Android 版 Visual Studio 模擬器](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Google Android SDK 模擬器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

