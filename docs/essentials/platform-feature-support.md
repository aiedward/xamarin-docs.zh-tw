---
title: Xamarin.Essentials 平台與功能支援
description: Xamarin.Essentials 提供單一跨平台 API，能夠搭配任何 iOS、Android 或 UWP 應用程式使用，且無論使用者介面建立的方式為何，都能從共用的程式碼進行存取。
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 66cc9042b74cd29cc11194e8cfde91a6ebec6f8b
ms.sourcegitcommit: 04929b5ff4384ca807727bec7c0467111a7eb283
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867651"
---
# <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平台與作業系統：

| Platform | {2&gt;版本&lt;2} |
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

Xamarin.Essentials 一律會嘗試將功能帶到每個平台，不過，有時候因為裝置而可能會有限制。 以下是每個平臺上支援哪些功能的指南。

圖示指南：

* ![完整支援](~/media/shared/yes.png "完整支援")-完整支援
* ![有限支援](~/media/shared/warn.png "有限支援")-有限支援
* ![不支援](~/media/shared/no.png "不支援")-不支援

| 特殊功能 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速計](accelerometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [應用程式資訊](app-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [氣壓計](barometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [電池](battery.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![tvOS 有限支援](~/media/shared/warn.png "tvOS 有限支援") | ![Tizen 有限支援](~/media/shared/warn.png "Tizen 有限支援") | 
| [剪貼簿](clipboard.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | 
| [色彩轉換器](color-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [羅盤](compass.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [連線能力](connectivity.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [偵測搖動](detect-shake.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [裝置顯示資訊](device-display.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | 
| [裝置資訊](device-information.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [電子郵件](email.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [檔案系統協助程式](file-system-helpers.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [手電筒](flashlight.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [地理編碼](geocoding.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [陀螺儀](gyroscope.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [啟動器](launcher.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [磁力計](magnetometer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [地圖](maps.md?content=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [開啟瀏覽器](open-browser.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [方向感應器](orientation-sensor.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [電話撥號程式](phone-dialer.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [平台延伸模組](platform-extensions.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [偏好設定](preferences.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [保護儲存體](secure-storage.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [共用](share.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [SMS](sms.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [文字轉換語音](text-to-speech.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [單位轉換器](unit-converters.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [版本追蹤](version-tracking.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") | 
| [震動](vibrate.md?context=xamarin/xamarin-forms) | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
