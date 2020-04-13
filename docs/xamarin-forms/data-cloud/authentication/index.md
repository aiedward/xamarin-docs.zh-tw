---
title: Xamarin.表單網頁 服務身份驗證
description: 本指南介紹如何將身份驗證服務集成到 Xamarin.Forms 應用程式中,使用戶能夠共用後端,同時僅有權訪問自己的數據。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: c3ad061953b08ef12bea7b6a63b7c39bbd89a5f6
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388573"
---
# <a name="xamarinforms-web-service-authentication"></a>Xamarin.表單網頁 服務身份驗證

## <a name="authenticate-a-restful-web-service"></a>[驗證 RESTful Web 服務](rest.md)

HTTP 支援使用多種身份驗證機制來控制對資源的訪問。 基本身份驗證僅向具有正確憑據的用戶端提供對資源的訪問許可權。 本文介紹如何使用基本身份驗證來保護對 RESTful Web 服務資源的訪問。

## <a name="authenticate-users-with-azure-active-directory-b2c"></a>[使用 Azure Active Directory B2C 驗證使用者](azure-ad-b2c.md)

Azure Active Directory B2C 是適用於取用者導向 Web 與行動應用程式的雲端身分識別管理解決方案。 本文介紹如何使用 Microsoft 身份驗證庫 (MSAL) 和 Azure 活動目錄 B2C 將消費者識別管理整合到 Xamarin.Forms 應用程式中。

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>[使用 Azure Cosmos DB 文件資料庫和 Xamarin 驗證使用者。](azure-cosmosdb-auth.md)

Azure Cosmos DB 文件資料庫支援分區集合,該集合可以跨多個伺服器和分區,同時支援無限的存儲和輸送量。 本文介紹如何將訪問控制與分區集合相結合,以便使用者只能在 Xamarin.Forms 應用程式中訪問自己的文檔。
