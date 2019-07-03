---
title: 開始使用 iOS 13、 iPadOS 13、 13、 tvOS 和 watchOS 6
description: 本文件說明如何取得設定建置 iOS 13、 iPadOS 13、 13，tvOS 和 watchOS 6 應用程式與 Xamarin。 它討論如何下載 Xcode 11 和更新 Visual Studio for Mac 和 Visual Studio 2019。
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: d2bb69394d8e9bfeb949a734291179a3e6f5a495
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540434"
---
# <a name="get-started-with-ios-13"></a>開始使用 iOS 13

![預覽功能](~/media/shared/preview.png)

本文件說明如何開始建置 Xamarin ios 13 呼叫與 Xcode 11 日發行的 Api 的應用程式。 使用預覽需要 macOS 10.14.4 (Mojave) 或更新版本。

## <a name="download-and-install"></a>下載並安裝

1. **安裝 Xcode 11 beta** – 已註冊的 Apple 開發人員可以下載並安裝最新版的 Xcode 11 beta 版，從[Apple 開發人員入口網站](https://developer.apple.com/download/)或**App Store**。

2. **執行 Xcode 11 beta 版**– 之前更新並執行 Visual Studio for Mac，則會安裝一些工具，Xamarin，執行 Xcode 11 要求。

3. 在 Visual Studio for Mac 中，選取**Visual Studio > 檢查更新...** ，選取**Xcode 11 預覽**通道，並安裝可用的更新。

4. 在 Visual Studio for Mac 中，選取**Visual Studio > 喜好設定 > 專案 > SDK 的位置 > Apple** ，然後選取**Xcode beta.app**。

5. （選擇性）如果評估使用此預覽_Xcode 11 beta 3_，您必須啟用連結。 您的專案上按一下滑鼠右鍵，瀏覽至**選項 > iOS 組建 > 連結器行為**並將連結器行為的值設定為**僅連結 Framework Sdk**。 此因應措施不會有需要在未來的預覽版。

6. （選擇性）**安裝在您的 iOS 裝置上的 iOS 13** – 適用於裝置與 Xcode 11 中，已註冊的 Apple 開發人員可以使用 Api 導入的應用程式測試[下載](https://developer.apple.com/download)並在其裝置上安裝作業系統。 由於 iOS 是 beta 版中，小心安裝在您的主要裝置上。

   > [!TIP]
   > 即使您的應用程式不使用任何新的 Api，請務必使用最新的 Xcode 11 Sdk 建置它，再測試它，以確定一切運作正常。 如果應用程式不會呼叫任何新的 Api，您可以重新編譯使用這些新的 Sdk，並在尚未升級至新的作業系統的裝置上進行測試。
   >
   > 將您的裝置升級至最新的作業系統版本從 Apple，以測試您的 Xamarin 應用程式之前，請務必先：
   >
   > - 讀取[Apple 的版本資訊](https://developer.apple.com/download/)作業系統更新。

## <a name="related-links"></a>相關連結

- [下載 Xcode](https://developer.apple.com/download/)
