---
title: Azure Active Directory
description: 本文件說明以允許使用 Azure Active Directory 進行驗證的行動裝置應用程式必須遵循的步驟。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318294"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_註冊以使用 Azure Active Directory 應用程式_

Azure Active Directory 可讓開發人員安全的資源，例如檔案、 連結及使用相同的組織帳戶登入他們的系統，或檢查其電子郵件的員工使用的 Web Api。

開發行動應用程式可以使用 Azure Active Directory 進行驗證，牽涉到三個步驟。
前兩個步驟大致相同，不論您打算使用哪些服務。 第三個步驟是不同的每個服務類型：

  1. [使用 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*入口網站，然後
  2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 開發行動應用程式服務。

您可以存取的不同服務的範例包括：

- [Graph API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365


## <a name="conclusion"></a>結論

使用您上面的步驟就可以驗證您對 Azure Active Directory 的行動應用程式。 Active Directory Authentication Library (ADAL) 可以更容易以短短幾行程式碼，同時保留大多數的程式碼相同，因而讓它共用跨平台。



## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-windowsstore 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
