---
title: 手動建立 Xamarin NuGet 套件
description: 此頁面包含一些秘訣可協助您建立 Xamarin 平台為目標的 NuGet 封裝。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 510c27cb54b91c837ca6f6b7a93f944f13097f0c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手動建立 Xamarin NuGet 套件

_此頁面包含一些秘訣可協助您建立 Xamarin 平台為目標的 NuGet 封裝。_

> [!NOTE]
> Xamarin Studio 6.2 （與 Visual Studio for Mac） 的能力_自動_從 PCL 中，.NET 標準或共用專案中產生 NuGet 封裝。 請參閱[多平台程式庫程式碼共用](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)指南以取得詳細資料。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 封裝 Xamarin 設定檔

NuGet 網站[支援多個.NET Framework 版本和設定檔](https://docs.nuget.org/create/enforced-package-conventions)討論如何支援不同的 Microsoft 架構和設定檔，但不包括 Xamarin 所使用的目標架構名稱。

使用中主要的 Xamarin 的目標 framework 今天是：

* **MonoAndroid** -Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS[統一的 API](~/cross-platform/macios/unified/index.md) （支援 64 位元）
* **Xamarin.Mac** -Xamarin.Mac 的行動裝置設定檔，它就相當於 Xamarin.iOS 和 Xamarin.Android API 介面。

另外還有適用於較舊 iOS 目標[傳統 API](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS 傳統應用程式開發介面

A **.nuspec**目標所有這些檔案會看起來像：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述會忽略任何可攜式類別庫。

大部分 **.nuspec**檔案指定的目標 framework 的版本號碼，但它是選擇性的如果您的組件適用於所有的目標 framework 版本。 因此，如果您的目標是**lib\MonoAndroid**這表示它適用於任何版本的 Xamarin.Android。

您可以指定不含小數點的數字集合的版本，或您可以指定使用小數位數。 不含小數點 NuGet 將只需要每個數字並使其成為版本插入 '。 ' 每個數字之間。

在上述 「 MonoAndroid10"表示 Android 「 1.0 」。 這就表示專案的[目標 framework](~/android/app-fundamentals/android-api-levels.md)必須是 MonoAndroid 1.0 或更高版本。 在指定的版本`<TargetFrameworkVersion>`專案檔中的項目。

若要釐清：

- **MonoAndroid403**符合 Android 4.0.3 及更新版本 （亦即應用程式開發介面層級 15）
- **Xamarin.iOS10**符合 Xamarin.iOS 1.0 及更新版本
- **Xamarin.iOS1.0**也會比對 Xamarin.iOS 1.0 及更新版本


## <a name="pcl-nugets-with-platform-dependencies"></a>PCL NuGets 與平台相依性

PCL 設定檔僅限於哪些.NET framework 應用程式開發介面，他們可以存取，並確實無法存取平台專屬的程式碼。 這些第 3 合作對象連結將討論不同的方法來建立使用 Xamarin 和其他平台提供相容性的 PCL 和原生 Api 的 NuGet 封裝：

- [如何為您建立可攜式類別程式庫工作](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [上鉤 and 交換器 PCL 訣竅](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [建立 NuGet PCL 搭配 Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

這個外部[PCL 設定檔的 NuGet 目標名稱清單](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)也是很有用的參考。

## <a name="examples"></a>範例

您可以參考一些開放原始碼範例：

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) – 撰寫應用程式使用 System.Net.Http，但卸除此文件庫中，就會有很大速度移 (檢視[來源](https://github.com/paulcbetts/ModernHttpClient))。
- [**Splat** ](https://www.nuget.org/packages/Splat/) -應該是為了讓事情跨平台程式庫 (檢視[來源](https://github.com/paulcbetts/Splat))。
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -呈現向量圖形.net 的跨平台程式庫 (檢視[來源](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec))。


## <a name="related-links"></a>相關連結

- [Nugetizer 3000 自動化 Nuget 建立](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [更新 NuGets ios 64 位元](http://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [包括您的專案中的 NuGet](/visualstudio/mac/nuget-walkthrough/index.md)
