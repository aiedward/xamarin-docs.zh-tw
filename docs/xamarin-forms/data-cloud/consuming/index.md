---
title: 使用 Web 服務
description: 本指南示範如何與提供不同的網頁服務建立、 讀取、 更新和刪除 (CRUD) Xamarin.Forms 應用程式的功能。 涵蓋的主題包含與 ASMX 服務、 WCF 服務、 REST 服務、 Azure 行動應用程式和 Amazon Web Services 通訊。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 530b57c009a1f76d3756d7315856f74b6cda2f66
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-web-services"></a>使用 Web 服務

_本指南示範如何與提供不同的網頁服務建立、 讀取、 更新和刪除 (CRUD) Xamarin.Forms 應用程式的功能。涵蓋的主題包含與 ASMX 服務、 WCF 服務、 REST 服務、 Azure 行動應用程式和 Amazon Web Services 通訊。_

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[使用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

ASP.NET Web 服務 (ASMX) 提供建立傳送訊息透過 HTTP 使用簡易物件存取通訊協定 (SOAP) 的 web 服務的能力。 SOAP 是平台和語言無關的通訊協定，來建置和存取 web 服務。 ASMX 服務的取用者不需要了解平台、 物件模型或用來實作服務的程式設計語言的任何項目。 它們只需要了解如何傳送和接收 SOAP 訊息。 本文將示範如何使用 ASMX web 服務從 Xamarin.Forms 應用程式。

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[使用 Windows Communication Foundation (WCF) Web 服務](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF 是 Microsoft 的統一的架構建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，以及可互通的分散式應用程式。 ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援 ASMX 提供的相同功能，透過 HTTP SOAP 訊息。 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 WCF SOAP 服務。

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) 為架構的樣式，來建置 web 服務。 REST 要求都是透過 HTTP 使用網頁瀏覽器使用來擷取 web 網頁，並將資料傳送至伺服器的相同 HTTP 動詞命令。 這篇文章會示範如何使用 RESTful web 服務從 Xamarin.Forms 應用程式。

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)

Azure 行動應用程式可讓您與裝載在 Azure 應用程式服務中，以支援行動裝置的驗證、 離線同步處理，及推播通知的可擴充後端開發應用程式。 只適用於使用 Node.js 後端的 Azure 行動應用程式，這篇文章說明如何查詢、 插入、 更新和刪除儲存在 Azure 行動應用程式執行個體中資料表的資料。

## <a name="consuming-an-amazon-simpledb-servicexamarin-formsdata-cloudconsumingawsmd"></a>[使用 Amazon SimpleDB 服務](~/xamarin-forms/data-cloud/consuming/aws.md)

Amazon SimpleDB 是 web 服務，提供儲存和查詢 Amazon 雲端中的資料的能力。 本文說明如何使用 AWS SDK for.NET 進行查詢、 建立和取代，並刪除儲存在 SimpleDB 服務中的資料。


## <a name="related-links"></a>相關連結

- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
- [非同步支援概觀](~/cross-platform/platform/async.md)
