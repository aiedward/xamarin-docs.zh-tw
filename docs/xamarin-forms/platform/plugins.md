---
title: 使用及建立 Xamarin.Forms 外掛程式
description: 本文說明如何使用及建立 Xamarin.Forms 外掛程式。 外掛程式通常可用來輕鬆地公開的原生平台功能。
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2016
ms.openlocfilehash: dff9fad0da30475a0fb91c0af76a25ea50d34439
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242553"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>使用及建立 Xamarin.Forms 外掛程式

存在於所有平台之間的許多原生平台功能，不過有稍微不同的應用程式開發介面。 開發人員撰寫外掛程式，以建立抽象跨平台介面也可以與其他人共用這些功能。

這些功能包括： 電池狀態、 指南針、 影片感應器、 地理位置，文字轉換語音，和更多。 外掛程式可讓由 Xamarin.Forms 應用程式輕鬆地存取這些功能。

## <a name="finding-and-adding-plugins"></a>尋找及加入外掛程式

Xamarin 社群已建立許多跨平台外掛程式 Xamarin.Forms-與相容的大型集合，請參閱：

[**Xamarin 外掛程式**](https://github.com/xamarin/plugins)

將 NuGet 封裝加入您專案的指引，請參閱我們的逐步解說上,[包括您的專案中的 NuGet 封裝](/visualstudio/mac/nuget-walkthrough/)。


## <a name="creating-plugins"></a>建立的外掛程式

它也可建立和發行您自己的外掛程式為 Nuget 封裝 （和 Xamarin 元件）。 許多現有的外掛程式都是開放原始碼，因此您可以檢閱其程式碼，以了解如何已 writtern。

例如，外掛程式下方的清單是所有的開放原始碼，和它們對應中的範例[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) > 一節：

- **文字轉換語音**James Montemagno 的&ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/TextToSpeech)和[NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)
- **電池狀態**James Montemagno 的&ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery)和[NuGet](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech/)

這些 Github 專案可以提供很好的起點來建立您自己跨平台的外掛程式，與這些指示[xamarin 建立外掛程式](https://github.com/xamarin/plugins#create-a-plugin-for-xamarin)。

### <a name="structuring-cross-platform-plugin-projects"></a>結構化的跨平台增益集專案

雖然有沒有特定的需求設計 NuGet 封裝，但有一些指導方針建立跨平台應用程式套件。

跨平台外掛程式通常應該包含下列元件：

- PCL 與表示外掛程式 API 介面
- iOS、 Android 和 Windows 類別程式庫與介面的實作。

讀取 James Montemagno 的[部落格文章](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/)描述 xamarin 建立外掛程式的程序。

最好避免直接從外掛程式參考 Xamarin.Forms。
其他開發人員嘗試使用外掛程式時，這樣就可以建立版本衝突問題。 改為嘗試設計應用程式開發介面，讓任何 Xamarin 或.NET 應用程式可以使用它。

### <a name="publishing-nuget-packages"></a>發行的 NuGet 封裝

NuGet 封裝有**nuspec**是定義在封裝中發佈您的專案中哪些部分的 xml 檔案的檔案。 **Nuspec**檔案還包含封裝，例如識別碼、 標題和作者的相關資訊。

請參閱[NuGet 的文件](http://docs.nuget.org/create/creating-and-publishing-a-package)如需有關建立和發佈 NuGet 套件。


## <a name="related-links"></a>相關連結

- [建立可重複使用的外掛程式的 Xamarin.Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [使用 （& s） 開發適用於 Xamarin （視訊） 的外掛程式](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
