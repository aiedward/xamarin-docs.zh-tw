---
title: 適用於 Mac 連接 Visual Studio 中的服務
description: 新增 Azure 的資料存放區、 驗證和推播通知從行動裝置應用程式在 Visual Studio for Mac
ms.prod: xamarin
ms.assetid: ADDFB3A5-DB6A-417C-8ACE-33D107FB75C2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b9aaa197ccf01c59d6e4bbb0476d10295a108f89
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="connected-services-walkthrough"></a>已連接的服務的逐步解說

已連接服務工作流程會在 Azure 入口網站工作流程帶入 Visual Studio for Mac，讓您不必離開您的專案加入服務。

本逐步解說示範如何加入 Azure 後端服務，它將雲端資料儲存、 驗證和推播通知給跨平台 Xamarin.Forms 可攜式類別程式庫 (PCL) 應用程式。


1.  按兩下，即可啟動**已連接服務**節點在解決方案中，將會出現**服務組件庫**。
  這是一份所有可用的服務，此應用程式類型。 選取服務 (例如**使用 Azure App Service 行動後的端**) 在其上按一下。

  [![](connected-services-images/image001-sml.png "適用於 Mac 連接 Visual Studio 中的 [服務] 節點")](connected-services-images/image001.png#lightbox)

2. 服務詳細資料頁面有服務和安裝的相依性的描述。
  按一下**新增**按鈕以加入應用程式的相依性：

  [![](connected-services-images/image002-sml.png "使用 Azure 行動後端")](connected-services-images/image002.png#lightbox)

3. 相依性必須將加入 PCL 和平台專屬專案工作。
  選取核取方塊，將服務加入至每個會參考它 （直接或間接） 的專案：

  [![](connected-services-images/image003-sml.png "檢查應該參考服務的所有專案")](connected-services-images/image003.png#lightbox)

4. 選擇**接受**上**授權接受**對話的 NuGet 封裝。
  有時可能會接受，一個用於 MobileClient 和相依性，和另一個用於 SQLiteStore，所需之離線的資料同步的兩個對話方塊：

  [![](connected-services-images/image004-sml.png "接受授權合約")](connected-services-images/image004.png#lightbox)

  ![](connected-services-images/image005.png "授權接受視窗")

5. 一旦已加入相依性，系統會要求您想要用來與 Azure 通訊的帳戶登入。
  如果您已登入 Microsoft ID，Visual Studio for Mac 將會嘗試擷取您的 Azure 訂用帳戶和任何與其相關聯的應用程式服務。 如果您沒有任何訂用帳戶，您可以加入一個註冊免費的試用或購買訂用帳戶中的計劃 Azure 入口網站。

6. 從清單中選取的應用程式服務。 這將會填滿的範本程式碼`MobileServiceClient`物件相對應的 Azure 上的應用程式服務 url:

  [![](connected-services-images/image006-sml.png "從清單選取應用程式服務")](connected-services-images/image006.png#lightbox)

  如果不沒有列出任何服務，按一下**新增**按鈕 （請參閱步驟 9）。

7. 將範本程式碼複製`MobileServiceClient`PCL 到。 中的檔案位置的應用程式中不重要，只要只有一個執行個體。
  建議的方法是建立`AzureService`類別來處理 Azure 的所有互動，以及使用`MobileServiceClient`:

  ![](connected-services-images/image007.png "組態程式碼複製到應用程式")

8. 請依照下列中的文件**接下來的步驟**加入資料、 離線同步處理、 驗證和推播通知給您的應用程式：

  [![](connected-services-images/image008-sml.png "檢閱下一個步驟的指示")](connected-services-images/image008.png#lightbox)

10. 如果您沒有任何現有的應用程式服務，您可以建立新服務從 Visual studio for mac。
  按一下**新增**左下角開啟 [服務] 清單中的按鈕**新應用程式服務**對話方塊：

  [![](connected-services-images/image009-sml.png "Visual Studio 中建立新的應用程式服務，適用於 Mac")](connected-services-images/image009.png#lightbox)

新的服務需要下列參數：

-   **應用程式服務名稱**– 唯一名稱/識別碼計劃
-   **訂用帳戶**– 您想要用來支付服務的訂用帳戶
-   **資源群組**– 方式或組織專案的所有 Azure 資源。 若要使用現有或建立一個新的選項。 如果這是您第一項 Azure 服務，請建立一個新。
-   **服務方案**– 決定的位置和使用它的任何資源的成本。 若要使用現有或建立一個新的選項。 如果這是您第一項 Azure 服務，使用預設值，或建立新的免費層中 (F1)。

請瀏覽[Azure 應用程式服務文件](https://docs.microsoft.com/azure/app-service/)如需詳細資訊


## <a name="related-links"></a>相關連結

- [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/)
- [版本資訊](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Connected_Services)
