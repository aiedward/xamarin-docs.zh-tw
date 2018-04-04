---
title: 專案參考
description: IOS 應用程式、 監看式應用程式，並監看式延伸模組之間的關聯性的說明。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: f3573e8b578ca567ea9d7360eb132aead4c24f37
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="project-references"></a>專案參考

_IOS 應用程式、 監看式應用程式，並監看式延伸模組之間的關聯性的說明。_

WatchOS 方案中的三個專案都*自動設定*來建置和正確配套 watchOS 3 應用程式以特定方式互相參考。 這些專案參考和套件組合識別碼設定如下所述的參考。

## <a name="project-references"></a>專案參考

每個專案的參考節點上按兩下，檢視的參考：

- **iPhone 應用程式**參考**監看式應用程式**

![](project-references-images/catalog-reference1.png "iPhone 應用程式參考監看式應用程式")

- **監看應用程式**參考**監看式應用程式擴充功能**

![](project-references-images/catalog-reference2.png "iPhone 應用程式參考監看式應用程式")


 - **監看式應用程式擴充功能**不參考任何其他專案

![](project-references-images/catalog-reference3.png "監看式應用程式擴充功能不會參考其他專案")



## <a name="bundle-identifiers"></a>套件組合識別碼

您也需要確定您**套件組合識別碼**正確無誤。
應該有三個專案*相同*識別碼前置詞，以具有預先定義的擴充功能的兩個監看式專案`watchkitextension`和`watchkitapp`、，如下所示 (如**WatchKitCatalog**範例）：

 - Xamarin.iOS 整合專案- `com.xamarin.WatchKitCatalog`

 - WatchKit 擴充功能專案- `com.xamarin.WatchKitCatalog.watchkitextension`

 - 監看式應用程式專案- `com.xamarin.WatchKitCatalog.watchkitapp`

也請確定這些**Info.plist**設定正確無誤：

 - 監看式應用程式專案的`WKCompanionAppBundleIdentifier`符合父代/容器應用程式套件組合識別碼 (ie。 在 iPhone 執行的一個)。

 - 監看式套件擴充功能專案中的**WKApp 套件組合識別碼**符合監看式應用程式專案的配套識別碼。

您可以按兩下，即可編輯識別碼**Info.plist**每個專案中的檔案。

這個螢幕擷取畫面， **Watch 擴充功能**Info.plist 檔案，顯示**監看式應用程式的**以及識別項：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
![](project-references-images/infoplist-extension.png "這個螢幕擷取畫面會監看式擴充功能的 Info.plist 檔案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
![](project-references-images/infoplist-extension-vs.png "這個螢幕擷取畫面會監看式擴充功能的 Info.plist 檔案")

-----

這個螢幕擷取畫面，**監看式應用程式的**Info.plist 檔案。
目前**監看式 OS**版本是 8.2，所以**部署目標**監看式應用程式應該是**8.2**。 請注意，如果您有已安裝 Xcode 6.3，這個值可能會設定為 dos 8.3 格式應該將它變更 8.2。

![](project-references-images/infoplist-watchapp.png "監看式 Info.plist 檔案")

監看式應用程式的部署目標可以不同於 Watch 擴充功能和 iOS 應用程式。

