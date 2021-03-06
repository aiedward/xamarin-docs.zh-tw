---
title: 步驟 1： 註冊要使用 Azure Active Directory 的應用程式
description: 本檔說明如何使用 Azure Active Directory 註冊 Azure 應用程式，讓行動用戶端可以安全地存取它。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 6b10b2fd489881292d95bbf79375a6488aa88a17
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932537"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步驟 1： 註冊要使用 Azure Active Directory 的應用程式

1. 流覽至[windowsazure.com](https://manage.windowsazure.com) ，並在 Azure 入口網站中使用您的 Microsoft 帳戶或組織帳戶登入。 如果您沒有 Azure 訂用帳戶，可以從[azure.com](https://www.azure.com)取得試用版

2. 登入之後，移至 [ **Active Directory** （1）] 區段，然後選擇您要註冊應用程式的目錄（2）

   [![區段，然後選擇您要註冊應用程式的目錄](register-images/01.-active-directory-in-azure-portal-sml.jpg)](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 按一下 **[新增] 以建立**新的應用程式，然後選取 [**新增我的組織正在開發的應用程式**]

   [![新增組織開發中的應用程式](register-images/02.-add-new-application-sml.jpg)](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一個畫面上，提供您的應用程式名稱（例如， XAM-示範）。
   請確定您選取 [ **Native Client 應用程式**] 做為應用程式類型。

   ![請確定選取 [Native Client 應用程式] 做為應用程式類型](register-images/03.-app-name.jpg)

5. 在最後一個畫面上，提供應用程式唯一的 * 重新*導向 URI* ，因為驗證完成時將會傳回此 uri。

   ![在最後一個畫面上，提供應用程式唯一的重新導向 URI，因為驗證完成時將會返回此 URI](register-images/04.-app-redirect.jpg)

6. 建立應用程式之後，流覽至 [**設定**] 索引標籤。記下**用戶端識別碼**，我們將在稍後於應用程式中使用。 此外，在此畫面上，您可以將行動應用程式的存取權授與 Active Directory 或新增另一個應用程式（例如 Web API 或 Office365），以供行動應用程式在驗證完成後使用。

   ![此外，在此畫面上，您可以將行動應用程式的存取權授與 Active Directory 或新增另一個應用程式（例如 Web API 或 Office365）](register-images/05.-configure.jpg)

## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-android 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
