---
title: 資料與雲端服務
description: Xamarin.Forms 應用程式可以使用 web 服務使用各種不同的技術，實作，而本指南會討論如何執行這項操作。
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327502"
---
# <a name="data--cloud-services"></a>資料與雲端服務

_Xamarin.Forms 應用程式可以使用 web 服務使用各種不同的技術，實作，而本指南會討論如何執行這項操作。_

在 Xamarin 平台上的跨平台 web 服務取用的簡介，請參閱 < [Web 服務的簡介](~/cross-platform/data-cloud/web-services/index.md)。

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[了解範例](~/xamarin-forms/data-cloud/walkthrough.md)

這篇文章會逐步解說示範如何與不同的 web 服務通訊的 Xamarin.Forms 範例應用程式。 涵蓋的主題包括應用程式、 頁面、 資料模型的結構，並叫用 web 服務作業。

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[使用 Web 服務](~/xamarin-forms/data-cloud/consuming/index.md)

本指南示範如何與不同的 web 服務，以提供建立、 讀取、 更新和刪除 (CRUD) 至 Xamarin.Forms 應用程式的功能。 涵蓋的主題包括與通訊[ASMX 服務](consuming/asmx.md)， [WCF 服務](consuming/wcf.md)， [REST 服務](consuming/rest.md)，以及[Azure Mobile Apps](consuming/azure.md)。

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[驗證 Web 服務的存取權](~/xamarin-forms/data-cloud/authentication/index.md)

本指南說明如何將驗證服務整合至 Xamarin.Forms 應用程式，讓使用者可以共用的後端，只有同時存取他們自己的資料。 涵蓋的主題包括[REST 服務中使用基本驗證](authentication/rest.md)，[驗證 OAuth 識別提供者使用 Xamarin.Auth 元件](authentication/oauth.md)，並使用內建的驗證所提供的機制[Azure Mobile Apps](authentication/azure.md)。

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[同步處理資料與 Web 服務](sync/index.md)

這篇文章說明如何將離線同步處理功能新增至 Xamarin.Forms 應用程式。 離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。 變更會儲存在本機資料庫中，並變更該裝置上線後，可以同步處理與 web 服務。

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[傳送推播通知](push-notifications/index.md)

這篇文章會示範如何將推播通知新增至 Xamarin.Forms 應用程式。 Azure 通知中樞提供可調整的推播基礎結構從任何後端傳送推播通知到任何行動平台，同時不需要不同的平台通知系統與通訊的後端的複雜性。

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[在雲端中儲存檔案](storage/index.md)

這篇文章會示範如何使用 Xamarin.Forms 來將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。 Azure 儲存體是用於儲存非結構化及結構化資料的可調整的雲端儲存體解決方案。

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[在雲端中搜尋資料](search/index.md)

這篇文章會示範如何使用 Microsoft Azure 搜尋服務程式庫，將 Azure 搜尋服務整合至 Xamarin.Forms 應用程式。 Azure 搜尋服務是雲端服務可編製索引和查詢上傳資料的功能。 這會移除傳統上相關聯的應用程式中實作搜尋功能的搜尋演算法複雜度與基礎結構需求。

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[將資料儲存在文件資料庫](cosmosdb/index.md)

本指南示範如何使用 Azure Cosmos DB.NET Standard 用戶端程式庫，若要將 Xamarin.Forms 應用程式中整合 Azure Cosmos DB 文件資料庫。 Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性、 可調整的資料庫服務的應用程式，需要能夠順暢調整和全域複寫的 NoSQL 資料庫。

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[使用認知服務加入智慧](cognitive-services/index.md)

本指南說明如何在 Xamarin.Forms 應用程式中使用某些 Microsoft 認知服務 Api。 認知服務是一組 Api、 Sdk 和適用於開發人員可以藉由新增功能，例如臉部辨識、 語音辨識和語言理解，讓他們的應用程式更有智慧服務。
