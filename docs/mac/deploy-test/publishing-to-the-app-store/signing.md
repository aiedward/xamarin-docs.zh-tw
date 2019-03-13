---
title: 使用開發人員識別碼簽署 Xamarin.Mac 應用程式
description: 本文件描述如何使用開發人員識別碼簽署 Xamarin.Mac 應用程式，以便在 Mac App Store 外散發。 並討論程式碼簽署選項及建置。
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 9294080f361d280bcb61ea162320da57f44a2a95
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117664"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>使用開發人員識別碼簽署 Xamarin.Mac 應用程式

如果開發人員打算直接向 macOS 使用者散發應用程式，Apple 建議使用他們的開發人員識別碼來簽署應用程式，以便在啟用**閘道管理員**的 macOS 系統上安裝。 如果尚未簽署應用程式，**閘道管理員**會透過警示訊息來阻止使用者安裝 (啟動時按住 Control 鍵即可略過此限制)。

閱讀更多 Apple 網站上[開發人員識別碼和閘道管理員](https://developer.apple.com/resources/developer-id/) \(英文\) 與[在 Mac App Store 外部散發](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) \(英文\) 的相關資訊。

## <a name="code-signing-options"></a>程式碼簽署選項

若要建置可對使用者直接部署的應用程式 (不透過 Mac App Store)，請將**簽署設定**設為使用 [開發人員識別碼]。 務必要編輯 [發行] 組態。

 [![](signing-images/config02.png "Mac 簽署選項")](signing-images/config02.png#lightbox)


## <a name="build"></a>組建

建置之前，請務必選取正確的組態，並在 [Mac 組建] 設定中選擇要建立安裝套件：

[![](signing-images/config03.png "建置選項")](signing-images/config03.png#lightbox)

建置應用程式時，系統會提示開發人員使用這兩種憑證：

 [![](signing-images/image57.png "允許鑰匙圈存取")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "允許鑰匙圈存取")](signing-images/image58.png#lightbox)

建置應用程式之後，開發人員可在專案上按一下滑鼠右鍵，然後選擇 [開啟包含的資料夾] 即可找到套件檔案 (在 `bin/Release` 目錄中)。 此套件檔案包含應用程式的安裝程式，因此可散發給任何 macOS 使用者進行安裝。

 [![](signing-images/image59.png "在 Finder 中選取應用程式套件")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>相關連結

- [安裝](~//mac/get-started/installation.md)
- [Hello, Mac 範例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [工具指南：對應用程式進行程式碼簽署](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/resources/developer-id/) \(英文\)
