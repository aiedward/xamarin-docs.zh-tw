---
title: 在 Xamarin.iOS 中內嵌的架構
description: 本文件說明如何在 Xamarin.iOS 應用程式中內嵌的架構與共用程式碼。 這可與 mtouch 工具或原生參考。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2018
ms.openlocfilehash: cce5356fd1d3d9a5cf16370a4843c3541b00a7c0
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351430"
---
# <a name="embedded-frameworks-in-xamarinios"></a>在 Xamarin.iOS 中內嵌的架構

_本文件說明如何應用程式開發人員時，可以在自己的應用程式中內嵌使用者架構。_

使用 iOS 8.0 Apple 使得建立內嵌的架構，來共用應用程式擴充功能與在 Xcode 中主要的應用程式之間的程式碼。

Xamarin.iOS 9.0 在 Xamarin.iOS 應用程式新增支援使用這些內嵌的架構 （使用 Xcode 建立）。 *它會**不**就能夠從任何類型的 Xamarin.iOS 專案中建立內嵌的架構，只使用現有的原生 (OBJECTIVE-C) 架構。*

有兩種方式可使用在 Xamarin.iOS 中的架構：

- 將下列內容新增至其他 mtouch 引數，在專案的 mtouch 工具，以便傳遞給架構**iOS 組建**選項：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  此值必須為每個專案組態設定。

- 從內容功能表新增原生參考

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

以滑鼠右鍵按一下專案，並瀏覽加入原生參考

![](embedded-frameworks-images/xam-native-refs.png "選取 Mac 在 Visual Studio 中新增原生參考")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

以滑鼠右鍵按一下專案，並瀏覽加入原生參考

![](embedded-frameworks-images/vs-native-refs.png "選取 Visual Studio 中的 新增原生參考")

-----

  這適用於所有的組態。

在 Visual Studio for Mac 和 Xamarin 工具適用於 Visual Studio 的未來版本中將可能使用在 IDE 中的架構 （而不需要手動編輯專案檔）。

幾個範例專案都位於[github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>限制

- 只支援內嵌的架構[整合](~/cross-platform/macios/unified/index.md)專案。
- 內嵌的架構只支援的部署目標的專案中至少 iOS 8.0 以上版本。
- 如果擴充功能需要內嵌的架構，容器應用程式必須也有 framework，否則為架構將不會包含在應用程式套件組合中的參考。

## <a name="the-mono-runtime"></a>Mono 執行階段

在內部 Xamarin.iOS 會利用這項功能可以與 Mono 執行階段連結的架構，而不是以靜態方式連結到每個擴充功能和容器應用程式的 Mono 執行階段。

這是自動完成的如果容器應用程式是整合的應用程式，它包含延伸模組以及目標部署為 iOS 8.0 或更新版本。

沒有擴充功能的應用程式將仍與 Mono 執行階段靜態連結，因為沒有使用一種架構，如果只有一個參考它的應用程式對大小產生負面影響。

此行為可覆寫應用程式開發人員，將下列內容新增為專案的 iOS 組建 選項的其他 mtouch 引數：

- `--mono:static`： 以靜態方式連結 Mono 執行階段使用。
- `--mono:framework`： 連結與 Mono 執行階段的架構。

Mono 執行階段即使沒有擴充功能的應用程式的架構為連結的其中一個案例是要減少可執行檔的大小，以克服任何 Apple 會強制執行可執行檔的大小限制。 如需參考，Mono 執行階段會增加大約 1.7 MB 每個架構，（由於的 Xamarin.iOS 8.12，不過他互異之間發行版本之間，甚至在應用程式）。 而 Mono 架構增加大約 2.3 MB 每個架構，這表示單一架構應用程式，而不需要任何擴充功能，讓應用程式連結與 Mono 執行階段架構將會壓縮由 ~1.7MB，可執行檔，但新增 ~2.3MB 架構，產生在 ~0.6MB 更大的應用程式一起指定。

