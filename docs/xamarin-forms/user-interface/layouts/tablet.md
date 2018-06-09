---
title: 平板電腦和桌面應用程式的版面配置
description: 本文說明如何最佳化平板電腦，而不是電話的 Xamarin.Forms 應用程式配置。
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 932b4aa9c865501284b02fc35e12dc8d7e7166fc
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244841"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>平板電腦和桌面應用程式的版面配置

Xamarin.Forms 支援所有支援的平台上可用的裝置類型，因此除了電話應用程式也可以執行：

* Ipad，
* Android 平板電腦
* Windows 平板電腦和桌上型電腦 （執行 Windows 10）。

此頁面會簡要討論：

* 支援[裝置類型](#Device_Types)，和
* 如何[最佳化](#optimize)手機和平板電腦的版面配置。

<a name="Device_Types" />

## <a name="device-types"></a>裝置類型

較大的螢幕裝置可供所有 Xamarin.Forms 所支援的平台。

### <a name="ipads-ios"></a>(iOS) Ipad

Xamarin.Forms 範本會自動包含 iPad 支援藉由設定**Info.plist > 裝置**設**通用**（這表示支援 iPhone 和 iPad）。

若要提供愉快啟動的經驗，並確保所有的裝置上使用全螢幕解析度，您應該確定[iPad 特定的啟動螢幕](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用分鏡腳本） 提供。 這可確保應用程式在 iPad 迷你、 iPad 與 iPad Pro 裝置上正確轉譯。

在 iOS 9 之前所有應用程式花費了完整的螢幕，在裝置上，但現在可以執行一些 Ipad[分割螢幕多工作業](~/ios/platform/multitasking.md)。
這表示您的應用程式可能需要只輕薄資料行 畫面上，畫面上或整個螢幕的寬度的 50%的側邊。

[![](tablet-images/ipad-sml.png "iPad 分割螢幕範例")](tablet-images/ipad.png#lightbox "iPad 分割畫面範例")

分割畫面功能表示您應該設計您的應用程式適用於儘量減少為 320 像素寬、 或最多可達 1366年像素。

### <a name="android-tablets"></a>Android 平板電腦

Android 的生態系統的各種支援的螢幕大小、 從小型到大型的平板電腦的電話。 Xamarin.Forms 可支援所有的螢幕大小，但做為與其他平台可能會想要調整您的使用者介面，較大的裝置。

當支援許多不同的螢幕解析度，您可以提供您在不同的大小，以最佳化使用者經驗的原生映像資源。
檢閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)文件集 (和特別[建立不同的螢幕大小的資源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) 如需有關如何建構您的 Android 應用程式中的檔案名稱和資料夾若要最佳化的映像資源加入您的應用程式專案。

### <a name="windows-tablets-and-desktops"></a>Windows 平板電腦和桌上型電腦

若要支援平板電腦和執行 Windows 的桌上型電腦，您必須使用[Windows UWP 支援](~/xamarin-forms/platform/windows/installation/index.md)，哪些組建通用 Windows 10 執行的應用程式。

Windows 平板電腦和桌上型電腦上執行的應用程式所能調整至任意維度另外執行全螢幕。

[![](tablet-images/splitscreen-sml.png "Windows 分割螢幕範例")](tablet-images/splitscreen.png#lightbox "Windows 分割畫面範例")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>平板電腦和桌上型電腦最佳化

您可以調整 Xamarin.Forms 取決於使用者介面是否電話或 tablet/桌面裝置正在使用。 這表示您可以最佳化大型螢幕裝置如平板電腦和桌上型電腦的使用者體驗。


### <a name="deviceidiom"></a>Device.Idiom

您可以使用[ `Device` ](~/xamarin-forms/platform/device.md)類別，以變更您的應用程式或使用者介面的行為。 使用`Device.Idiom`列舉型別，您可以

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

這種方法可以在個別的頁面配置，大幅變更，或甚至呈現較大的螢幕上的完全不同頁面展開。

### <a name="leveraging-masterdetailpage"></a>利用 MasterDetailPage

[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)非常適合使用較大的螢幕，特別是在何處使用在 iPad 上[ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/)提供原生 iOS 體驗。

檢閱[Xamarin 部落格文章](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)若要查看如何調整您的使用者介面，如此電話使用一種配置及較大的螢幕可以使用另一個 (具有`MasterDetailPage`)。



## <a name="related-links"></a>相關連結

- [Xamarin 部落格](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 範例](https://github.com/jamesmontemagno/myshoppe)
