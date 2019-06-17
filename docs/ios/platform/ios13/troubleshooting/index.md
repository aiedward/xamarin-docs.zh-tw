---
title: Xamarin 和 iOS 13 疑難排解
description: 本節包含與 iOS 13 的 Xamarin 功能的疑難排解秘訣。
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039785"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>疑難排解適用於 iOS 13 和 Xamarin.iOS 的秘訣

![預覽功能](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>更新 Xcode 11 來停止模擬器無法啟動

若要更新之後**Xcode 11 beta 1**每次啟動模擬器時擲回下列例外狀況，而且模擬器無法啟動。 這會使用所有的模擬器。

### <a name="exception"></a>例外

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>因應措施

直到[修正](https://github.com/xamarin/xamarin-macios/issues/6216)，重新安裝舊的模擬器架構，可讓開發人員繼續運作，可以遵循下列步驟：

> [!NOTE]
> 這些步驟假設您有兩個 Xcode 應用程式：
> - **Xcode11 beta1.app** -beta 版，無法在模擬器與 Visual Studio for mac。
> - **Xcode102.app** – Xcode 10 的穩定版本。 您可能也會呼叫**Xcode.app**。
>
> 變更下列命令列範例，以適合您的設定。

1. 請確定您已透過 xcode-select 選取的 Xcode 11:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. 執行，如有需要安裝程式工具第一次。

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. 移除下列架構：

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. 切換回舊的 Xcode 版本

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. 重新執行的第一個啟動工具_舊_您剛才選取的 Xcode 版本

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

這些步驟後，您應該能夠一次使用 iOS 模擬器。