---
title: 平板電腦和傳統型應用程式的版面配置
description: 這篇文章說明如何最佳化適用於平板電腦，而不是電話的 Xamarin.Forms 應用程式版面配置。
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 9d1f54fa4753ba2ef44ba9b8b48a84a3ca932c4b
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233844"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>平板電腦和傳統型應用程式的版面配置

Xamarin.Forms 支援所有支援的平台上可用的裝置類型，因此除了手機應用程式也可以執行：

* Ipad
* Android 平板電腦，
* Windows 平板電腦和桌上型電腦 （執行 Windows 10）。

此頁面將簡短討論：

* 支援[裝置類型](#Device_Types)，及
* 如何[最佳化](#optimize)手機與平板電腦版面配置。

<a name="Device_Types" />

## <a name="device-types"></a>裝置類型

較大的螢幕裝置可供所有的 Xamarin.Forms 所支援的平台。

### <a name="ipads-ios"></a>Ipad (iOS)

Xamarin.Forms 範本會自動包含 iPad 支援藉由設定**Info.plist > 裝置**設為**通用**（這表示支援 iPhone 和 iPad）。

若要提供愉快的啟動體驗，並確保所有的裝置上使用全螢幕解析度，您應該確定[iPad 特定的啟動螢幕](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用分鏡腳本） 提供。 這可確保應用程式可在 iPad mini、 iPad 及 iPad Pro 裝置上正確轉譯。

在 iOS 9 之前所有的應用程式一職，全螢幕，在裝置上，但現在可以執行一些 Ipad[分割畫面多工作業](~/ios/platform/multitasking.md)。
這表示您的應用程式最多可能需要只是精簡型資料行旁邊的畫面中，50%的畫面上或整個螢幕的寬度。

[![](tablet-images/ipad-sml.png "iPad 分割畫面範例")](tablet-images/ipad.png#lightbox "iPad 分割畫面範例")

分割畫面功能表示您應該設計您的應用程式適用於儘量減少為 320 像素寬，或最高達 1366年個像素寬。

### <a name="android-tablets"></a>Android 平板電腦

Android 的生態系統有大量的受支援的螢幕大小，從小型到大型的平板電腦的電話。 Xamarin.Forms 可支援所有的螢幕大小，但做為與其他平台可能會想要調整您的使用者介面，較大的裝置。

當支援許多不同螢幕解析度，您可以提供不同的大小，以最佳化使用者經驗中的原生映像資源。
檢閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)文件 (特別[建立資源的不同螢幕大小](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) 如需有關如何建構的資料夾和 Android 應用程式中的檔案名稱若要最佳化的映像資源納入您的應用程式的專案。

### <a name="windows-tablets-and-desktops"></a>Windows 平板電腦和桌上型電腦

若要支援平板電腦和執行 Windows 的桌上型電腦，您必須使用[Windows UWP 支援](~/xamarin-forms/platform/windows/installation/index.md)，哪些組建在 Windows 10 執行的通用應用程式。

Windows 平板電腦和桌上型電腦上執行的應用程式所能調整至任意維度另外執行全螢幕。

[![](tablet-images/splitscreen-sml.png "Windows 分割畫面範例")](tablet-images/splitscreen.png#lightbox "Windows 分割畫面範例")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>針對平板電腦和桌上型電腦最佳化

是否有電話，您可以調整取決於您的 Xamarin.Forms 使用者介面或使用平板電腦/傳統型裝置。 這表示您可以最佳化大型螢幕裝置如平板電腦和桌上型電腦的使用者體驗。


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

這種方法可以擴充，在個別的頁面配置，大幅變更，或甚至是呈現在較大的螢幕上完全不同的網頁。

### <a name="leveraging-masterdetailpage"></a>利用 MasterDetailPage

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)非常適合大螢幕，尤其是在何處使用的 iPad [ `UISplitViewController` ](xref:UIKit.UISplitViewController)提供原生 iOS 體驗。

檢閱[此 Xamarin 部落格文章](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)若要查看如何調整您的使用者介面，以便電話使用一個版面配置和較大的畫面可以使用另一個 (具有`MasterDetailPage`)。



## <a name="related-links"></a>相關連結

- [Xamarin 部落格](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 範例](https://github.com/jamesmontemagno/myshoppe)
