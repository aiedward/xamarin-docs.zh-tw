---
title: 平板電腦和傳統型應用程式的版面配置
description: 本文說明如何將平板電腦 Xamarin.Forms 的應用程式版面配置優化，而不是行動電話。
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5eb6a13888d96cac6fe193457ccc20659869ab68
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940378"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>平板電腦和傳統型應用程式的版面配置

Xamarin.Forms 支援在支援的平臺上使用的所有裝置類型，因此除了手機之外，應用程式也可以在上執行：

- Ipad
- Android 平板電腦、
- Windows 平板電腦和桌上型電腦 (執行 Windows 10) 。

此頁面簡短討論：

- 支援的 [裝置類型](#device-types)，以及
- 如何 [優化](#optimize-for-tablet-and-desktop) 平板電腦與手機的版面配置。

## <a name="device-types"></a>裝置類型

較大的螢幕裝置適用于支援的所有平臺 Xamarin.Forms 。

### <a name="ipads-ios"></a>Ipad (iOS) 

此 Xamarin.Forms 範本會將 **Plist > 裝置** 設定設定為 **通用** (（表示支援 iPhone 和 iPad) ），藉此包含 iPad 支援。

若要提供愉快的啟動體驗，並確保所有裝置都使用全螢幕解析度，您應該使用提供的分鏡腳本) 來確定 [iPad 特定的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md) (。 這可確保在 iPad 迷你、iPad 和 iPad Pro 裝置上正確轉譯應用程式。

在 iOS 9 之前，所有應用程式都會佔用裝置上的全螢幕，但某些 Ipad 現在可以執行 [分割螢幕多工](~/ios/platform/multitasking.md)。
這表示您的應用程式可能只會在螢幕側邊（螢幕的寬度50% 或整個螢幕）上佔用超薄的資料行。

[![iPad 分割畫面範例](tablet-images/ipad-sml.png)](tablet-images/ipad.png#lightbox "iPad 分割畫面範例")

分割畫面功能表示您應該將應用程式設計成可正常運作，最大可達320圖元寬，或最多1366圖元。

### <a name="android-tablets"></a>Android 平板電腦

Android 生態系統有各式各樣的支援畫面大小，從小型手機到大型平板電腦。 Xamarin.Forms 可以支援所有畫面大小，但與其他平臺一樣，您可能會想要調整更大裝置的使用者介面。

當支援許多不同的螢幕解析度時，您可以提供不同大小的原生映射資源，以將使用者體驗優化。
請參閱 [android 資源](~/android/app-fundamentals/resources-in-android/index.md) 檔 (，特別 [針對不同的螢幕大小建立資源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) 如需有關如何結構您的 android 應用程式專案中的資料夾和檔案名，以在應用程式中包含優化映射資源的詳細資訊，請參閱。

### <a name="windows-tablets-and-desktops"></a>Windows 平板電腦和桌上型電腦

若要支援執行 Windows 的平板電腦和桌上型電腦，您必須使用 [WINDOWS UWP 支援](~/xamarin-forms/platform/windows/installation/index.md)，它會建立在 Windows 10 上執行的通用應用程式。

除了執行全螢幕之外，在 Windows 平板電腦和桌上型電腦上執行的應用程式也可以調整大小為任意的維度。

[![Windows 分割畫面範例](tablet-images/splitscreen-sml.png)](tablet-images/splitscreen.png#lightbox "Windows 分割畫面範例")

## <a name="optimize-for-tablet-and-desktop"></a>針對平板電腦和桌面優化

您可以 Xamarin.Forms 根據使用的電話或平板電腦/桌面裝置，來調整您的使用者介面。 這表示您可以將大型螢幕裝置的使用者體驗優化，例如平板電腦和桌上型電腦。

### <a name="deviceidiom"></a>Device. 用法

您可以使用 [`Device`](~/xamarin-forms/platform/device.md) 類別來變更應用程式或使用者介面的行為。 `Device.Idiom`您可以使用列舉

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

這種方法可以展開，以對個別頁面配置進行重大變更，或甚至是在較大的螢幕上呈現完全不同的頁面。

### <a name="leverage-flyoutpage"></a>利用 FlyoutPage

適用于 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 較大的螢幕，特別是在 iPad 上使用 [`UISplitViewController`](xref:UIKit.UISplitViewController) 以提供原生 iOS 體驗。

請參閱 [此 Xamarin blog 文章](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/) ，以瞭解如何調整您的使用者介面，讓手機使用一個版面配置，而較大的螢幕可以搭配使用另一個 (與 `FlyoutPage`) 。

## <a name="related-links"></a>相關連結

- [Xamarin Blog](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 範例](https://github.com/jamesmontemagno/myshoppe)
