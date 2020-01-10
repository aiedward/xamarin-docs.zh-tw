---
title: Xamarin. Forms DataPages
description: 本文介紹 DataPages，其提供 API 來快速且輕鬆地將資料來源系結至預先建立的視圖。
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 4569240d7419254bad41957d30b4ad652c7a3f4e
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727936"
---
# <a name="xamarinforms-datapages"></a>Xamarin. Forms DataPages

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages 需要有 Xamarin 主題參考才能呈現。 這牽涉到將 [ [xamarin](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) ] nuget 套件安裝到您的專案中，後面接著 [ [xamarin](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) [] 或 [](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) node.js] nuget 套件。

DataPages 已于演進2016宣佈，並可供客戶試用並提供意見反應。

DataPages 提供 API，以快速且輕鬆地繫結資料來源至預先建立的檢視。 [清單專案] 和 [詳細資料] 頁面會自動轉譯資料，而且可以使用主題進行自訂。

若要查看進化專題示範如何運作，請參閱[快速入門手冊](get-started.md)。

[![](images/demo-sml.png "DataPages Sample Application")](images/demo.png#lightbox "DataPages Sample Application")

## <a name="introduction"></a>簡介

資料來源和相關聯的資料頁可讓開發人員快速且輕鬆地取用支援的資料來源，並使用可以使用主題自訂的內建 UI 樣板來呈現它。

DataPages 會加入至 Xamarin 應用程式，方法是包含 [ **xamarin** ] NuGet 套件。

### <a name="data-sources"></a>資料來源

預覽中有一些可供使用的預先建立資料來源：

* **JsonDataSource**
* **AzureDataSource** （個別 NuGet）
* **AzureEasyTableDataSource** （個別 NuGet）

如需使用 `JsonDataSource`的範例，請參閱[快速入門手冊](get-started.md)。

### <a name="pages--controls"></a>頁面 & 控制項

包含下列頁面和控制項可讓您輕鬆地系結至提供的資料來源：

* **ListDataPage** –請參閱使用者入門[範例](get-started.md)。
* **DirectoryPage** –已啟用群組的清單。
* **PersonDetailPage** –針對特定物件類型（連絡人項目）自訂的單一資料項目視圖。
* **DataView** –以一般方式從來源公開資料的視圖。
* **CardView** –樣式化的視圖，其中包含影像、標題文字和描述文字。
* **HeroImage** –影像呈現視圖。
* [專案 **] –預先**建立的視圖，其版面配置類似于原生 IOS 和 Android 清單專案。

如需範例，請參閱[DataPages 控制項參考](controls.md)。

### <a name="under-the-hood"></a>幕後

[Xamarin] 資料來源會遵循 `IDataSource` 介面。

[Xamarin] 基礎結構會透過下列屬性與資料來源進行互動：

* `Data` –可以顯示之資料項目的唯讀清單。
* `IsLoading` –布林值，指出資料是否已載入並可供呈現。
* `[key]` –用來抓取元素的索引子。

有兩種方法 `MaskKey` 和 `UnmaskKey` 可用來隱藏（或顯示）資料項目屬性（ie）。 防止轉譯這些專案）。
索引鍵會對應至資料項目物件上的名稱屬性。
