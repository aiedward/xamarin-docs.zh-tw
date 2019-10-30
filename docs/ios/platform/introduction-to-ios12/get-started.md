---
title: IOS 12、tvOS 12 和 watchOS 5 入門
description: 本檔說明如何使用 Xamarin 設定組建 iOS 12、tvOS 12 和 watchOS 5 應用程式。 它討論如何下載 Xcode 10 和 update Visual Studio for Mac 和 Visual Studio 2017。
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2018
ms.openlocfilehash: 1db249a9e07f178461bcb052508d08f54ecea121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032012"
---
# <a name="get-started-with-ios-12-tvos-12-and-watchos-5"></a>IOS 12、tvOS 12 和 watchOS 5 入門

本檔說明如何開始建立 Xamarin 應用程式，以呼叫 Xcode 10 （適用于 iOS 12、tvOS 12 和 watchOS 5）所發行的 Api。

## <a name="download-and-install"></a>下載並安裝

1. **安裝 Xcode 10** –註冊的 Apple 開發人員可以從[Apple 開發人員入口網站](https://developer.apple.com/download/)或**App Store**下載並安裝最新版本的 Xcode 10。

2. **執行 Xcode 10** –執行 Xcode 10，然後 Visual Studio for Mac 或 Visual Studio 2017，因為它會安裝 Xamarin 所需的一些工具。

3. **更新 Visual Studio for Mac 和 Visual Studio 2017** –確保您擁有最新穩定版本的 Xamarin。

4. _（選擇性）_ **在您的 ios 裝置上安裝 ios 12** –

   針對使用 Xcode 10 引進之 Api 的應用程式進行裝置測試，已註冊的 Apple 開發人員可以在其裝置上[下載](https://developer.apple.com/download)並安裝作業系統。

   > [!TIP]
   > 即使您的應用程式未使用任何新的 Api，請務必使用最新的 Xcode 10 Sdk 來建立它，並加以測試，以確保所有專案都能如預期般運作。 如果應用程式未呼叫任何新的 Api，您可以使用這些新的 Sdk 重新編譯，並在尚未升級到新作業系統的裝置上進行測試。
   >
   > 將您的裝置升級至 Apple 的最新作業系統版本，以測試您的 Xamarin 應用程式之前，請務必：
   >
   > - 閱讀[Apple 的](https://developer.apple.com/download/)作業系統更新版本資訊。
   > - 閱讀 Xamarin preview[版本的 blog 文章](https://releases.xamarin.com/preview-release-xcode-10-beta-6/)。

## <a name="related-links"></a>相關連結

- [下載 Xcode](https://developer.apple.com/download/)
