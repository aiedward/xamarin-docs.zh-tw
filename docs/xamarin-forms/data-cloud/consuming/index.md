---
title: 使用 Web 服務
description: 本指南示範如何與不同的 web 服務，以提供建立、 讀取、 更新和刪除 (CRUD) 至 Xamarin.Forms 應用程式的功能。 涵蓋的主題包括與 ASMX 服務、 WCF 服務、 REST 服務和 Azure 行動應用程式進行通訊。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a4c842ea7fd37ade9be0a9cb3e3ff7e50a6d1491
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328186"
---
# <a name="consuming-web-services"></a>使用 Web 服務

（_t） 指南示範如何與不同的 web 服務，以提供建立、 讀取、 更新和刪除 (CRUD) 至 Xamarin.Forms 應用程式的功能。 涵蓋的主題包括與 ASMX 服務、 WCF 服務、 REST 服務和 Azure 行動應用程式進行通訊。

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[使用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

ASP.NET Web 服務 (ASMX) 提供建立傳送訊息透過 HTTP 使用簡易物件存取通訊協定 (SOAP) 的 web 服務的能力。 SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。 ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。 它們只需要了解如何傳送和接收 SOAP 訊息。 這篇文章會示範如何使用 Xamarin.Forms 應用程式的 ASMX web 服務。

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[使用 Windows Communication Foundation (WCF) Web 服務](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF 是 Microsoft 的統一的架構，用於建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，且可互通的分散式應用程式。 ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援 ASMX 提供相同的功能，透過 HTTP 的 SOAP 訊息。 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 WCF SOAP 服務。

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) 是建置 web 服務的架構樣式。 REST 要求都是透過 HTTP 使用網頁瀏覽器使用來擷取 web 網頁，並將資料傳送至伺服器的相同 HTTP 動詞命令。 這篇文章會示範如何使用 Xamarin.Forms 應用程式的 RESTful web 服務。

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)

Azure Mobile Apps 可讓您開發可調整的後端裝載在 Azure App Service 中支援的行動驗證、 離線同步處理，以及推播通知與應用程式。 本文章中，也就是只適用於 Azure Mobile Apps 使用 Node.js 後端，說明如何查詢、 插入、 更新和刪除儲存在 Azure Mobile Apps 執行個體中資料表的資料。

## <a name="related-links"></a>相關連結

- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
- [非同步支援概觀](~/cross-platform/platform/async.md)
