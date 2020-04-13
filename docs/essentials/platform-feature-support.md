---
title: Xamarin.Essentials 平台與功能支援
description: Xamarin.Essentials 提供單一跨平台 API，能夠搭配任何 iOS、Android 或 UWP 應用程式使用，且無論使用者介面建立的方式為何，都能從共用的程式碼進行存取。
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 6d0e4df22ed363951759f24cc81f8147df482aef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78295426"
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

> [!NOTE]
>
> * Tizen 由 Samsung 開發小組提供官方支援。
> * tvOS 與 watchOS 具有有限的 API 支援，請參閱功能指南以取得詳細資訊。

## <a name="feature-support"></a>功能支援

Xamarin.Essentials 一律會嘗試將功能帶到每個平台，不過，有時候因為裝置而可能會有限制。 以下是每個平臺上支援哪些功能的指南。

圖示指南：

* ![全力支援](~/media/shared/yes.png "完整支援")- 全力支援
* ![有限支援](~/media/shared/warn.png "有限支援")- 有限支援
* ![不支援](~/media/shared/no.png "不支援")- 不支援

| 功能 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [加速計](accelerometer.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [應用程式資訊](app-information.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/no.png "不支援手錶作業系統") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [應用程式主題](app-theme.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [氣壓計](barometer.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [電池](battery.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![watchOS 有限支援](~/media/shared/warn.png "watchOS 有限支援") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 有限支援](~/media/shared/warn.png "Tizen 有限支援") | 
| [剪貼簿](clipboard.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | 
| [色彩轉換器](color-converters.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [羅盤](compass.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [連線能力](connectivity.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [偵測搖動](detect-shake.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [裝置顯示資訊](device-display.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") | 
| [裝置資訊](device-information.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [電子郵件](email.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [檔案系統協助程式](file-system-helpers.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [手電筒](flashlight.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [地理編碼](geocoding.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [地理位置](geolocation.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [陀螺儀](gyroscope.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [啟動器](launcher.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [磁力計](magnetometer.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [地圖](maps.md?content=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [開啟瀏覽器](open-browser.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [方向感應器](orientation-sensor.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [權限](permissions.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [電話撥號程式](phone-dialer.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [平台延伸模組](platform-extensions.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [偏好設定](preferences.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [保護儲存體](secure-storage.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [共用](share.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [SMS](sms.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [文字轉換語音](text-to-speech.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [單位轉換器](unit-converters.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [版本追蹤](version-tracking.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") | 
| [震動](vibrate.md?context=xamarin/xamarin-forms) | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
