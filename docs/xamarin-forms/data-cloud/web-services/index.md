---
title: Xamarin.Forms 和 Web 服務
description: 本指南說明如何與不同的 web 服務，以提供建立、 讀取、 更新和刪除 (CRUD) 至 Xamarin.Forms 應用程式的功能。 涵蓋的主題包括與 ASMX 服務、 WCF 服務、 REST 服務通訊。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659005"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms 和 Web 服務

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

這篇文章會逐步解說示範如何與不同的 web 服務通訊的 Xamarin.Forms 範例應用程式。 涵蓋的主題包括應用程式、 頁面、 資料模型的結構，並叫用 web 服務作業。

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[使用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web 服務 (ASMX) 提供建立傳送訊息透過 HTTP 使用簡易物件存取通訊協定 (SOAP) 的 web 服務的能力。 SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。 ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。 它們只需要了解如何傳送和接收 SOAP 訊息。 這篇文章會示範如何使用 Xamarin.Forms 應用程式的 ASMX web 服務。

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[使用 Windows Communication Foundation (WCF) Web 服務](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF 是 Microsoft 的統一的架構，用於建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，且可互通的分散式應用程式。 ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援 ASMX 提供相同的功能，透過 HTTP 的 SOAP 訊息。 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 WCF SOAP 服務。

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) 是建置 web 服務的架構樣式。 REST 要求都是透過 HTTP 使用網頁瀏覽器使用來擷取 web 網頁，並將資料傳送至伺服器的相同 HTTP 動詞命令。 這篇文章會示範如何使用 Xamarin.Forms 應用程式的 RESTful web 服務。
