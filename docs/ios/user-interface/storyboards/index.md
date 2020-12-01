---
title: 使用 Xcode 設計使用者介面
description: 瞭解如何在 Mac 上使用 Xcode 直接建立 iOS 使用者介面的建議方式。
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b25245250bd9ea17034ea8f6b11388a12fc13241
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439545"
---
# <a name="designing-user-interfaces-with-xcode"></a>使用 Xcode 設計使用者介面

從 Visual Studio 2019 16.8 版和 Visual Studio for Mac 8.8 版開始，建議編輯. 腳本和筆尖檔案的方式，就是在 Mac 上的 Xcode Interface Builder 中進行編輯。

> [!NOTE]
> 從 Visual Studio 2019 16.9 版開始，將不支援在 Windows 上編輯 iOS 分鏡腳本的方式。 使用 Visual Studio for Mac 和 Xcode Interface Builder 繼續建立您的 Xamarin iOS 使用者介面。

本文涵蓋使用 Xcode Interface Builder 建立使用者介面的常見解決方案。  如果您先前已使用 [Xamarin] 設計工具來編輯您的 Ui，本文可能特別有用。 

如需更深入的分鏡腳本逐步解說，請參閱 [Xamarin 中](./indepth-storyboard.md)的分鏡腳本。

## <a name="how-to-open-a-storyboard"></a>如何開啟分鏡腳本 

在 Visual Studio for Mac 中開啟 iOS 使用者介面檔案，方法是以滑鼠右鍵按一下分鏡腳本檔案，然後選取 [ **Xcode Interface Builder**：

[![選取 Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

然後，您應該會看到 Xcode 視窗開啟。 此處儲存的任何編輯都會反映在 Visual Studio 專案中。

[![Xcode 視窗](images/xcode.png)](images/xcode.png#lightbox)

如需 Xcode Interface Builder 的詳細資訊，請參閱 [Interface Builder 內建](https://developer.apple.com/xcode/interface-builder/)。

## <a name="creating-a-new-control"></a>建立新控制項

若要使用 Xcode Interface Builder 建立新的控制項，請先選取您想要編輯的腳本。 然後，開啟 [Xcode 程式庫] 對話方塊 (**View**  >  **Show Library**) ，然後將控制項拖曳到您的腳本。

[![程式庫選擇器](images/library-picker.png)](images/library-picker.png#lightbox)

接下來，開啟對應的視圖控制器標頭檔。  若為空白的「單一視圖」 Xamarin iOS 應用程式，預設的分鏡腳本稱為「主要」（ **storyboard**）。 在 ViewController.cs Visual Studio 中，對應的視圖控制器檔案稱為「 **ViewController.cs** 」，其對應的 **ViewController .h** 標頭檔是從 Xcode 查看時。

從 [Xcode Interface Builder 中，開啟腳本和對應的視圖控制器標頭檔。  按住 **control** 鍵 (**^**) ，將控制項從分鏡腳本拖曳至 view controller 檔案，直到 Xcode 提示您提供對話方塊。

[![示範連結控制項](images/demo-link-control.gif)](images/demo-link-control.gif#lightbox)

如上所述，對應的 c # 程式碼會在 view controller 的程式碼後端檔案中自動產生。  您現在可以在您的 Xamarin. iOS 專案記憶體取此控制項。

## <a name="editing-an-existing-controls-name"></a>編輯現有的控制項名稱

若要從 Xcode Interface Builder 編輯現有的控制項名稱，並將該變更反映回您的 c # 專案，請流覽至適當的視圖控制器標頭檔，並以滑鼠右鍵按一下，然後選取 [ **重構**]。   

[![重構控制項](images/refactor-control.png)](images/refactor-control.png#lightbox)

您的程式碼後端檔案會以新名稱重新產生，讓您可以透過 Visual Studio for Mac 中的程式碼存取控制項。

## <a name="known-problems"></a>已知的問題

本節涵蓋已知問題。

### <a name="visual-studio-could-not-communicate-with-xcode"></a>「Visual Studio 無法與 Xcode 通訊」

在 macOS Catalina 或更高版本中，您可能會遇到下列錯誤：

[![無法傳達錯誤](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

首先，在 Mac 的 [ **安全性 & 隱私權 > 自動化**] 下的 [系統偏好設定] 中，確定已列出 Visual Studio，並已核取 [ **Xcode** ]。

[![macOS 安全性](images/macos-security.png)](images/macos-security.png#lightbox)

如果已核取 [ **Xcode** ] 且錯誤訊息仍然出現，您可能需要重設 Visual Studio for Mac 的隱私權許可權。

這可以藉由啟動終端機視窗併發出下列命令來完成：

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

為確保上述變更生效，請重設您的 Mac 的系所。 如需相關指示，請參閱 [在您的 Mac 上重設 NVRAM 或](https://support.apple.com/HT204063)a。
