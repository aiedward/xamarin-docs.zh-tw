---
title: IOS 14 入門
description: 本檔說明如何設定為使用 Xamarin 建立 iOS 14 應用程式。 它會討論如何下載 Xcode 12 和更新 Visual Studio for Mac。
ms.prod: xamarin
ms.assetid: 0d721b4b-86bd-495a-8c0f-1f2f9fd6855e
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: 1bf77ca71d5fda945c81ac3ac6b338eec0164a76
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843568"
---
# <a name="get-started-with-ios-14"></a>IOS 14 入門

本檔說明如何開始建立 Xamarin 應用程式，以呼叫以 Xcode 12 for iOS 14 發行的 Api。 使用預覽版需要 macOS Catalina 10.15.4 或更新版本。

## <a name="download-and-install"></a>下載並安裝

1. **安裝 Xcode 12** –註冊的 apple 開發人員可以從 [Apple 開發人員入口網站](https://developer.apple.com/download/) 或 **App Store**下載並安裝最新版本的 Xcode 12。

2. **執行 Xcode 12** –在更新和執行 Visual Studio for Mac 或 Visual Studio 2019 之前執行 Xcode 12，因為它會安裝一些 Xamarin 所需的工具。

3. **更新 Visual Studio for Mac 和 Visual Studio 2019** -確定您擁有最新的 Xamarin 穩定版本。

4.  (選擇性) **在您的 ios 裝置上安裝 ios 14** –針對使用 Xcode 12 所引進之 api 的應用程式進行裝置測試，已註冊的 Apple 開發人員可以在其裝置上 [下載](https://developer.apple.com/download) 並安裝作業系統。 

   > [!TIP]
   > 即使您的應用程式不使用任何新的 Api，請務必使用最新的 Xcode 12 Sdk 來建立它，並加以測試，以確定一切如預期般運作。 如果應用程式不會呼叫任何新的 Api，您可以使用這些新的 Sdk 重新編譯它，並在尚未升級為新作業系統的裝置上進行測試。
   >
   > 將裝置升級至 Apple 的最新作業系統版本，以測試 Xamarin 應用程式之前，請務必：
   >
   > - 閱讀 [Apple 的](https://developer.apple.com/download/) 作業系統更新版本資訊。

## <a name="related-links"></a>相關連結

- [下載 Xcode](https://developer.apple.com/download/)
- [Xamarin.iOS 版本資訊](/xamarin/ios/release-notes/14/14.0)
- [Xcode 12 版本資訊](https://developer.apple.com/documentation/xcode-release-notes/xcode-12-release-notes)
