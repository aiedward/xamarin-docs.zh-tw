---
title: 步驟 1： 註冊要使用 Azure Active Directory 的應用程式
description: 本檔說明如何使用 Azure Active Directory 註冊 Azure 應用程式，讓行動用戶端可以安全地存取它。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: c3b93ec4fa4ec2ffad9db26414c2453ba7281974
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016657"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步驟 1： 註冊要使用 Azure Active Directory 的應用程式

1. 流覽至[windowsazure.com](https://manage.windowsazure.com) ，並在 Azure 入口網站中使用您的 Microsoft 帳戶或組織帳戶登入。 如果您沒有 Azure 訂用帳戶，可以從[azure.com](https://www.azure.com)取得試用版

2. 登入之後，移至 [ **Active Directory** （1）] 區段，然後選擇您要註冊應用程式的目錄（2）

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "section and choose the directory where you want to register the application")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 按一下 **[新增] 以建立**新的應用程式，然後選取 [**新增我的組織正在開發的應用程式**]

   [![](register-images/02.-add-new-application-sml.jpg "Add an application my organization is developing")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一個畫面上，提供您的應用程式名稱（例如， XAM-示範）。
   請確定您選取 [ **Native Client 應用程式**] 做為應用程式類型。

   ![](register-images/03.-app-name.jpg "Make sure you select Native Client Application as the type of application")

5. 在最後一個畫面上，提供應用程式唯一的 * 重新*導向 URI* ，因為驗證完成時將會傳回此 uri。

   ![](register-images/04.-app-redirect.jpg "On the final screen, provide a Redirect URI that is unique to your application as it will return to this URI when   authentication is complete")

6. 建立應用程式之後，流覽至 [**設定**] 索引標籤。記下**用戶端識別碼**，我們將在稍後於應用程式中使用。 此外，在此畫面上，您可以將行動應用程式的存取權授與 Active Directory 或新增另一個應用程式（例如 Web API 或 Office365），以供行動應用程式在驗證完成後使用。

   ![](register-images/05.-configure.jpg "Also, on this screen you can give your mobile application access to Active Directory or add another application like Web API or Office365")

## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-android 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
