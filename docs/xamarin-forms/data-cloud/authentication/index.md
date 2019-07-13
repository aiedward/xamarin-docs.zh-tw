---
title: Xamarin.Forms Web 服務驗證
description: 本指南說明如何將驗證服務整合至 Xamarin.Forms 應用程式，讓使用者可以共用的後端，只有同時存取他們自己的資料。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: a36dfba7aa07de1633ca9620674ddb4887902ba9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650417"
---
# <a name="xamarinforms-web-service-authentication"></a>Xamarin.Forms Web 服務驗證

## <a name="authenticate-a-restful-web-servicerestmd"></a>[驗證的 RESTful Web 服務](rest.md)

HTTP 支援使用數種驗證機制，來控制資源存取權。 基本驗證會提供資源的存取權的用戶端具有正確的認證。 這篇文章說明如何使用基本驗證保護 RESTful web 服務資源的存取權。

## <a name="authenticate-users-with-an-identity-provideroauthmd"></a>[驗證與身分識別提供者的使用者](oauth.md)

Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。 它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。 這篇文章說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。

## <a name="authenticate-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[使用 Azure Active Directory B2C 進行使用者驗證](azure-ad-b2c.md)

Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。 這篇文章說明如何使用 Microsoft Authentication Library (MSAL) 和 Azure Active Directory B2C 來取用者身分識別管理整合至 Xamarin.Forms 應用程式。

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinformsazure-cosmosdb-authmd"></a>[使用 Azure Cosmos DB 文件資料庫和 Xamarin.Forms 進行使用者驗證](azure-cosmosdb-auth.md)

Azure Cosmos DB 文件資料庫支援資料分割的集合，可跨越多部伺服器和資料分割，同時支援無限制的儲存體和輸送量。 這篇文章說明如何結合資料分割的集合，存取控制，讓使用者只能存取自己的文件中的 Xamarin.Forms 應用程式。
