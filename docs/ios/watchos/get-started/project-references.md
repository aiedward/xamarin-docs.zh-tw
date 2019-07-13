---
title: watchOS 在 Xamarin 中的專案參考
description: 本文件說明 iOS 應用程式、 監控應用程式和監看式應用程式擴充功能之間的關聯性。 它討論的專案參考 」 和 「 套件組合識別碼。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 60eaad98e2d5469e9c43e6b4ad889080e1aa63ba
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832055"
---
# <a name="watchos-project-references-in-xamarin"></a>watchOS 在 Xamarin 中的專案參考

_IOS 應用程式、 監看式應用程式和監看式延伸模組之間的關聯性的說明。_

WatchOS 方案中的三個專案都*自動設定*彼此參考 watchOS 3 應用程式，建置和正確配套以特定方式。 如需參考，會將這些專案參考 」 和 「 套件組合識別碼設定說明如下。

## <a name="project-references"></a>專案參考

針對每個專案的參考節點上按兩下來檢視參考：

- **iPhone 應用程式**參考**Watch 應用程式**

  ![](project-references-images/catalog-reference1.png "iPhone 應用程式參考 Watch 應用程式")

- **監看應用程式**參考**監看式應用程式擴充功能**

  ![](project-references-images/catalog-reference2.png "iPhone 應用程式參考 Watch 應用程式")


- **監看式應用程式擴充功能**不參考任何其他專案

  ![](project-references-images/catalog-reference3.png "監看式應用程式擴充功能不會參考其他專案")



## <a name="bundle-identifiers"></a>套件組合識別碼

您也必須先確定您**套件組合識別碼**正確無誤。
這三個專案應該有*相同*識別碼的前置詞，與具有預先定義的延伸模組的兩個監看式專案`watchkitextension`和`watchkitapp`，如下 (如**WatchKitCatalog**範例）：

- Xamarin.iOS 統一專案- `com.xamarin.WatchKitCatalog`

- WatchKit 擴充功能專案- `com.xamarin.WatchKitCatalog.watchkitextension`

- 監看式應用程式專案- `com.xamarin.WatchKitCatalog.watchkitapp`

此外，請確定這些**Info.plist**設定是否正確：

- Watch 應用程式專案的`WKCompanionAppBundleIdentifier`與父代/容器應用程式的套件組合識別碼 」 相符 (ie。 在 iPhone 執行的);

- 監看式套件延伸模組專案的**WKApp 套件組合識別碼**符合 Watch 應用程式專案的套件組合識別碼。

您可以編輯的識別項，方法是按兩下**Info.plist**每個專案中的檔案。

此螢幕擷取畫面**監看式延伸模組**Info.plist 檔案中，顯示**監看式應用程式的**識別碼以及：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "此螢幕擷取畫面是監看式延伸模組的 Info.plist 檔案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "此螢幕擷取畫面是監看式延伸模組的 Info.plist 檔案")

-----

此螢幕擷取畫面**監看式應用程式的**Info.plist 檔案。
目前**監看式 OS**版本是 8.2，因此**部署目標**Watch 應用程式應**8.2**。 請注意，如果您有安裝的 Xcode 6.3，這個值可能會設定為 8.3 應變更 8.2。

![](project-references-images/infoplist-watchapp.png "監看式 Info.plist 檔案")

Watch 應用程式的部署目標可以是不同於 iOS 應用程式與監控擴充功能。

