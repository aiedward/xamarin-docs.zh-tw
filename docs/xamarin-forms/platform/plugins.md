---
title: 使用和建立 Xamarin.Forms 外掛程式
description: 這篇文章說明如何使用及建立 Xamarin.Forms 外掛程式。 外掛程式通常會用來輕鬆地公開原生平台功能。
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/05/2018
ms.openlocfilehash: 4d121c2dfcca380e1735da1a4ca47c42d1957b8a
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854736"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>使用和建立 Xamarin.Forms 外掛程式

有許多存在跨所有平台的原生平台功能，但有稍微不同的 Api。 適用於開發人員可以使用這些功能的方法之一是藉由建立跨平台抽象的介面，並接著在各種平台上實作該介面。 接著，Xamarin.Forms 應用程式存取使用這些平台實作[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

開發人員可以共用這項工作，藉由撰寫_外掛程式_並將其發佈至 NuGet。

> [!NOTE]
> 許多先前僅能透過外掛程式的跨平台功能現在是開放原始碼的一部分**[Xamarin.Essentials](~/essentials/index.md)** 程式庫。 這些功能包括： 電池狀態、 羅盤、 動作感應器、 地理位置、 文字轉換語音，和更多。 未來**Xamarin.Essentials**會用於 Xamarin.Forms 應用程式的跨平台功能的主要來源。 雖然開發人員仍然可以建立和發行外掛程式，請考慮參與**Xamarin.Essentials**。

## <a name="finding-and-adding-plugins"></a>尋找及加入外掛程式

Xamarin 社群已建立許多跨平台外掛程式與 Xamarin.Forms 相容。 大型集合位於：

[**Xamarin 外掛程式**](https://github.com/xamarin/XamarinComponents)

將 NuGet 套件新增至您的專案的指南，請參閱我們的逐步解說，在[在專案中包含 NuGet 套件](/visualstudio/mac/nuget-walkthrough/)。

## <a name="creating-plugins"></a>建立外掛程式

它也可建立和發行您自己的外掛程式為 Nuget 套件 （及 Xamarin 元件）。 許多現有的外掛程式都是開放原始碼，因此您可以檢閱其程式碼，以了解如何已 writtern。

比方說，下列的外掛程式清單是所有的開放原始碼，和它們對應中的一些範例[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)區段：

- **文字轉換語音**James Montemagno 所&ndash; [GitHub](https://github.com/jamesmontemagno/TextToSpeechPlugin)和[NuGet  ](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech)
- **Stav Baterie** James Montemagno 所&ndash; [GitHub](https://github.com/jamesmontemagno/BatteryPlugin)和[NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)

Github 專案，才可以提供很好的起點來建立您自己跨平台的外掛程式，一樣的這些指示[建立適用於 Xamarin 的外掛程式](https://github.com/xamarin/XamarinComponents#create-a-plugin-for-xamarin)。

### <a name="structuring-cross-platform-plugin-projects"></a>結構化和跨平台外掛程式的專案

雖然有沒有特定的需求設計的 NuGet 套件，但有一些指導方針建立跨平台應用程式封裝。

在過去，跨平台的外掛程式通常包括下列元件：

- 表示為外掛程式 API 介面的 PCL
- iOS、 Android 和通用 Windows 平台 (UWP) 的類別程式庫與介面的實作。

讀取 James Montemagno 的[部落格文章](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/)描述為 Xamarin 建立外掛程式的程序。

最近，外掛程式可以是使用來建立單一多目標平台。 這個方法會討論 James Montemagno[部落格文章](https://montemagno.com/converting-xamarin-libraries-to-sdk-style-multi-targeted-projects/)。 這個方法會在連結上方，James Montemagno 的外掛程式和格式中也使用**Xamarin.Essentials**。

建議您最好避免直接從外掛程式參考 Xamarin.Forms。
其他開發人員嘗試使用外掛程式時，這可以建立版本衝突問題。 請改為試用設計 API，讓它可供任何 Xamarin 或.NET 應用程式。

### <a name="publishing-nuget-packages"></a>發行 NuGet 套件

NuGet 套件**nuspec**是定義在封裝中發佈您的專案的哪些部分的 xml 檔案的檔案。 **Nuspec**檔案也包含封裝，例如識別碼、 標題和作者的相關資訊。

請參閱[NuGet 的文件](/nuget/create-packages/creating-a-package.md)如需有關建立和發行 NuGet 套件。

## <a name="related-links"></a>相關連結

- [適用於 Xamarin.Forms 建立可重複使用的外掛程式](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [使用 & 開發適用於 Xamarin （影片） 的外掛程式](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
