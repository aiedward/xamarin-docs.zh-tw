---
title: 開始使用 macOS Mojave
description: 本文件說明如何取得組建 macOS Xamarin.Mac Mojave 應用程式設定。 其中也會討論如何下載 Xcode 10 和更新 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067299"
---
# <a name="getting-started-with-macos-mojave"></a>開始使用 macOS Mojave

![預覽](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 macOS Mojave 支援目前為預覽狀態，這表示它可能包含 bug、 不是完成功能，而且可能會變更。
> 您可以將它用於只試驗。

> [!NOTE]
> 如需詳細資訊，請參閱[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)xamarin 預覽版本。

本文件說明如何取得組建 macOS Xamarin.Mac Mojave 應用程式設定。 其中也會討論如何下載 Xcode 10 和更新 Visual Studio for mac。

## <a name="download-and-install"></a>下載並安裝

1. **安裝最新的 Xcode 10 beta** – 註冊 Apple 開發人員可以下載並安裝最新版的從 Xcode 10 [Apple 開發人員入口網站](https://developer.apple.com/download/)。

   > [!NOTE]
   > Xcode 10 macOS Mojave SDK 所散發。

2. **執行 Xcode 10** – 執行 Xcode 10 之前更新並執行 Visual Studio for Mac; 它會安裝 Xamarin 需要某些工具。

3. **更新 Visual Studio for Mac** – 依照[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)安裝 Xamarin preview。

4. _（選擇性）_ **在 Mac 上安裝最新的 macOS Mojave beta** – 若要測試 Xamarin.Mac 應用程式使用新引入 macOS Mojave Api，已註冊的 Apple 開發人員可以[下載](https://developer.apple.com/download/)並安裝最新的 macOS Mojave 開發人員 beta。

   > [!TIP]
   > 即使您的應用程式不會使用任何新 macOS Mojave 應用程式開發介面，請務必在 macOS Mojave SDK （安裝 Xcode 10） 與建置和測試以確定一切運作正常。 如果應用程式不會呼叫任何新的 Api，您可以重新編譯時 macOS Mojave SDK，並測試而不升級您的 Mac 作業系統。

   > [!IMPORTANT]
   > 然後再升級您的 Mac 來 macOS Mojave 來建置及測試 Xamarin.Mac 呼叫新 macOS Mojave Api 的應用程式：
   > - 讀取[Apple 的版本資訊](https://developer.apple.com/download/)作業系統更新。
   > - 讀取[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)xamarin 預覽版本。 請注意，此第一次預覽不包含新 macOS Mojave AppKit Api （例如深色模式） 的繫結。

## <a name="related-links"></a>相關連結

- [下載 Xcode 10](https://developer.apple.com/download/)
- Xamarin 預覽[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)
