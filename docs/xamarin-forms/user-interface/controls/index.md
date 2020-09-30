---
title: 控制項參考
description: 用來建立應用程式的所有使用者介面元素的描述 Xamarin.Forms 。 本文列出組成應用程式使用者介面的控制項群組 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 60c58ee17f68a12e3e51170f143adc9dc204e275
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562765"
---
# <a name="controls-reference"></a>控制項參考

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

應用程式的使用者介面 Xamarin.Forms 是用來建立對應至每個目標平臺之原生控制項的物件。 這可讓 iOS、Android 和通用 Windows 平臺的平臺特定應用程式使用 .NET Standard 連結 Xamarin.Forms [庫](~/cross-platform/app-fundamentals/net-standard.md)中包含的程式碼。

用來建立應用程式使用者介面的四個主要控制項群組如下所示 Xamarin.Forms ：

- [**頁面**](pages.md)
- [**版面配置**](layouts.md)
- [**檢視**](views.md)
- [**資料格**](cells.md)

Xamarin.Forms頁面通常會佔用整個畫面。 頁面通常會包含配置，其中包含視圖，以及其他可能的版面配置。 資料格是與和連接時所使用的特製化元件 [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) 。 在 Xamarin.Forms [ Xamarin.Forms 控制項類別](~/xamarin-forms/internals/class-hierarchy.md)階層架構中，顯示通常用來建立使用者介面之類型階層的類別圖表。

在 [**頁面**](pages.md)、 [**版面**](layouts.md)配置、 [**視圖**](views.md)及資料 [**格**](cells.md)的四篇文章中，每種類型的控制項都有其 API 檔的連結、描述其使用方式 (（) 如果有的話），以及一或多個範例程式 (（如果有的話）) 。 每種類型的控制項也會隨附一個螢幕擷取畫面，其中顯示在 iOS 和 Android 裝置上執行之 [**FormsGallery**](/samples/xamarin/xamarin-forms-samples/formsgallery) 範例中的頁面。 每個螢幕擷取畫面都是 c # 頁面的原始程式碼連結、對等的 XAML 頁面，以及在 XAML 頁面的 c # 程式碼後端檔案適當) 時 (。

> [!NOTE]
> 頁面、版面配置和視圖都衍生自 `VisualElement` 類別。 `VisualElement`類別會提供各種可用於衍生類別的屬性、方法和事件。 如需詳細資訊，請參閱 [VisualElement 屬性、方法和事件](common-properties.md)。

除了提供的控制項之外，還 Xamarin.Forms 可以使用協力廠商控制項。 如需詳細資訊，請參閱 [協力廠商控制項](thirdparty.md)。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Controls 類別階層](~/xamarin-forms/internals/class-hierarchy.md)
- [API 檔](/dotnet/api/xamarin.forms?view=xamarin-forms)