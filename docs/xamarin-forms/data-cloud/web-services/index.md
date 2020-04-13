---
title: Xamarin.表單和 Web 服務
description: 本指南介紹了如何與不同的 Web 服務通訊,以便向 Xamarin.Forms 應用程式提供創建、讀取、更新和刪除 (CRUD) 功能。 涵蓋的主題包括與 ASMX 服務、WCF 服務、REST 服務通訊。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 799a0a97f7c1a212b10dc62f8b3e7bd2cf2060c8
ms.sourcegitcommit: bf3b5925018e1bd6a00505e9f37500761b66809d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395449"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.表單和 Web 服務

## <a name="introduction"></a>[簡介](introduction.md)

本文提供了 Xamarin.Forms 範例應用程式的演練,演示如何與不同的 Web 服務進行通訊。 涵蓋的主題包括應用程式的分析、頁面、資料模型和調用 Web 服務操作。

## <a name="consume-an-aspnet-web-service-asmx"></a>[使用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web 服務 (ASMX) 提供使用簡單物件存取協定 (SOAP) 透過 HTTP 生成消息的 Web 服務。 SOAP 是一個獨立於平臺且獨立於語言的協定,用於構建和訪問 Web 服務。 ASMX 服務的消費者不需要瞭解用於實現該服務的平臺、物件模型或程式設計語言。 他們只需要瞭解如何發送和接收 SOAP 消息。 本文展示如何使用 Xamarin.Forms 應用程式中的 ASMX Web 服務。

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[使用 Windows 通訊基礎 (WCF) Web 服務](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF 是Microsoft構建面向服務的應用程式的統一框架。 它使開發人員能夠構建安全、可靠、事務和可互通的分散式應用程式。 ASP.NET Web 服務 (ASMX) 和 WCF 之間存在差異,但請務必瞭解,WCF 支援與 ASMX 提供的相同功能 – 透過 HTTP 提供 SOAP 消息。 本文演示如何使用 Xamarin.Forms 應用程式中的 WCF SOAP 服務。

## <a name="consume-a-restful-web-service"></a>[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)

表示狀態傳輸 (REST) 是建置 Web 服務的體系結構樣式。 REST 請求透過 HTTP 使用 Web 瀏覽器用於檢索網頁和將資料發送到伺服器的相同 HTTP 謂詞。 本文展示如何使用 Xamarin.Forms 應用程式中的 RESTful Web 服務。
