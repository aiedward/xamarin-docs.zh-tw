---
title: Xamarin 中的 Embedded framework
description: 本檔說明如何在 Xamarin iOS 應用程式中與內嵌架構共用程式碼。 您可以使用 mtouch 工具或原生參考來完成這項作業。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 6287dca8660c1147455beb22304b7f8637ac7fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292777"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Xamarin 中的 Embedded framework

_本檔說明應用程式開發人員可以如何在其應用程式中內嵌使用者架構。_

透過 iOS 8.0 Apple，您可以建立內嵌架構，在應用程式擴充功能與 Xcode 中的主要應用程式之間共用程式碼。

Xamarin. iOS 9.0 新增了在 Xamarin iOS 應用程式中使用這些內嵌架構（以 Xcode 建立）的支援。 *您**無法**從任何類型的 Xamarin iOS 專案建立內嵌的架構，只會使用現有的原生（目標-C）架構。*

有兩種方式可以使用 Xamarin 中的架構。 iOS：

- 將下列內容加入至專案的**IOS 組建**選項中的其他 mtouch 引數，以將架構傳遞至 mtouch 工具：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  這必須針對每個專案設定進行設定。

- 從內容功能表新增原生參考

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

以滑鼠右鍵按一下專案，然後流覽以加入原生參考

![](embedded-frameworks-images/xam-native-refs.png "選取 [在 Visual Studio for Mac 中新增原生參考]")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

以滑鼠右鍵按一下專案，然後流覽以加入原生參考

![](embedded-frameworks-images/vs-native-refs.png "選取 [在 Visual Studio 中新增原生參考]")

-----

  這適用于所有設定。

在未來版本的 Visual Studio for Mac 和適用于 Visual Studio 的 Xamarin 工具中，可以從 IDE 內取用架構（不需要手動編輯專案檔）。

您可以在[github](https://github.com/rolfbjarne/embedded-frameworks)上找到幾個範例專案

## <a name="limitations"></a>限制

- 只有在[整合](~/cross-platform/macios/unified/index.md)專案中才支援內嵌架構。
- 只有在至少具有 iOS 8.0 部署目標的專案中，才支援內嵌架構。
- 如果延伸模組需要內嵌架構，則容器應用程式也必須有架構的參考，否則架構將不會包含在應用程式套件組合中。

## <a name="the-mono-runtime"></a>Mono 執行時間

內部 Xamarin 會利用這項功能，將 Mono 執行時間連結為架構，而不是將 Mono 執行時間靜態連結至每個擴充功能和容器應用程式。

如果容器應用程式是整合的應用程式，它會包含延伸模組，而目標部署為 iOS 8.0 或更高版本，這會自動完成。

沒有延伸模組的應用程式仍會以靜態方式連結到 Mono 執行時間，因為如果只有一個應用程式參考它，則使用架構會有大小上的負面影響。

應用程式開發人員可以覆寫此行為，方法是在專案的 iOS 組建選項中新增下列做為額外的 mtouch 引數：

- `--mono:static`：以靜態方式連結 Mono 執行時間。
- `--mono:framework`：以 Mono 執行時間做為架構的連結。

將 Mono 執行時間連結為架構的其中一個案例，即使是沒有擴充功能的應用程式，也可以減少可執行檔的大小，以克服 Apple 強制執行檔的任何大小限制。 就參考而言，Mono 執行時間會為每個架構增加大約 1.7 MB （從 Xamarin. iOS 8.12，但在不同版本之間，甚至是在應用程式之間）。 Mono 架構在每個架構中大約增加了 2.3 MB，這表示針對不含任何延伸模組的單一架構應用程式，讓應用程式連結以 Mono 執行時間做為架構，會將可執行檔縮小 ~ 1.7 MB，但會增加 ~ 2.3 mb 的架構，產生的結果在 ~ 0.6 MB 的應用程式 alltogether 中。

