---
title: 手動建立適用于 Xamarin 的 NuGet 套件
description: 本檔包含的秘訣可協助您建立以 Xamarin 平臺為目標的 NuGet 套件。 其中說明 NuGet 套件 Xamarin 設定檔、具有平臺相依性的 PCL Nuget，以及各種開放原始碼範例的連結。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 16b8f303555bc2f45516c3c060c0d2482f9c4954
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728222"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手動建立適用于 Xamarin 的 NuGet 套件

_此頁面包含一些秘訣，可協助您建立以 Xamarin 平臺為目標的 NuGet 套件。_

> [!NOTE]
> Xamarin Studio 6.2 （和 Visual Studio for Mac）包含從 PCL、.NET Standard 或共用專案_自動_產生 NuGet 套件的能力。 如需詳細資訊，請參閱[程式碼共用](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)的多平臺程式庫指南。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 套件 Xamarin 設定檔

NuGet 網站[支援多個 .NET Framework 版本和設定檔](https://docs.nuget.org/create/enforced-package-conventions)討論如何支援不同的 Microsoft 架構和設定檔，但不包含 Xamarin 所使用的目標 Framework 名稱。

現今使用的主要 Xamarin 目標架構為：

- **MonoAndroid** -Xamarin. Android
- [Unified API](~/cross-platform/macios/unified/index.md) （支援64位 **）的 xamarin. ios**
- **Xamarin** -xamarin 的行動設定檔，相當於 Xamarin. IOS 和 Xamarin. Android API 介面。

也有舊版 iOS [Classic API](~/cross-platform/macios/unified/index.md)的目標：

- **MonoTouch** -iOS Classic API

以這些專案為目標的**nuspec**檔案看起來會像這樣：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述會忽略任何可移植的類別庫。

大部分的 nuspec 檔案會指定目標 framework 的版本號碼，但如果您的元件與該目標 framework 的所有版本搭配使用，則它是選擇性的 **。** 因此，如果您的目標是**lib\MonoAndroid** ，這就表示它適用于任何版本的 Xamarin. Android。

您可以使用不含小數點的數位集來指定版本，或者可以使用小數點來指定它。 如果沒有小數點，NuGet 只會採用每個數位，並在每個數位之間插入 '. '，以將它轉換成版本。

在上述「MonoAndroid10」中，表示「Android 1.0」。 這只是表示專案的[目標架構](~/android/app-fundamentals/android-api-levels.md)需要 MonoAndroid 1.0 或更高版本。 版本是在專案檔的 `<TargetFrameworkVersion>` 元素中指定。

釐清：

- **MonoAndroid403**符合 Android 4.0.3 和更新版本（ie API 層級15）
- **IOS10**符合 Xamarin. iOS 1.0 和更新版本
- **Xamarin. ios 1.0**也符合 Xamarin. ios 1.0 和更新版本

## <a name="pcl-nugets-with-platform-dependencies"></a>具有平臺相依性的 PCL Nuget

PCL 設定檔會限制其可存取的 .NET framework Api，而且它們當然無法存取平臺特定的程式碼。 這些協力廠商連結討論使用 PCL 和原生 Api 來建立 NuGet 套件的不同方法，以提供 Xamarin 和其他平臺的相容性：

- [如何讓可移植的類別庫適用于您](https://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Bait 和交換器 PCL 訣竅](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [建立適用于 Xamarin 的 NuGet PCL](https://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

這份[具有 NuGet 目標名稱的 PCL 設定檔外部清單](https://portablelibraryprofiles.stephencleary.com)也是有用的參考。

## <a name="examples"></a>範例

您可以參考的一些開放原始碼範例：

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) –使用 System .Net. Http 撰寫您的應用程式，但將此程式庫放入，它會大幅加快（view [source](https://github.com/paulcbetts/ModernHttpClient)）。
- [**Splat**](https://www.nuget.org/packages/Splat/) –可讓您跨平臺進行的工作庫，應為（view [source](https://github.com/paulcbetts/Splat)）。
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) -跨平臺程式庫，用於在 .net 上轉譯向量圖形（view [source](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)）。

## <a name="related-links"></a>相關連結

- [Nugetizer-3000 自動化 NuGet 建立](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
