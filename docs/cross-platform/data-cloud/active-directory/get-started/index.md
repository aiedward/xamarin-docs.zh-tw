---
title: Azure Active Directory
description: 本檔說明允許行動應用程式使用 Azure Active Directory 進行驗證所必須遵循的步驟。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 18604ffa4980d47149d8feed257460e782f30bee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016639"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_註冊要使用 Azure Active Directory 的應用程式_

Azure Active Directory 可讓開發人員使用與員工用來登入系統或檢查其電子郵件的相同組織帳戶，來保護資源（例如檔案、連結和 Web Api）。

開發可使用 Azure Active Directory 進行驗證的行動應用程式包含三個步驟。
前兩個步驟通常都相同，不論您打算使用哪些服務。 每個服務類型的第三個步驟不同：

  1. 在*windowsazure.com*入口網站上[向 Azure Active Directory 註冊](~/cross-platform/data-cloud/active-directory/get-started/register.md)，然後
  2. [設定服務](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 使用服務開發行動應用程式。

您可以存取的不同服務範例包括：

- [圖形 API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365

## <a name="conclusion"></a>結論

使用上述步驟，您可以根據 Azure Active Directory 來驗證您的行動應用程式。 Active Directory 驗證程式庫（ADAL）可讓程式碼更少，同時讓大部分的程式碼保持相同，使其可以跨平臺共用。

## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-android 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
