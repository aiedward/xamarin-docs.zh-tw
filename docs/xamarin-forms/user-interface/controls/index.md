---
title: 控制項參考
description: 所有用來建立 Xamarin Forms 應用程式的使用者介面元素的描述。 本文列出組成 Xamarin 應用程式使用者介面的控制項群組。
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: b9436603c17eb8008f470e75a52a93e8e122034f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488111"
---
# <a name="controls-reference"></a>控制項參考

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

Xamarin 應用程式的使用者介面是由對應至每個目標平臺原生控制項的物件所構成。 這可讓 iOS、Android 和通用 Windows 平臺的平臺特定應用程式使用包含在[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)中的 Xamarin。

用來建立 Xamarin 應用程式使用者介面的四個主要控制項群組如下所示：

- [**頁面**](pages.md)
- [**大綱**](layouts.md)
- [**視圖**](views.md)
- [**資料格**](cells.md)

[Xamarin] 頁面通常會佔用整個畫面。 此頁面通常包含一個版面配置，其中包含視圖和可能的其他版面配置。 資料格是用來與[`TableView`](views.md#tableview)和[`ListView`](views.md#listview)連接的特製化元件。 類別圖，顯示通常用來在 Xamarin 中建立使用者介面的類型階層架構。您可以在 [ [xamarin] 控制項類別](~/xamarin-forms/internals/class-hierarchy.md)階層中找到表單。

在[**頁面**](pages.md)、[**版面**](layouts.md)配置、[**視圖**](views.md)和[**儲存格**](cells.md)的四篇文章中，每種類型的控制項都有其 API 檔的連結、描述其使用方式（如果有的話），以及一或多個範例程式（如果有的話）。 每一種類型的控制項也會伴隨一個螢幕擷取畫面，其中顯示在 iOS 和 Android 裝置上執行的[**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)範例中的頁面。 在每個螢幕擷取畫面底下，會連結至C#頁面的原始程式碼、對等的 xaml 頁面，以及（如果適用C#的話） xaml 頁面的程式碼後置檔案。

> [!NOTE]
> 頁面、版面配置和 Views 衍生自 `VisualElement` 類別。 `VisualElement` 類別提供各種可用於衍生類別的屬性、方法和事件。 如需詳細資訊，請參閱[VisualElement 屬性、方法和事件](common-properties.md)。

除了以 Xamarin 提供的控制項之外，還有協力廠商控制項可供使用。 如需詳細資訊，請參閱[協力廠商控制項](thirdparty.md)。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin Forms 控制項類別階層架構](~/xamarin-forms/internals/class-hierarchy.md)
- [API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
