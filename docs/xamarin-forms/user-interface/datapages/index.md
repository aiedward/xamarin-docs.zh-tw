---
title: Xamarin.Forms DataPages
description: 本文介紹 Xamarin.Forms DataPages，提供 API，以快速並輕鬆地將資料來源繫結至預先建立的檢視。
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815883"
---
# <a name="xamarinforms-datapages"></a>Xamarin.Forms DataPages

![](~/media/shared/preview.png "此 API 目前為預覽狀態")

> [!IMPORTANT]
> 需要 DataPages [Xamarin.Forms 佈景主題](~/xamarin-forms/user-interface/themes/index.md)呈現的參考。

Xamarin.Forms DataPages 發表於發展 2016年，並可讓客戶試用並提供意見以預覽的形式。

DataPages 提供 API，以快速且輕鬆地繫結資料來源至預先建立的檢視。 清單項目和詳細資料頁面會自動轉譯資料，並可使用佈景主題。

若要查看發展專題示範的運作方式，請參閱[快速入門指南](get-started.md)。

[![](images/demo-sml.png "DataPages 範例應用程式")](images/demo.png#lightbox "DataPages 範例應用程式")

## <a name="introduction"></a>簡介

資料來源和相關聯的資料頁可讓開發人員快速且輕鬆地使用支援的資料來源，並呈現它使用佈景主題中的 UI scaffolding，您可以自訂的內建。

DataPages 會新增至 Xamarin.Forms 應用程式包括**Xamarin.Forms.Pages** Nuget 套件。

### <a name="data-sources"></a>Data Sources

預覽具有某些預先建置的資料來源可供使用：

* **JsonDataSource**
* **AzureDataSource** （分隔 Nuget）
* **AzureEasyTableDataSource** （分隔 Nuget）

請參閱[快速入門指南](get-started.md)的使用範例`JsonDataSource`。


### <a name="pages--controls"></a>頁面和控制項

下列網頁及控制項是包含以允許簡易的繫結至所提供的資料來源：

* **ListDataPage** – 請參閱 <<c2> [ 快速入門範例](get-started.md)。
* **DirectoryPage** – 已啟用群組清單。
* **PersonDetailPage** – 單一資料項目的特定物件類型 （連絡人項目） 的自訂檢視。
* **DataView** – 一種檢視來公開資料來源的資料以一般的方法。
* **CardView** – 有檢視，其中包含影像、 標題文字和說明文字的樣式。
* **HeroImage** – 影像呈現檢視。
* **ListItem** – 預先建置的原生 iOS 和 Android 的清單項目類似的版面配置檢視。

請參閱[DataPages 控制項參考](controls.md)的範例。



### <a name="under-the-hood"></a>背後原理

Xamarin.Forms 資料來源會遵守`IDataSource`介面。

Xamarin.Forms 基礎結構互動的資料來源，藉由下列屬性：

* `Data` -可以顯示的資料項目的唯讀清單。
* `IsLoading` – 布林值，指出資料是否已載入且可供轉譯。
* `[key]` – 索引子來擷取項目。

有兩種方法`MaskKey`和`UnmaskKey`，可用來隱藏 （或顯示） （即資料的項目屬性 不會加以轉譯）。
索引鍵對應至資料的項目物件上的具名的屬性。
