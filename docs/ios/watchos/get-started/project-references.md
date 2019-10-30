---
title: Xamarin 中的 watchOS 專案參考
description: 本檔說明 iOS 應用程式、監看式應用程式和 watch 應用程式延伸模組之間的關聯性。 其中討論專案參考和套件組合識別碼。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 3dcd5f17b35b9829831adcf997d8bde97c0572e7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030161"
---
# <a name="watchos-project-references-in-xamarin"></a>Xamarin 中的 watchOS 專案參考

_說明 iOS 應用程式、監看式應用程式和監看式擴充功能之間的關聯性。_

WatchOS 解決方案中的三個專案會*自動設定*為以特定方式參考彼此，以正確建立及配套 watchOS 3 個應用程式。 以下說明這些專案參考和套件組合識別碼設定以供參考。

## <a name="project-references"></a>專案參考

按兩下每個專案的 [參考] 節點，即可查看參考：

- **iPhone 應用程式**參考**監看式應用程式**

  ![](project-references-images/catalog-reference1.png "iPhone app references Watch App")

- **監看應用程式**參考**監看應用程式延伸**模組

  ![](project-references-images/catalog-reference2.png "iPhone app references Watch App")

- **Watch 應用程式延伸**模組未參考任何其他專案

  ![](project-references-images/catalog-reference3.png "Watch App Extension does not reference the other projects")

## <a name="bundle-identifiers"></a>套件組合識別碼

您也必須確定您的套件組合**識別碼**正確。
這三個專案都應該具有*相同*的識別碼前置詞，且兩個監看式專案具有預先定義的 `watchkitextension` 和 `watchkitapp`延伸模組，如下所示（適用于**WatchKitCatalog**範例）：

- Xamarin iOS 整合專案-`com.xamarin.WatchKitCatalog`

- WatchKit 擴充功能專案-`com.xamarin.WatchKitCatalog.watchkitextension`

- 監看應用程式專案-`com.xamarin.WatchKitCatalog.watchkitapp`

此外，請確定這些**資訊 plist**設定是否正確：

- Watch 應用程式專案的 `WKCompanionAppBundleIdentifier` 符合父系/容器應用程式的套件組合識別碼（即 iPhone 上執行的套件）;

- 監看套件延伸模組專案的**WKApp 配套識別碼**會符合 Watch 應用程式專案的套件組合識別碼。

您可以按兩下每個專案中的**plist**檔案來編輯識別碼。

這個螢幕擷取畫面是**監**看式擴充功能的 plist 檔案，也會顯示**監看式應用程式的**識別碼：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](project-references-images/infoplist-extension.png "This screenshot is the Watch Extension's Info.plist file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](project-references-images/infoplist-extension-vs.png "This screenshot is the Watch Extension's Info.plist file")

-----

這個螢幕擷取畫面是**監看式應用程式的**plist 檔案。
目前的**監看式作業系統**版本為8.2，因此 Watch 應用程式的**部署目標**應該是**8.2**。 請注意，如果您已安裝 Xcode 6.3，此值可能會設定為 8.3-您應該將它變更為8.2。

![](project-references-images/infoplist-watchapp.png "The watch Info.plist file")

Watch 應用程式的部署目標可能與監看延伸模組和 iOS 應用程式不同。
