---
title: 針對 Mac App Store 進行統合
description: 本指南逐步說明統合 Xamarin.Mac 應用程式以發行至 Mac App Store。
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 88d813331dfce437f3668ada8f008bbbd5fdc3de
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="bundle-for-mac-app-store"></a>Mac App Store 的套件組合

本節說明使用 Visual Studio for Mac 建置應用程式以供在 Mac App Store 中發行的基本要點。 視額外功能 (例如 iCloud 存取與推播通知) 而定，可能會需要進一步的設定，但這不在本文的討論範圍內。

> [!NOTE]
> 開始進行本節之前，開發人員必須已建立生產佈建設定檔，才能針對 Mac App Store 來建置。 請參閱本文稍早的指示，以了解如何建立必要的佈建設定檔。

## <a name="code-signing-options"></a>程式碼簽署選項

在更新程式碼簽署與封裝選項之前，請將 [組態] 變更為 [發行]。 簽署應用程式以供在 App Store 中發行時，開發人員必須確定他們使用自己公司的 [身分識別] 與上述建立的佈建設定檔。

 [![編輯程式碼簽署選項](bundling-images/config02.png "編輯程式碼簽署選項")](bundling-images/config02-large.png#lightbox)

請務必勾選 [Mac 組建] 設定中建立安裝程式套件的選項：

[![編輯組建選項](bundling-images/config03.png "編輯組建選項")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>組建

進行組建之前，請確定已選取 [發行] 組態。 當開發人員建置應用程式時，系統會提示他們使用這兩種憑證：

 ![允許應用程式使用憑證](bundling-images/image62.png "允許應用程式使用憑證")

 ![允許應用程式使用憑證](bundling-images/image63.png "允許應用程式使用憑證")

建置應用程式之後，開發人員可在專案上按一下滑鼠右鍵，然後選擇 [開啟包含的資料夾] 即可找到套件檔案 (在下列範例所示的 `bin/x86/AppStore` 目錄中)。  此套件檔案包含應用程式的安裝程式，可提交給 Apple 以納入 Mac App Store。

 ![在 Finder 中選取組建套件](bundling-images/image64.png "在 Finder 中選取組建套件")


## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [Hello, Mac 範例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/resources/developer-id/) \(英文\)
