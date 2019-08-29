---
title: 控制項參考
description: 所有用來建立 Xamarin Forms 應用程式的使用者介面元素的描述。 這篇文章會列出組成 Xamarin.Forms 應用程式的使用者介面的控制項群組。
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: f3aa8249b0e94721b8e35437997b74b24e31f689
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065243"
---
# <a name="controls-reference"></a>控制項參考

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

Xamarin 應用程式的使用者介面是由對應至每個目標平臺原生控制項的物件所構成。 這可讓 iOS、Android 和通用 Windows 平臺的平臺特定應用程式使用包含在[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)中的 Xamarin。

用來建立 Xamarin 應用程式使用者介面的四個主要控制項群組如下所示:

- [**頁面**](pages.md)
- [**版面配置**](layouts.md)
- [**檢視**](views.md)
- [**資料格**](cells.md)

Xamarin.Forms 頁面通常佔滿整個螢幕。 頁面通常會包含版面配置，其中包含檢視，以及可能是其他版面配置。 資料格都是特殊化的元件，與使用[ `TableView` ](views.md#tableView)並[ `ListView` ](views.md#listView)。 類別圖, 顯示通常用來在 Xamarin 中建立使用者介面的類型階層架構。您可以在 [ [xamarin] 控制項類別](~/xamarin-forms/internals/class-hierarchy.md)階層中找到表單。

四個文件上[**頁**](pages.md)， [**配置**](layouts.md)， [**檢視**](views.md)，並[**資料格**](cells.md)，每種控制項類型描述其 API 文件、 說明其用途 （如果有的話），發行項及一或多個範例程式的連結 （如果有的話）。 每一種類型的控制項也會伴隨一個螢幕擷取畫面, 其中顯示在 iOS 和 Android 裝置上執行的[**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)範例中的頁面。 以下每個螢幕擷取畫面是 C# 頁面上，對等的 XAML 頁面中，原始程式碼的連結，以及 （當適當） XAML 頁面的 C# 程式碼後置檔案。

> [!NOTE]
> 頁面、版面配置和視圖皆衍生自`VisualElement`類別。 `VisualElement`類別提供各種可用於衍生類別的屬性、方法和事件。 如需詳細資訊, 請參閱[VisualElement 屬性、方法和事件](common-properties.md)。

除了以 Xamarin 提供的控制項之外, 還有協力廠商控制項可供使用。 如需詳細資訊, 請參閱[協力廠商控制項](thirdparty.md)。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin Forms 控制項類別階層架構](~/xamarin-forms/internals/class-hierarchy.md)
- [API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
