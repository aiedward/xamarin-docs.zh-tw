---
title: "驗證 Web 服務的存取權"
description: "本指南說明如何整合到 Xamarin.Forms 應用程式，讓使用者共用在只具有自己的資料存取時的後端驗證服務。 涵蓋的主題包括使用基本驗證搭配 REST 服務，使用 Xamarin.Auth 元件驗證 OAuth 身分識別提供者，以及使用內建的驗證機制所提供不同的提供者。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 0139a7a921861b5d1c9a3639ee2c7e25ee6cf5fe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="authenticating-access-to-web-services"></a>驗證 Web 服務的存取權

_本指南說明如何整合到 Xamarin.Forms 應用程式，讓使用者共用在只具有自己的資料存取時的後端驗證服務。涵蓋的主題包括使用基本驗證搭配 REST 服務，使用 Xamarin.Auth 元件驗證 OAuth 身分識別提供者，以及使用內建的驗證機制所提供不同的提供者。_

## <a name="authenticating-a-restful-web-servicerestmd"></a>[驗證 RESTful Web 服務](rest.md)

HTTP 支援使用數種驗證機制，來控制資源的存取權。 基本驗證有正確的認證這些用戶端提供資源的存取權。 這篇文章會示範如何使用基本驗證來保護 RESTful web 服務資源的存取權。

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[驗證使用者身分識別提供者](oauth.md)

Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。 它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。 本文說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[驗證使用者與 Azure 行動應用程式](azure.md)

Azure 行動應用程式會使用各種不同的外部識別提供者來支援應用程式使用者驗證和授權。 然後可以限制存取已驗證的使用者資料表上設定權限。 本文說明如何使用 Azure 行動應用程式來管理驗證程序，在 Xamarin.Forms 應用程式。

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[驗證使用者與 Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active Directory B2C 是消費者導向 web 與行動應用程式的雲端識別身分管理解決方案。 本文示範如何使用 Microsoft 驗證程式庫 (MSAL) 和 Azure Active Directory B2C 取用者身分識別管理整合 Xamarin.Forms 應用程式。

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[整合 Azure Active Directory B2C 與 Azure Mobile Apps](azure-ad-b2c-mobile-app.md)

Azure Active Directory B2C 可以用來管理 Azure 行動應用程式的驗證工作流程。 使用此方法時，身分識別管理體驗完全在雲端中，定義，並可以修改而不需要變更您的行動應用程式程式碼。 本文示範如何使用 Azure Active Directory B2C xamarin.forms 提供驗證和授權 Azure 行動應用程式執行個體。

## <a name="authenticating-users-with-an-amazon-simpledb-serviceawsmd"></a>[驗證使用者與 Amazon SimpleDB 服務](aws.md)

Amazon SimpleDB 並未提供它自己的資源為基礎的權限系統。 相反地，身分識別提供者的驗證可以用來確保使用者只能 SimpleDB 網域中擁有自己的資料存取。 本文說明如何限制使用者存取他們自己的 SimpleDB 資料。


## <a name="related-links"></a>相關連結

- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
- [非同步支援概觀](~/cross-platform/platform/async.md)
