---
title: 以手動方式為 Xamarin 建立 NuGet 套件
description: 本文件包含可協助您建立 Xamarin 平台為目標的 NuGet 套件的秘訣。 它描述 NuGet 套件 Xamarin 設定檔，PCL 的 Nuget，利用平台相依性，並連結到各種開放原始碼的範例。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 2f66d8a3960741643013a1010162f52d283026d6
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855715"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>以手動方式為 Xamarin 建立 NuGet 套件

_此頁面包含可協助您建立 Xamarin 平台為目標的 NuGet 套件的一些祕訣。_

> [!NOTE]
> Xamarin Studio 6.2 （和 Visual Studio for Mac） 包括能夠_自動_產生從 PCL、.NET Standard，或共用專案的 NuGet 套件。 請參閱[適用於共用程式碼的多平台程式庫](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)指南以取得詳細資料。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 套件 Xamarin 設定檔

NuGet 網站的[支援多個.NET Framework 版本和設定檔](https://docs.nuget.org/create/enforced-package-conventions)討論如何支援不同的 Microsoft 架構和設定檔，但不包含 Xamarin 所使用之目標架構名稱。

使用中主要的 Xamarin 目標架構目前是：

* **MonoAndroid** - Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS[統一 API](~/cross-platform/macios/unified/index.md) （支援 64 位元）
* **Xamarin.Mac** -Xamarin.Mac 的行動裝置設定檔，這相當於 Xamarin.iOS 和 Xamarin.Android API 介面。

另外還有適用於較舊 iOS 為目標[傳統 API](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS 傳統 API

A **.nuspec**目標的所有這些檔案會看起來像：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述時則會忽略任何可攜式類別庫。

大部分 **.nuspec**檔案指定目標 framework 的版本號碼，但它是選擇性的如果您的組件會使用所有的目標 framework 版本。 因此，如果您的目標已**lib\MonoAndroid**這表示它適用於任何版本的 Xamarin.Android。

您可以指定一組數字沒有小數點的版本，或您可以指定使用小數位數。 小數點 NuGet 將只需要每個數字而不將它轉換成一個版本中，插入 '。 ' 每個數字之間。

在上述 「 MonoAndroid10"表示"Android 1.0"。 這只是表示專案組[目標 framework](~/android/app-fundamentals/android-api-levels.md)必須是 MonoAndroid 版本 1.0 或更新版本。 在指定的版本`<TargetFrameworkVersion>`專案檔中的項目。

若要釐清：

- **MonoAndroid403**符合 Android 4.0.3 及更新版本 （亦即 API 層級 15）
- **Xamarin.iOS10**符合 Xamarin.iOS 1.0 及更新版本
- **Xamarin.iOS1.0**也會比對 Xamarin.iOS 1.0 及更新版本

## <a name="pcl-nugets-with-platform-dependencies"></a>PCL 平台相依性的 Nuget

PCL 設定檔會有限制哪些.NET framework、 使用者可存取的 Api，而且肯定無法存取平台特定程式碼。 下列連結，第 3 方討論不同的方法，來建立使用 PCL 和原生 Api 來提供 Xamarin 及其他平台的相容性的 NuGet 封裝：

- [如何讓您的可攜式類別程式庫工作](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [誘導轉向 」 PCL 技巧](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [建立 NuGet PCL 可利用 Xamarin.iOS 的運作方式](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

這個外部[PCL 設定檔清單中的 NuGet 目標名稱](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)也是一個實用的參考。

## <a name="examples"></a>範例

您可以參考一些開放原始碼範例：

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) -撰寫您的應用程式使用 System.Net.Http，但卸除此文件庫中，就會有很大速度移 (檢視[來源](https://github.com/paulcbetts/ModernHttpClient))。
- [**Splat** ](https://www.nuget.org/packages/Splat/) – 應該是把事情跨平台程式庫 (檢視[來源](https://github.com/paulcbetts/Splat))。
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -呈現向量圖形，在.NET 上的跨平台程式庫 (檢視[來源](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec))。

## <a name="related-links"></a>相關連結

- [Nugetizer 3000 自動化 Nuget 建立](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [在專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
