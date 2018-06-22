---
title: 資料與雲端服務
description: 本指南將會檢驗如何執行這項操作和 Xamarin.Forms 應用程式可以使用 web 服務使用各種不同的技術，實作。
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
ms.locfileid: "31045051"
---
# <a name="data--cloud-services"></a>資料與雲端服務

_本指南將會檢驗如何執行這項操作和 Xamarin.Forms 應用程式可以使用 web 服務使用各種不同的技術，實作。_

如需 Xamarin 平台上的跨平台 web 服務取用的簡介，請參閱[Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)。

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[了解範例](~/xamarin-forms/data-cloud/walkthrough.md)

本文提供的逐步解說示範如何與不同的 web 服務通訊的 Xamarin.Forms 範例應用程式。 涵蓋的主題包括剖析應用程式、 頁面、 資料模型，並叫用 web 服務作業。

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[使用 Web 服務](~/xamarin-forms/data-cloud/consuming/index.md)

本指南示範如何與提供不同的網頁服務建立、 讀取、 更新和刪除 (CRUD) Xamarin.Forms 應用程式的功能。 涵蓋的主題包含與通訊[ASMX 服務](consuming/asmx.md)， [WCF 服務](consuming/wcf.md)， [REST 服務](consuming/rest.md)，和[Azure 行動應用程式](consuming/azure.md)。

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[驗證 Web 服務的存取權](~/xamarin-forms/data-cloud/authentication/index.md)

本指南說明如何整合到 Xamarin.Forms 應用程式，讓使用者共用在只具有自己的資料存取時的後端驗證服務。 涵蓋的主題包括[REST 服務搭配使用基本驗證](authentication/rest.md)， [OAuth 身分識別提供者進行驗證，使用 Xamarin.Auth 元件](authentication/oauth.md)，並使用內建的驗證所提供的機制[Azure 行動應用程式](authentication/azure.md)。

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[同步處理資料與 Web 服務](sync/index.md)

本文說明如何加入 Xamarin.Forms 應用程式中離線的同步處理功能。 離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。 變更會儲存在本機資料庫中，並變更裝置在線上時，一旦可以同步處理與 web 服務。

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[傳送推播通知](push-notifications/index.md)

本文示範如何將推播通知新增至 Xamarin.Forms 應用程式。 Azure 通知中心提供可擴充的推播基礎結構，將行動推播通知從任何後端傳送至任何行動裝置的平台，同時不需與各平台通知系統進行通訊的後端的複雜性。

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[在雲端中儲存檔案](storage/index.md)

本文示範如何使用 Xamarin.Forms，將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。 Azure 儲存體是可以用來儲存非結構化，和結構化資料的可擴充式雲端存放裝置解決方案。

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[在雲端中搜尋資料](search/index.md)

本文示範如何使用 Azure 搜尋整合 Xamarin.Forms 應用程式的 Microsoft Azure 搜尋程式庫。 Azure 搜尋是雲端服務可提供索引和查詢功能上傳的資料。 這會移除基礎結構需求和傳統上相關聯的應用程式中實作搜尋功能搜尋演算法的複雜度。

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[將資料儲存在文件資料庫](cosmosdb/index.md)

本指南示範如何使用 Azure Cosmos DB 標準.NET 用戶端程式庫，將 Azure Cosmos DB 文件資料庫整合到 Xamarin.Forms 應用程式。 Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性的可擴充資料庫服務，無縫式的小數位數和全域複寫所需要的應用程式的 NoSQL 資料庫。

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[使用認知服務加入智慧](cognitive-services/index.md)

本指南說明如何使用某些 Microsoft 認知 Services Api 中的 Xamarin.Forms 應用程式。 認知的服務是一組應用程式開發介面、 Sdk 和藉由新增功能，例如臉部辨識、 語音辨識和語言的了解，使其應用程式更聰明的開發人員可使用的服務。
