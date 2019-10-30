---
title: 步驟 2： 設定行動應用程式的服務存取
description: 本檔說明如何提供 Xamarin 應用程式，以存取受 Azure Active Directory 保護的 Azure 應用程式。
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eeac7b0b70b2f11304a374de7522f28d4bcad6c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016674"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步驟 2： 設定行動應用程式的服務存取

每當有任何資源（例如 web 應用程式、web 服務等等）都必須受到 Azure Active Directory 保護時，就必須註冊。 所有安全的應用程式或服務都可以在 [**應用程式**] 索引標籤底下看到。您可以在這裡選取需要從行動應用程式存取的應用程式，並授與存取權。

1. 在 [**設定**] 索引標籤上，找出 [**其他應用程式的許可權**] 區段：

   ![](configure-images/2.1-configure.png "On the Configure tab, locate permissions to other applications section")

2. 按一下 [**新增應用程式**] 按鈕。 在下一個畫面快顯視窗中，您應該會看到受到 Azure Active Directory 保護的所有應用程式的清單。 選取需要從行動應用程式存取的應用程式。

   ![](configure-images/2.2-add-application.png "Select the applications that needs to be accessed from the mobile application")

3. 選取應用程式之後，再次選取 [**其他應用程式的許可權**] 區段中新增的應用程式，並授與適當的許可權。

   ![](configure-images/2.3-permissions.png "After selecting the application, once again select the newly-added application in permissions to other   applications section and give appropriate rights")

4. 最後，**儲存**設定。 這些服務現在應該可以在行動應用程式中使用！

## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-android 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
