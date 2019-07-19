---
title: Xamarin.Essentials 平台與功能支援
description: Xamarin.Essentials 提供單一跨平台 API，能夠搭配任何 iOS、Android 或 UWP 應用程式使用，且無論使用者介面建立的方式為何，都能從共用的程式碼進行存取。
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 07/10/2019
ms.openlocfilehash: 7db848c01879510969f690cb49c5ead32cb3f0eb
ms.sourcegitcommit: 0845ed2daa65468b6fe12ac4e9386f3315d72f4d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850988"
---
# <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平台與作業系統：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| Tizen | 4.0 或更高版本 |
| tvOS | 10.0 或更高版本 |
| watchOS | 4.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |

**注意：**

* Tizen 由 Samsung 開發小組提供官方支援。
* tvOS 與 watchOS 具有有限的 API 支援，請參閱功能指南以取得詳細資訊。

## <a name="feature-support"></a>功能支援

Xamarin.Essentials 一律會嘗試將功能帶到每個平台，不過，有時候因為裝置而可能會有限制。 以下是每個平台支援之功能的指南。

圖示指南：

* ✔ - 完全支援
* ⚠ - 有限支援
* ❌ - 不支援

| 功能 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速計](accelerometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [應用程式資訊](app-information.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [氣壓計](barometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [電池](battery.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ⚠ | ⚠ |
| [剪貼簿](clipboard.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ❌ |
| [色彩轉換器](color-converters.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [羅盤](compass.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [連線能力](connectivity.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [偵測搖動](detect-shake.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [裝置顯示資訊](device-display.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ❌ |
| [裝置資訊](device-information.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [電子郵件](email.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [檔案系統協助程式](file-system-helpers.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [手電筒](flashlight.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [地理編碼](geocoding.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [陀螺儀](gyroscope.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [啟動器](launcher.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [磁力計](magnetometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [地圖](maps.md?content=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [開啟瀏覽器](open-browser.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [方向感應器](orientation-sensor.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [電話撥號程式](phone-dialer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [平台延伸模組](platform-extensions.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [偏好設定](preferences.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [保護儲存體](secure-storage.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [共用](share.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [SMS](sms.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [文字轉換語音](text-to-speech.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [單位轉換器](unit-converters.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [版本追蹤](version-tracking.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [震動](vibrate.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |

