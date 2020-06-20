---
title: Xamarin.Forms和 Web 服務
description: 本指南說明如何與不同的 web 服務通訊，以提供應用程式的建立、讀取、更新和刪除（CRUD）功能 Xamarin.Forms 。 涵蓋的主題包括與 .ASMX 服務、WCF 服務、REST 服務進行通訊。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b2613b94d2c347d9bc6a94086f869b07ab8a55b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131881"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms和 Web 服務

## <a name="introduction"></a>[簡介](introduction.md)

本文提供 Xamarin.Forms 範例應用程式的逐步解說，示範如何與不同的 web 服務通訊。 涵蓋的主題包括應用程式的剖析、頁面、資料模型，以及叫用 web 服務作業。

## <a name="consume-an-aspnet-web-service-asmx"></a>[使用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web Services （.ASMX）可讓您建立 Web 服務，透過 HTTP 使用簡單的物件存取通訊協定（SOAP）來傳送訊息。 SOAP 是一種與平臺無關且與語言無關的通訊協定，可用於建立和存取 web 服務。 .ASMX 服務的取用者不需要知道用來執行服務的平臺、物件模型或程式設計語言等任何專案。 他們只需要瞭解如何傳送和接收 SOAP 訊息。 本文示範如何從應用程式取用 .ASMX web 服務 Xamarin.Forms 。

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[使用 Windows Communication Foundation （WCF） Web 服務](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF 是 Microsoft 用來建立服務導向應用程式的統一架構。 它可以讓開發人員建立安全、可靠、交易和互通的分散式應用程式。 ASP.NET Web 服務（.ASMX）和 WCF 之間有差異，但請務必瞭解 WCF 支援的功能與 .ASMX 所提供的相同，也就是透過 HTTP 的 SOAP 訊息。 本文示範如何從應用程式使用 WCF SOAP 服務 Xamarin.Forms 。

## <a name="consume-a-restful-web-service"></a>[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)

具像狀態傳輸（REST）是用來建立 web 服務的架構樣式。 REST 要求是使用 web 瀏覽器用來抓取網頁和將資料傳送到伺服器的相同 HTTP 動詞，透過 HTTP 進行。 本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。
