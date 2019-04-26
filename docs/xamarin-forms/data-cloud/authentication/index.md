---
title: 驗證 Web 服務的存取權
description: 本指南說明如何將驗證服務整合至 Xamarin.Forms 應用程式，讓使用者可以共用的後端，只有同時存取他們自己的資料。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331178"
---
# <a name="authenticating-access-to-web-services"></a>驗證 Web 服務的存取權

_本指南說明如何將驗證服務整合至 Xamarin.Forms 應用程式，讓使用者可以共用的後端，只有同時存取他們自己的資料。涵蓋的主題包括使用基本驗證搭配 REST 服務，使用 Xamarin.Auth 元件驗證 OAuth 識別提供者，並使用內建的驗證機制不同的提供者所提供。_

## <a name="authenticating-a-restful-web-servicerestmd"></a>[驗證的 RESTful Web 服務](rest.md)

HTTP 支援使用數種驗證機制，來控制資源存取權。 基本驗證會提供資源的存取權的用戶端具有正確的認證。 這篇文章會示範如何使用基本驗證保護 RESTful web 服務資源的存取權。

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[使用身分識別提供者驗證使用者](oauth.md)

Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。 它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。 這篇文章說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[使用 Azure 行動應用程式驗證使用者](azure.md)

Azure Mobile Apps 會使用各種外部識別提供者來支援驗證與授權應用程式使用者。 然後可以限制只有經過驗證的使用者存取資料表上設定權限。 這篇文章說明如何使用 Azure Mobile Apps 管理驗證程序，在 Xamarin.Forms 應用程式。

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[使用 Azure Active Directory B2C 驗證使用者](azure-ad-b2c.md)

Azure Active Directory B2C 是取用者導向 web 與行動應用程式的雲端身分識別管理解決方案。 這篇文章會示範如何使用 Microsoft Authentication Library (MSAL) 和 Azure Active Directory B2C 來取用者身分識別管理整合至 Xamarin.Forms 應用程式。

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[整合 Azure Active Directory B2C 與 Azure Mobile Apps](azure-ad-b2c-mobile-app.md)

Azure Active Directory B2C 可用來管理適用於 Azure Mobile Apps 的驗證工作流程。 使用此方法時，身分識別管理體驗在雲端中完整定義，並可以進行修改，而不需要變更您的行動應用程式程式碼。 這篇文章會示範如何使用 Azure Active Directory B2C 來提供驗證和授權的 Azure Mobile Apps 執行個體使用 Xamarin.Forms。

## <a name="related-links"></a>相關連結

- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
- [非同步支援概觀](~/cross-platform/platform/async.md)
