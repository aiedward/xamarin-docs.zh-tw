---
title: DataPages
ms.topic: article
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 10c473656947dfea8bf832ae5a5704897ef4ce63
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="datapages"></a>DataPages

![](~/media/shared/preview.png "這個 API 目前處於預覽狀態")

> [!IMPORTANT]
> 需要 DataPages [Xamarin.Forms 佈景主題](~/xamarin-forms/user-interface/themes/index.md)來呈現的參考。

Xamarin.Forms DataPages 已宣佈在 Evolve 2016 且可做為客戶再試一次，以及提供意見反應的預覽。

DataPages 提供 API，以快速且輕鬆地資料來源繫結至預先建立的檢視。 清單項目和詳細資料 頁面會自動轉譯資料，並且可以使用主題自訂。

若要查看 Evolve 專題示範的運作方式，請參閱[入門指南](get-started.md)。

[ ![](images/demo-sml.png "DataPages 範例應用程式")](images/demo.png "DataPages 範例應用程式")

## <a name="introduction"></a>簡介

資料來源和相關聯的資料頁可讓開發人員快速且輕鬆地使用支援的資料來源，並呈現可自訂的 scaffolding，UI 的內建使用佈景主題。

Xamarin.Forms 應用程式中加入 DataPages 包含**Xamarin.Forms.Pages** Nuget 封裝。

### <a name="data-sources"></a>Data Sources

預覽具有某些預先建立的資料來源可供使用：

* **JsonDataSource**
* **AzureDataSource** （分隔 Nuget）
* **AzureEasyTableDataSource** （分隔 Nuget）

請參閱[入門指南](get-started.md)的使用範例`JsonDataSource`。


### <a name="pages--controls"></a>網頁及控制項

下列網頁及控制項的以允許簡易的繫結至所提供的資料來源：

* **ListDataPage** – 請參閱[快速入門範例](get-started.md)。
* **DirectoryPage** – 具有已啟用的群組清單。
* **PersonDetailPage** – 單一資料項目的特定物件類型 （連絡人項目） 的自訂檢視。
* **DataView** – 檢視，以將資料從來源以一般方式公開。
* **CardView** – 樣式包含影像、 標題文字和描述文字檢視。
* **HeroImage** – 影像呈現檢視。
* **ListItem** – 預先建立的檢視具有版面配置類似於原生 iOS 和 Android 的清單項目。

請參閱[DataPages 控制參考](controls.md)範例。



### <a name="under-the-hood"></a>背後原理

Xamarin.Forms 資料來源就會遵守`IDataSource`介面。

Xamarin.Forms 基礎結構互動與資料來源，藉由下列屬性：

* `Data` – 唯讀的資料可以顯示的項目清單。
* `IsLoading` – 布林值，指出資料是否已載入及可用的轉譯。
* `[key]` – 索引子以擷取項目。

有兩種方法`MaskKey`和`UnmaskKey`，可用來隱藏 （或者顯示） （即資料項目屬性 防止它們呈現）。
索引鍵會對應至資料的項目物件上的具名的屬性。

