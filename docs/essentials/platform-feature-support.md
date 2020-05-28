---
title: Xamarin.Essentials平臺 & 功能支援
description: Xamarin.Essentials提供單一跨平臺 API，可搭配任何 iOS、Android 或 UWP 應用程式使用，無論使用者介面建立的方式為何，都能從共用的程式碼存取。
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b052e03f3ca57229988a29b7d3f38f5fa7bb6f97
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137172"
---
# <a name="platform-support"></a>平台支援

Xamarin.Essentials支援下列平臺和作業系統：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| Tizen | 4.0 或更高版本 |
| tvOS | 10.0 或更高版本 |
| watchOS | 4.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |

> [!NOTE]
>
> * Tizen 由 Samsung 開發小組提供官方支援。
> * tvOS 與 watchOS 具有有限的 API 支援，請參閱功能指南以取得詳細資訊。

## <a name="feature-support"></a>功能支援

Xamarin.Essentials一律會嘗試將功能帶入每個平臺，但有時會根據裝置來限制。 以下是每個平臺上支援哪些功能的指南。

圖示指南：

* ![完整支援](~/media/shared/yes.png "完整支援")-完整支援
* ![有限支援](~/media/shared/warn.png "有限支援")-有限支援
* ![不支援](~/media/shared/no.png "不支援")-不支援

| 功能 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速計](accelerometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [應用程式資訊](app-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [應用程式主題](app-theme.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [Barometer](barometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [電池](battery.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![watchOS 有限支援](~/media/shared/warn.png "watchOS 有限支援") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 有限支援](~/media/shared/warn.png "Tizen 有限支援") |
| [剪貼簿](clipboard.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| [色彩轉換器](color-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [Compass](compass.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [連線能力](connectivity.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [偵測搖動](detect-shake.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [裝置顯示資訊](device-display.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| [裝置資訊](device-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [電子郵件](email.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [檔案系統協助程式](file-system-helpers.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [手電筒](flashlight.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [地理編碼](geocoding.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [迴轉儀](gyroscope.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [Launcher](launcher.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [磁力計](magnetometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [地圖](maps.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [開啟瀏覽器](open-browser.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [方向感應器](orientation-sensor.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [權限](permissions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [電話撥號程式](phone-dialer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [平台延伸模組](platform-extensions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [偏好設定](preferences.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [安全存放裝置](secure-storage.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [共用](share.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [sms](sms.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [文字轉換語音](text-to-speech.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [單位轉換器](unit-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [版本追蹤](version-tracking.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| [震動](vibrate.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
