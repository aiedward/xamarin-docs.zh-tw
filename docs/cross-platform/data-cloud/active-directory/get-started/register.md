---
title: "步驟 1： 註冊以使用 Azure Active Directory 應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 776a60701e01a81856b0a85e7136c57b97cff101
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步驟 1： 註冊以使用 Azure Active Directory 應用程式

1. 瀏覽至[windowsazure.com](https://manage.windowsazure.com)並登入您的 Microsoft 帳戶或組織帳戶，在 Azure 入口網站。 如果您沒有 Azure 訂用帳戶，您可以取得從試用[azure.com](http://www.azure.com)

2. 登入之後，請移至**Active Directory** （1） 區段，然後選擇您要註冊的應用程式 (2) 的目錄

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "區段，然後選擇您要註冊的應用程式的目錄")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 按一下**新增**若要建立新的應用程式，然後選取**加入我組織正在開發的應用程式**

  [ ![](register-images/02.-add-new-application-sml.jpg "加入我組織正在開發的應用程式")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一個畫面上，讓應用程式的名稱 （例如。 XAM-DEMO)。
  請確定您選取**原生用戶端應用程式**做為應用程式的類型。

  ![](register-images/03.-app-name.jpg "請確定您選取原生用戶端應用程式的應用程式類型")

5. 在最後一個畫面上，提供 **重新導向 URI* ，為您的應用程式的唯一驗證完成時，它會傳回對此 URI。

  ![](register-images/04.-app-redirect.jpg "在最後一個畫面上，提供為您的應用程式的唯一驗證完成時，它會傳回對此 URI 重新導向 URI")

6. 應用程式建立之後，瀏覽至**設定** 索引標籤。請記下**用戶端識別碼**將用於在我們的應用程式更新版本。 此外，在這個畫面上將行動應用程式存取授與 Active directory 或加入另一個應用程式，例如 Web API 或 Office365，完成驗證之後可供行動應用程式。

    ![](register-images/05.-configure.jpg "此外，在這個畫面上您可以將行動應用程式存取授與 Active directory 或加入另一個應用程式，例如 Web API 或 Office365")



## <a name="related-links"></a>相關連結

- [Microsoft 要範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
