---
title: ''
description: 本文說明如何優化平板電腦的 Xamarin.Forms 應用程式版面配置，而不是手機。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8ce5ba09f89c2bc84b7f6ba722f724ae39c0222e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137919"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>平板電腦和桌面應用程式的版面配置

Xamarin.Forms支援在支援的平臺上使用的所有裝置類型，因此除了電話以外，應用程式也可以在上執行：

- Ipad
- Android 平板電腦、
- Windows 平板電腦和桌上型電腦（執行 Windows 10）。

本頁簡要討論：

- 支援的[裝置類型](#Device_Types)，以及
- 如何[優化](#optimize)平板電腦與手機的版面配置。

<a name="Device_Types" />

## <a name="device-types"></a>裝置類型

較大的螢幕裝置適用于支援的所有平臺 Xamarin.Forms 。

### <a name="ipads-ios"></a>Ipad （iOS）

此 Xamarin.Forms 範本會將**Plist > 裝置**設定設為**通用**（這表示支援 iPhone 和 iPad），藉此自動包含 iPad 支援。

若要提供愉快的啟動體驗，並確保在所有裝置上都使用全螢幕解析度，您應該確定提供了[iPad 特定的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用分鏡腳本）。 這可確保在 iPad 迷你、iPad 和 iPad Pro 裝置上正確呈現應用程式。

在 iOS 9 之前，所有應用程式都會在裝置上佔用全螢幕，但有些 Ipad 現在可以執行[分割畫面的多工](~/ios/platform/multitasking.md)作業。
這表示您的應用程式可能只佔用畫面側邊的超薄資料行、畫面寬度的50% 或整個畫面。

[![](tablet-images/ipad-sml.png "iPad Split Screen Example")](tablet-images/ipad.png#lightbox "iPad Split Screen Example")

分割畫面功能表示您應該設計應用程式，使其在最少320圖元的情況下運作良好，或最多可達1366圖元的寬度。

### <a name="android-tablets"></a>Android 平板電腦

Android 生態系統有多種支援的螢幕大小，從小型手機到大型平板電腦。 Xamarin.Forms可以支援所有螢幕大小，但如同其他平臺，您可能會想要針對較大的裝置調整使用者介面。

支援許多不同的螢幕解析度時，您可以提供不同大小的原生映射資源，以優化使用者體驗。
請參閱[android 資源](~/android/app-fundamentals/resources-in-android/index.md)檔（尤其是[建立不同螢幕大小的資源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)），以取得如何在 android 應用程式專案中將資料夾和檔案名結構在應用程式中包含優化影像資源的詳細資訊。

### <a name="windows-tablets-and-desktops"></a>Windows 平板電腦和桌上型電腦

若要支援執行 Windows 的平板電腦和桌上型電腦，您必須使用[WINDOWS UWP 支援](~/xamarin-forms/platform/windows/installation/index.md)，其會建立在 Windows 10 上執行的通用應用程式。

除了執行全螢幕外，在 Windows 平板電腦和桌面上執行的應用程式也可以調整為任意維度。

[![](tablet-images/splitscreen-sml.png "Windows Split Screen Example")](tablet-images/splitscreen.png#lightbox "Windows Split Screen Example")

<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>為平板電腦和桌面優化

您可以根據 Xamarin.Forms 電話或平板電腦/桌面裝置是否正在使用，調整您的使用者介面。 這表示您可以將大型螢幕裝置（如平板電腦和桌上型電腦）的使用者體驗優化。

### <a name="deviceidiom"></a>Device. 用法

您可以使用 [`Device`](~/xamarin-forms/platform/device.md) 類別來變更應用程式或使用者介面的行為。 `Device.Idiom`您可以使用列舉型別

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

這種方法可以擴充以對個別頁面配置進行重大變更，甚至是在較大的螢幕上呈現完全不同的頁面。

### <a name="leveraging-masterdetailpage"></a>運用 MasterDetailPage

適用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 于較大的螢幕，特別是在 iPad 上，它會使用 [`UISplitViewController`](xref:UIKit.UISplitViewController) 來提供原生 iOS 體驗。

請參閱[這篇 Xamarin blog 文章](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)，以瞭解如何調整您的使用者介面，讓手機使用一個版面配置，而較大的螢幕則可以使用另一個（與 `MasterDetailPage` ）。

## <a name="related-links"></a>相關連結

- [Xamarin Blog](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 範例](https://github.com/jamesmontemagno/myshoppe)
