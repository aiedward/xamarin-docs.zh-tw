---
title: 步驟 1： 註冊要使用 Azure Active Directory 的應用程式
description: 本檔說明如何使用 Azure Active Directory 註冊 Azure 應用程式, 讓行動用戶端可以安全地存取它。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 732c1ee241b39a4bb1422b8c27820631bf8c6b0c
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199045"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步驟 1： 註冊要使用 Azure Active Directory 的應用程式

1. 流覽至[windowsazure.com](https://manage.windowsazure.com) , 並在 Azure 入口網站中使用您的 Microsoft 帳戶或組織帳戶登入。 如果您沒有 Azure 訂用帳戶, 可以從[azure.com](https://www.azure.com)取得試用版

2. 登入之後, 移至 [ **Active Directory** (1)] 區段, 然後選擇您要註冊應用程式的目錄 (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "區段, 然後選擇您要註冊應用程式的目錄")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 按一下 [新增] 以建立新的應用程式, 然後選取 [**新增我的組織正在開發的應用程式**]

   [![](register-images/02.-add-new-application-sml.jpg "新增我的組織正在開發的應用程式")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一個畫面上, 提供您的應用程式名稱 (例如, XAM-示範)。
   請確定您選取 [ **Native Client 應用程式**] 做為應用程式類型。

   ![](register-images/03.-app-name.jpg "請確定選取 [Native Client 應用程式] 做為應用程式類型")

5. 在最後一個畫面上, 提供應用程式唯一的 * 重新*導向 URI* , 因為驗證完成時將會傳回此 uri。

   ![](register-images/04.-app-redirect.jpg "在最後一個畫面上, 提供應用程式唯一的重新導向 URI, 因為驗證完成時將會返回此 URI")

6. 建立應用程式之後, 流覽至 [**設定**] 索引標籤。記下**用戶端識別碼**, 我們將在稍後於應用程式中使用。 此外, 在此畫面上, 您可以將行動應用程式的存取權授與 Active Directory 或新增另一個應用程式 (例如 Web API 或 Office365), 以供行動應用程式在驗證完成後使用。

   ![](register-images/05.-configure.jpg "此外, 在此畫面上, 您可以將行動應用程式的存取權授與 Active Directory 或新增另一個應用程式 (例如 Web API 或 Office365)")



## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-android 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
