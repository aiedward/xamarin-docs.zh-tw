---
title: 步驟 1： 註冊以使用 Azure Active Directory 應用程式
description: 本文件說明如何註冊 Azure 的應用程式與 Azure Active Directory，使它可以安全地存取行動用戶端。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3e852e3e7ab3ac5db28052a47af1ebec4dbd2416
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864664"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步驟 1： 註冊以使用 Azure Active Directory 應用程式

1. 瀏覽至[windowsazure.com](https://manage.windowsazure.com)並登入您的 Microsoft 帳戶或組織帳戶，在 Azure 入口網站中。 如果您沒有 Azure 訂用帳戶，您可以取得從試用[azure.com](https://www.azure.com)

2. 登入之後，請移至**Active Directory** （1） 區段，然後選擇您要註冊應用程式 (2) 的目錄

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "區段，然後選擇您要註冊應用程式的目錄")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 按一下 **新增**若要建立新的應用程式，然後選取**加入我的組織正在開發的應用程式**

   [![](register-images/02.-add-new-application-sml.jpg "加入我的組織正在開發的應用程式")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一個畫面上，為您的應用程式命名 （例如。 XAM-DEMO)。
   請確定您選取**原生用戶端應用程式**做為應用程式的類型。

   ![](register-images/03.-app-name.jpg "請確定您選取作為應用程式類型的原生用戶端應用程式")

5. 在最後一個畫面上，提供 **重新導向 URI* ，為您的應用程式的唯一驗證完成時，它會傳回對此 URI。

   ![](register-images/04.-app-redirect.jpg "在最後一個畫面上，提供 重新導向 URI，當驗證完成時，它會傳回對此 URI 是唯一您的應用程式")

6. 應用程式建立之後，瀏覽至**設定** 索引標籤。請記下**用戶端識別碼**，我們會用在我們的應用程式更新版本。 此外，在此畫面上 Active Directory 中為您的行動應用程式存取權或新增另一個應用程式，例如 Web API 或 Office365，完成驗證之後可供行動應用程式。

     ![](register-images/05.-configure.jpg "此外，在此畫面上您可以讓您的行動應用程式存取 Active Directory 或新增另一個應用程式，例如 Web API 或 Office365")



## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-windowsstore 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
