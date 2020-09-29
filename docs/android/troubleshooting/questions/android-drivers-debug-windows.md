---
title: 在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: c07f9099f3b76ed86a235883ce335ce19a426b99
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457883"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？

## <a name="finding-usb-drivers"></a>尋找 USB 驅動程式

在 Windows 中進行開發時，于 Android 裝置上進行偵錯工具您必須安裝相容的 USB 驅動程式。 根據預設，Android SDK 管理員會包含「Google USB 驅動程式」，這會新增對結點裝置的支援，如下所述： [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

其他裝置則需要由裝置製造商特別發佈的 USB 驅動程式。 本指南包含最常見製造商的一些連結： [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代方案

根據 manfacturer 而定，可能很難追蹤所需的確切 USB 驅動程式。 測試在 Windows 中開發的 Android 應用程式的一些替代方案，包括使用 Android 模擬器或使用外部測試服務。 其中包含：

- [App Center 測試](/appcenter/test-cloud/) -雲端測試服務會在數百個實際的 Android 裝置上執行。

- [適用于 Android 的 Visual Studio 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [在 Android Emulator 上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)