---
title: "步驟 2： 服務存取的行動應用程式設定"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a679d404eace121b38463e35350b8b85ec08b1e0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步驟 2： 服務存取的行動應用程式設定

每當任何資源例如 web 應用程式、 web 服務等，必須保護 Azure Active directory，它必須註冊。 所有安全的應用程式或服務可以看到下**應用程式** 索引標籤。您可以在這裡選取的應用程式必須從行動裝置應用程式存取，並提供存取的。

1. 在**設定**索引標籤上，找出**其他應用程式的權限**> 一節：

  ![](configure-images/2.1-configure.png "在 [設定] 索引標籤中，找出其他應用程式 > 一節的權限")

2.  按一下**新增應用程式** 按鈕。 快顯的下一個畫面中，您應該看到的所有受保護的 Azure Active Directory 應用程式的清單。 選取需要從行動應用程式存取的應用程式。

  ![](configure-images/2.2-add-application.png "選取需要從行動應用程式存取的應用程式")

3. 之後選取的應用程式，再次選取 新增應用程式中的**其他應用程式的權限**區段，並提供適當的權限。

  ![](configure-images/2.3-permissions.png "選取應用程式之後, 再次選取新加入的應用程式中其他應用程式 > 一節的權限，並提供適當的權限")

4. 最後，**儲存**組態。 這些服務現在應可在行動裝置應用程式 ！



## <a name="related-links"></a>相關連結

- [Microsoft 要範例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
