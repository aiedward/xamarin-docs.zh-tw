---
title: 手動建立適用于 Xamarin 的 NuGet 套件
description: 本檔包含的秘訣可協助您建立以 Xamarin 平臺為目標的 NuGet 套件。 其中描述 NuGet 套件 Xamarin 設定檔、具有平臺相依性的 PCL Nuget，以及各種開放原始碼範例的連結。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3e67d2c329e5ea0ad91eebcc4301965682d647aa
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458000"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手動建立適用于 Xamarin 的 NuGet 套件

_此頁面包含一些秘訣，可協助您建立以 Xamarin 平臺為目標的 NuGet 套件。_

> [!NOTE]
> Xamarin Studio 6.2 (和 Visual Studio for Mac) 包括從 PCL、.NET Standard 或共用專案 _自動_ 產生 NuGet 套件的功能。 如需詳細資料，請參閱 [適用于程式碼共用](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) 的多平臺程式庫指南。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 套件 Xamarin 設定檔

[支援多個 .NET Framework 版本和設定檔](https://docs.nuget.org/create/enforced-package-conventions)的 NuGet 網站會討論如何支援不同的 Microsoft framework 和設定檔，但不包含 Xamarin 所使用的目標 Framework 名稱。

目前使用的主要 Xamarin 目標 framework 如下：

- **MonoAndroid** -Xamarin. Android
- **Xamarin** -xamarin [Unified API](~/cross-platform/macios/unified/index.md) (支援64位) 
- **Xamarin** -xamarin 的行動設定檔，相當於 Xamarin 和 Xamarin. Android API 介面。

此外，也有舊版 iOS [Classic API](~/cross-platform/macios/unified/index.md)的目標：

- **Monotouch.dll** -iOS Classic API

以這些全都為目標的 **nuspec** 檔案看起來像這樣：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述會忽略任何便攜類別庫。

大部分的 nuspec 檔案會指定目標 framework 的版本號碼，但如果您的元件與該目標 framework 的所有版本搭配使用，則它是選擇性的 **。** 因此，如果您的目標是 **lib\MonoAndroid** ，這表示它適用于任何版本的 Xamarin。

您可以使用一組數位來指定版本（不含小數點），也可以使用小數點來指定。 如果沒有小數點，NuGet 只會採用每個數位，並在每個數位之間插入 '. '，將它轉換成版本。

在上述的「MonoAndroid10」中，表示「Android 1.0」。 這只是表示專案的 [目標架構](~/android/app-fundamentals/android-api-levels.md) 必須 MonoAndroid 1.0 版或更高版本。 版本是在專案檔的 `<TargetFrameworkVersion>` 元素中指定。

釐清：

- **MonoAndroid403** 符合 Android 4.0.3 和較新的 (ie API 層級 15) 
- **Ios10 執行** 符合 Xamarin. iOS 1.0 和更新版本
- **Xamarin. ios 1.0** 也符合 Xamarin. ios 1.0 和更新版本

## <a name="pcl-nugets-with-platform-dependencies"></a>具有平臺相依性的 PCL Nuget

PCL 設定檔可存取哪些 .NET framework Api，而且它們絕對無法存取平臺特定程式碼。 這些協力廠商連結會討論使用 PCL 和原生 Api 來為 Xamarin 和其他平臺提供相容性之 NuGet 套件的不同方法：

- [如何讓便攜類別庫適用于您](https://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Bait 和交換器 PCL 訣竅](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [建立適用于 Xamarin 的 NuGet PCL](https://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

這是 [PCL 設定檔的外部清單及其 NuGet 目標名稱](https://portablelibraryprofiles.stephencleary.com) ，也是很有用的參考。

## <a name="examples"></a>範例

您可以參考的一些開放原始碼範例：

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) –使用系統 .net 撰寫您的應用程式，但將此程式庫放入，它會大幅加快 (view [source](https://github.com/paulcbetts/ModernHttpClient)) 。
- [**Splat**](https://www.nuget.org/packages/Splat/) –這是一個程式庫，可讓 (view [source](https://github.com/paulcbetts/Splat)) 的跨平臺作業。
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) -在 .net 上轉譯向量圖形的跨平臺程式庫 (view [source](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)) 。

## <a name="related-links"></a>相關連結

- [Nugetizer-3000 自動化 NuGet 建立](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)