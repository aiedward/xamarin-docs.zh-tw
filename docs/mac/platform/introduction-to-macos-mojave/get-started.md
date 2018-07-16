---
title: 開始使用 macOS Mojave
description: 本文件說明如何取得建置 macOS Mojave Xamarin.Mac 應用程式設定。 它討論如何下載 Xcode 10 和更新 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: cf2725eafa18330a07a08db4235bad1a1ecd47b6
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030615"
---
# <a name="getting-started-with-macos-mojave"></a>開始使用 macOS Mojave

![預覽](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 macOS Mojave 支援目前為預覽狀態，這表示它包含 bug，並不具備完整功能，且可能會變更。
> 您可以使用它僅用於進行測試。

> [!NOTE]
> 如需詳細資訊，請閱讀 Xamarin 預覽版[釋放部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

本文件說明如何取得建置 macOS Mojave Xamarin.Mac 應用程式設定。 它討論如何下載 Xcode 10 和更新 Visual Studio for mac。

## <a name="download-and-install"></a>下載並安裝

1. **安裝最新的 Xcode 10 beta** – 已註冊的 Apple 開發人員可以下載並安裝最新版的 Xcode 10，從[Apple 開發人員入口網站](https://developer.apple.com/download/)。

   > [!NOTE]
   > MacOS Mojave SDK Xcode 10 一起散發。

2. **執行 Xcode 10** – 執行更新，並執行 Visual Studio for Mac; 之前的 Xcode 10 安裝 Xamarin 需要一些工具。

3. **更新 Visual Studio for Mac** – 依照[釋放部落格](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)安裝 Xamarin 預覽版。

4. _（選擇性）_ **在 Mac 上安裝最新的 macOS Mojave beta** – 若要測試 Xamarin.Mac 應用程式使用新引入的 macOS Mojave Api，已註冊的 Apple 開發人員可以[下載](https://developer.apple.com/download/)並安裝最新的 macOS Mojave 開發人員 beta 版。

   > [!TIP]
   > 即使您的應用程式不使用任何新的 macOS Mojave Api，請務必使用 macOS Mojave SDK （安裝 Xcode 10） 建立並測試以確定一切運作正常。 如果應用程式不會呼叫任何新的 Api，您可以重新編譯使用 macOS Mojave SDK，並測試它而不用升級您的 Mac 作業系統。

   > [!IMPORTANT]
   > 然後再升級您的 Mac，macos Mojave 來建置及測試 Xamarin.Mac 應用程式會呼叫新的 macOS Mojave Api:
   > - 讀取[Apple 的版本資訊](https://developer.apple.com/download/)作業系統更新。
   > - 讀取 Xamarin 預覽版[釋放部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

## <a name="related-links"></a>相關連結

- [下載 Xcode 10](https://developer.apple.com/download/)
- Xamarin 預覽[版本部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
