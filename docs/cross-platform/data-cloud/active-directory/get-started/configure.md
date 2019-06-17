---
title: 步驟 2： 設定行動應用程式的服務存取
description: 本文件說明如何提供 Xamarin 應用程式存取由 Azure Active Directory 保護的 Azure 應用程式。
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2a9baab9215ae2d30e4daf6800a116c95165da42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61188230"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步驟 2： 設定行動應用程式的服務存取

每當任何資源例如 web 應用程式、 web 服務等需要保護由 Azure Active Directory 時，它必須註冊。 所有安全的應用程式或服務可以看到底下**應用程式** 索引標籤。這裡您可以選取要從行動應用程式存取，並讓它存取的應用程式。

1. 在 **設定**索引標籤上，找出**其他應用程式的權限**區段：

  ![](configure-images/2.1-configure.png "在 [設定] 索引標籤中，找到 [其他應用程式] 區段的權限")

2.  按一下 [**將應用程式加入**] 按鈕。 在快顯的下一個畫面中，您應該看到的所有受保護的 Azure Active Directory 應用程式的清單。 選取需要從行動應用程式存取的應用程式。

  ![](configure-images/2.2-add-application.png "選取需要從行動應用程式存取的應用程式")

3. 之後選取應用程式，再次選取 新增應用程式中的**其他應用程式的權限**區段，並提供適當的權限。

  ![](configure-images/2.3-permissions.png "之後選取應用程式，再次選取 [其他應用程式] 區段的權限中的 新增應用程式，並提供適當的權限")

4. 最後，**儲存**組態。 這些服務現在應該可在行動裝置應用程式 ！



## <a name="related-links"></a>相關連結

- [Microsoft Nativeclient-windowsstore 範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
