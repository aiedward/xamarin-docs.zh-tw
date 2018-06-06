---
title: Azure Active Directory
description: 本文件說明允許行動裝置的應用程式向 Azure Active Directory 必須遵照的步驟。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780059"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_註冊以使用 Azure Active Directory 應用程式_

Azure Active Directory 可讓開發人員安全的資源，例如檔案、 連結和 Web 應用程式開發介面使用相同的組織帳戶的員工用來登入他們的系統，或檢查其電子郵件。

開發行動應用程式可以使用 Azure Active Directory 進行驗證，牽涉到三個步驟。
前兩個步驟通常是相同，不論您打算使用何種服務。 第三個步驟是每種服務類型不同：

  1. [向 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*入口網站，然後
  2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 開發行動應用程式使用的服務。

您可以存取的不同服務的範例包括：

- [Graph API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365


## <a name="conclusion"></a>結論

使用您的上述步驟，可以驗證您對 Azure Active Directory 的行動裝置應用程式。 Active Directory Authentication Library (ADAL) 可以更容易使用較少的行程式碼，同時保留大部分的程式碼相同，因此讓它成為可共用跨平台。



## <a name="related-links"></a>相關連結

- [Microsoft 要範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
