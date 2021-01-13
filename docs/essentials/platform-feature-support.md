---
title: Xamarin.Essentials 平臺 & 功能支援
description: Xamarin.Essentials 提供單一跨平臺 API，適用于任何可從共用程式碼存取的 iOS、Android 或 UWP 應用程式，無論使用者介面的建立方式為何。
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c21e4c746224902b4dcc317b53d751d33c45df1
ms.sourcegitcommit: 86663f94f8eddb808eb4504cd32ddaf217b6406c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166663"
---
# <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平臺和作業系統：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| Tizen | 4.0 或更高版本 |
| tvOS | 10.0 或更高版本 |
| watchOS | 4.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |
| macOS | 10.12.6 (塞拉里昂) 或更高版本 |

> [!NOTE]
>
> * Tizen 由 Samsung 開發小組提供官方支援。
> * tvOS 與 watchOS 具有有限的 API 支援，請參閱功能指南以取得詳細資訊。
> * macOS 支援目前為預覽狀態。

## <a name="feature-support"></a>功能支援

Xamarin.Essentials 一律會嘗試將功能帶入每個平臺，但有時根據裝置會有一些限制。 以下是每個平臺上支援哪些功能的指南。

圖示指南：

* ![完整支援](~/media/shared/yes.png "完整支援") -完整支援
* ![有限支援](~/media/shared/warn.png "有限支援") -有限支援
* ![不支援](~/media/shared/no.png "不支援") -不支援

| 功能 | Android | iOS | UWP | watchOS | tvOS | Tizen | macOS |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速計](accelerometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [應用程式動作](app-actions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [應用程式資訊](app-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [應用程式主題](app-theme.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [Barometer](barometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [電池](battery.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![watchOS 有限支援](~/media/shared/warn.png "watchOS 有限支援") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 有限支援](~/media/shared/warn.png "Tizen 有限支援") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [剪貼簿](clipboard.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [色彩轉換器](color-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [Compass](compass.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [連線能力](connectivity.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [連絡人](contacts.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [偵測搖動](detect-shake.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [裝置顯示資訊](device-display.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [裝置資訊](device-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [電子郵件](email.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [檔案選擇器](file-picker.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [檔案系統協助程式](file-system-helpers.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [Flashlight](flashlight.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [地理編碼](geocoding.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [迴轉儀](gyroscope.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [觸覺回饋技術意見反應](haptic-feedback.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [Launcher](launcher.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [磁力計](magnetometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [地圖](maps.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [媒體選擇器](media-picker.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![macOS 有限支援](~/media/shared/warn.png "macOS 有限支援") |
| [開啟瀏覽器](open-browser.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [方向感應器](orientation-sensor.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [權限](permissions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [電話撥號程式](phone-dialer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [平台延伸模組](platform-extensions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [偏好設定](preferences.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [螢幕擷取畫面](screenshot.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [安全存放裝置](secure-storage.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [共用](share.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [SMS](sms.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [文字轉換語音](text-to-speech.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [單位轉換器](unit-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [版本追蹤](version-tracking.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
| [Vibrate](vibrate.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") | ![不支援 macOS](~/media/shared/no.png "不支援 macOS") |
| [Web 驗證器](web-authenticator.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | ![支援 macOS](~/media/shared/yes.png "支援 macOS") |
