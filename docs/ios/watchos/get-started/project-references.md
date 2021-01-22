---
title: Xamarin 中的 watchOS 專案參考
description: 本檔說明 iOS 應用程式、watch 應用程式和 watch 應用程式延伸模組之間的關聯性。 它會討論專案參考和套件組合識別碼。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 9c0ece6880726993b88293f13a549c16c1e91d05
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697692"
---
# <a name="watchos-project-references-in-xamarin"></a>Xamarin 中的 watchOS 專案參考

_IOS 應用程式、監看應用程式和監看延伸模組之間關聯性的說明。_

WatchOS 方案中的三個專案會 *自動設定* 為以特定方式彼此參考，以供 watchOS 3 應用程式正確建立和配套。 以下說明這些專案參考和套件組合識別碼設定，以供參考。

## <a name="project-references"></a>專案參考

按兩下每個專案的 [參考] 節點，即可查看參考：

- **iPhone 應用程式** 參考 **監看應用程式**

  ![螢幕擷取畫面：顯示 [專案] 索引標籤。](project-references-images/catalog-reference1.png)

- **觀看應用程式** 參考 **監看應用程式延伸** 模組

  ![螢幕擷取畫面顯示已選取 MyWatchApp 點 OnWatchExtension 的 [專案] 索引標籤。](project-references-images/catalog-reference2.png)

- **Watch 應用程式延伸** 模組未參考任何其他專案

  ![Watch 應用程式延伸模組未參考其他專案](project-references-images/catalog-reference3.png)

## <a name="bundle-identifiers"></a>套件組合識別碼

您也必須確定您的套件組合 **識別碼** 正確無誤。
這三個專案都應該有 *相同* 的識別碼首碼，而兩個監看式專案具有預先定義的 `watchkitextension` 和副檔名，如下 `watchkitapp` 所示 (**WatchKitCatalog** 範例) ：

- Xamarin. iOS 統一專案- `com.xamarin.WatchKitCatalog`

- WatchKit 延伸模組專案- `com.xamarin.WatchKitCatalog.watchkitextension`

- 監看應用程式專案- `com.xamarin.WatchKitCatalog.watchkitapp`

也請確定這些 **資訊 plist** 設定正確：

- Watch 應用程式專案 `WKCompanionAppBundleIdentifier` 符合父/容器應用程式的套件組合識別碼 (例如在 iPhone) 上執行的套件;

- Watch 套件擴充功能專案的 **WKApp** 套件組合識別碼符合 Watch 應用程式專案的套件組合識別碼。

您可以在每個專案中按兩下 **plist** 檔案，以編輯識別碼。

這個螢幕擷取畫面是 **Watch 延伸模組的** plist 檔案，它也會顯示 **watch 應用程式的** 識別碼：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![這個螢幕擷取畫面是 Watch 延伸模組的資訊 plist 檔案](project-references-images/infoplist-extension.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![這個螢幕擷取畫面是 Watch 延伸模組的資訊 plist 檔案](project-references-images/infoplist-extension-vs.png)

-----

這個螢幕擷取畫面是 **Watch 應用程式的** plist 檔案。
目前的 **監看作業系統** 版本是8.2，因此 Watch 應用程式的 **部署目標** 應該是 **8.2**。 請注意，如果您已安裝 Xcode 6.3，此值可能會設定為8.3，您應該將它變更為8.2。

![Watch Info. plist 檔案](project-references-images/infoplist-watchapp.png)

Watch 應用程式的部署目標可能與 Watch 擴充功能和 iOS 應用程式不同。
