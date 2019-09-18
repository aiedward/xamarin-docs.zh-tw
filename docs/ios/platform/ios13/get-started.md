---
title: 開始使用 iOS 13、iPadOS 13、tvOS 13 和 watchOS 6
description: 本檔說明如何使用 Xamarin 建立 iOS 13、iPadOS 13、tvOS 13 和 watchOS 6 應用程式。 它討論如何下載 Xcode 11 和 update Visual Studio for Mac。
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 9dc6f234c4bc14c3644d953eef0d2e0f397436e5
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033082"
---
# <a name="get-started-with-ios-13"></a>IOS 13 入門

![預覽功能](~/media/shared/preview.png)

本檔說明如何開始建立 Xamarin 應用程式，以呼叫 Xcode 11 （適用于 iOS 13）發行的 Api。 使用預覽需要 macOS 10.14.4 （Mojave）或更新版本。

## <a name="download-and-install"></a>下載並安裝

1. **安裝 Xcode 11 Beta** –註冊的 Apple 開發人員可以從[Apple 開發人員入口網站](https://developer.apple.com/download/)或**App Store**下載並安裝最新版的 Xcode 11 Beta 版。

2. **執行 Xcode 11 Beta** –執行 Xcode 11，再更新和執行 Visual Studio for Mac，因為它會安裝 Xamarin 所需的一些工具。

3. 在 Visual Studio for Mac 中，選取  **Visual Studio > 檢查更新 ...** ，選取  **Xcode 11 預覽** 頻道，然後安裝可用的更新。 如果您看不到此頻道，請確定您已從**Visual Studio > 帳戶**登入您的帳戶 ...。

4. 在 Visual Studio for Mac 中，選取  **Visual Studio > 偏好設定 > 專案 > Apple > SDK 位置**，然後選取  **Xcode-Beta**。

5. 選擇性如果使用_Xcode 11 Beta 3_評估此預覽版本，您必須啟用連結。 以滑鼠右鍵按一下您的專案，流覽至 [**選項] > [IOS 組建 > 連結器行為**]，並將 [連結器行為] 值設定為**僅連結 Framework sdk**。 在未來的預覽中，不需要此因應措施。

6. 選擇性**在您的 ios 裝置上安裝 ios 13** –針對使用 Xcode 11 引進之 api 的應用程式進行裝置測試，已註冊的 Apple 開發人員可以在其裝置上[下載](https://developer.apple.com/download)並安裝作業系統。 由於 iOS 處於搶鮮版（Beta），請小心將它安裝在您的主要裝置上。

   > [!TIP]
   > 即使您的應用程式未使用任何新的 Api，請務必使用最新的 Xcode 11 Sdk 來建立它，並加以測試，以確保所有專案都能如預期般運作。 如果應用程式未呼叫任何新的 Api，您可以使用這些新的 Sdk 重新編譯，並在尚未升級到新作業系統的裝置上進行測試。
   >
   > 將您的裝置升級至 Apple 的最新作業系統版本，以測試您的 Xamarin 應用程式之前，請務必：
   >
   > - 閱讀[Apple 的](https://developer.apple.com/download/)作業系統更新版本資訊。

## <a name="related-links"></a>相關連結

- [下載 Xcode](https://developer.apple.com/download/)
- [Xamarin iOS preview 版本資訊](/xamarin/ios/release-notes/12/12.99)
