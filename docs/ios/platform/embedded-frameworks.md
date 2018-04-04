---
title: 內嵌的架構
description: 本文件說明應用程式開發人員如何在其應用程式中內嵌使用者介面架構。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f223d8ef6e89cc44822b8a831dbba3cf71d727c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="embedded-frameworks"></a>內嵌的架構

_本文件說明應用程式開發人員如何在其應用程式中內嵌使用者介面架構。_

使用 iOS 8.0 Apple，變成可以建立內嵌到應用程式擴充功能，並在 Xcode 中主要的應用程式之間共用程式碼架構。

Xamarin.iOS 9.0 Xamarin.iOS 應用程式中，加入使用這些內嵌的架構 （使用 Xcode 建立） 的支援。 *它會**不**能夠從任何類型的 Xamarin.iOS 專案中建立內嵌的架構，只使用現有的原生 (OBJECTIVE-C) 架構。*

有兩種方式可以取用 Xamarin.iOS 架構：

- 傳架構 mtouch 工具，在專案中的其他 mtouch 引數中加入下列**iOS 建置**選項：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  這必須為每個專案組態設定。

- 從內容功能表加入原生參考

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

以滑鼠右鍵按一下專案，並瀏覽加入原生參考

![](embedded-frameworks-images/xam-native-refs.png "選取 Visual Studio 中加入原生參考 for Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

以滑鼠右鍵按一下專案，並瀏覽加入原生參考

![](embedded-frameworks-images/vs-native-refs.png "選取 Visual Studio 中的 新增原生參考")

-----

  這適用於所有的組態。

適用於 Mac 的 Visual Studio 和 Xamarin 工具的 Visual Studio 的未來版本中將可能使用從 IDE 中的架構 （而不需手動編輯專案檔）。

幾個範例專案可以找到上[github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>限制

- 只支援內嵌的架構[統一](~/cross-platform/macios/unified/index.md)專案。
- 內嵌的架構才支援的部署目標的專案中至少 iOS 8.0 以上版本。
- 如果延伸模組需要內嵌的架構，容器應用程式必須也會有的架構，否則為架構將不會包含在應用程式套件組合的參考。

## <a name="the-mono-runtime"></a>單聲道的執行階段

在內部 Xamarin.iOS 會利用這項功能的架構，而不是以靜態方式連結到每個擴充功能和容器應用程式的單聲道的執行階段當做連結 Mono 執行階段。

會自動完成此如果容器應用程式整合應用程式，它包含擴充功能，而目標部署 iOS 8.0 或更新版本。

不具副檔名的應用程式將仍與單聲道的執行階段以靜態方式連結，因為只有一個正在參照它的應用程式是否使用架構大小負面影響。

這個行為會覆寫應用程式，開發人員所做為專案的 iOS 建置選項中的其他 mtouch 引數中加入下列：

- `--mono:static`： 以靜態方式連結 Mono 執行階段。
- `--mono:framework`： 連結與單聲道 runtime 做的架構。

連結的單聲道的執行階段版本做甚至不具副檔名的應用程式架構的其中一個案例是要減少可執行檔的大小，以克服 Apple 會強制執行的可執行檔的任何大小限制。 如需參考，單聲道的執行階段會將大約 1.7 MB，每個架構，（如的 Xamarin.iOS 8.12，不過他更改之間的版本中，與應用程式之間，甚至）。 而連結 Mono 架構增加大約 2.3 MB 每個架構，這表示單一架構應用程式中不含任何擴充功能，讓應用程式連結與單聲道 runtime 架構將會壓縮 ~1.7MB，由可執行檔，但加入 ~2.3MB 架構，產生在 ~0.6MB 更大的應用程式要一起指定。

