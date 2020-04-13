---
title: 針對 Mac App Store 進行統合
description: 本文件描述如何搭售供發行之用的 Xamarin.Mac 應用程式和 Mac App Store。 並討論程式碼簽署選項及建置。
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f4d38bb66a34257c1e0a27c5fbbfe16f59743e83
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725502"
---
# <a name="bundling-for-the-mac-app-store"></a>針對 Mac App Store 進行統合

本節說明使用 Visual Studio for Mac 建置應用程式以供在 Mac App Store 中發行的基本要點。 視額外功能 (例如 iCloud 存取與推播通知) 而定，可能會需要進一步的設定，但這不在本文的討論範圍內。

> [!NOTE]
> 開始進行本節之前，開發人員必須已建立生產佈建設定檔，才能針對 Mac App Store 來建置。 若要建立必要的佈建設定檔，請參閱[設定檔指示](profiles.md)。

## <a name="code-signing-options"></a>程式碼簽署選項

在更新程式碼簽署與封裝選項之前，請將 [組態]**** 變更為 [發行]****。 簽署應用程式以供在 App Store 中發行時，開發人員必須確定他們使用自己公司的 [身分識別]**** 與上述建立的佈建設定檔。

[![編輯代碼簽章選項](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

請務必勾選 [Mac 組建]**** 設定中建立安裝程式套件的選項：

[![編輯建置選項](bundling-images/build.png "編輯建置選項")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Build

進行組建之前，請確定已選取 [發行]**** 組態。 當開發人員建置應用程式時會收到_兩次_提示 (使用應用程式和安裝程式這兩種憑證)：

![允許應用程式使用憑證，會出現兩次](bundling-images/perms02.png)

建置應用程式之後，開發人員可在專案上按一下滑鼠右鍵，然後選擇 [在 Finder 中顯示]**** 來尋找套件檔案 (位於下例所示的 `bin/Release/AppStore` 目錄中)。  此套件檔案包含應用程式的安裝程式，可提交給 Apple 以納入 Mac App Store。

> [!div class="mx-imgBorder"]
> ![在 Finder 中選取建置套件](bundling-images/path.png)

## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [您好,Mac 樣品](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/developer-id/) \(英文\)
