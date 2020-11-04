---
title: 使用 Xcode 設計 Ui
description: 現在您可以在 Mac 上使用 Xcode，直接建立 iOS 使用者介面的建議方式。
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b6fc5c89e9221bc84b364290a275fbd6d35c12c4
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343271"
---
# <a name="using-xcode-to-design-user-interfaces"></a>使用 Xcode 設計使用者介面

_編輯. 腳本和筆尖檔案的建議方式是在 Mac 上的 Xcode Interface Builder 中編輯這些檔案。_

## <a name="how-to-open-a-storyboard"></a>如何開啟分鏡腳本 

在 Visual Studio for Mac 中開啟 iOS 使用者介面檔案，方法是以滑鼠右鍵按一下分鏡腳本檔案，然後選取 [ **Xcode Interface Builder** ：

[![選取 Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

接著，您應該會看到 Xcode 視窗啟動。 此處儲存的任何編輯都會反映在 Visual Studio 專案中。

[![Xcode 視窗](images/xcode.png)](images/xcode.png#lightbox)

您可以在 [這裡](https://developer.apple.com/xcode/interface-builder/)找到 Xcode Interface Builder 的詳細資訊。

## <a name="known-problems"></a>已知的問題

本節將討論已知問題。

### <a name="visual-studio-could-not-communicate-with-xcode"></a>「Visual Studio 無法與 Xcode 通訊」

在 macOS Catalina 或更高版本中，您可能會遇到下列錯誤：

[![無法傳達錯誤](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

首先，請確定在您 Mac 的 [ **安全性] & [> 隱私權** ] 下的 [系統偏好設定] 下，會列出 Visual Studio 列出並已核取 [ **Xcode** ]

[![macOS 安全性](images/macos-security.png)](images/macos-security.png#lightbox)

如果已核取 [ **Xcode** ] 且錯誤訊息仍然出現，您可能需要重設 Visual Studio for Mac 的隱私權許可權。

這可以藉由啟動終端機視窗併發出命令來完成：

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

為確保上述變更生效，請重設您的 Mac 的系所。 您可以在 [這裡](https://support.apple.com/HT204063)找到如何進行的指示。
