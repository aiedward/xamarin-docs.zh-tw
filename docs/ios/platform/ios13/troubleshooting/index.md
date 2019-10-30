---
title: Xamarin 和 iOS 13 疑難排解
description: 本節包含與 iOS 13 相關之 Xamarin 功能的疑難排解秘訣。
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/12/2019
ms.openlocfilehash: 6ccd0a100e2a1f01d33fee481df85d976d2191a8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031705"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>適用于 iOS 13 和 Xamarin 的疑難排解秘訣

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>更新至 Xcode 11 會阻止模擬器啟動

每次啟動模擬器時，更新至**Xcode 11 Beta 1**時，會擲回下列例外狀況，而且模擬器不會啟動。 所有模擬器都會發生這種情況。

### <a name="exception"></a>例外

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>因應措施

在[修正](https://github.com/xamarin/xamarin-macios/issues/6216)之前，您可以遵循下列步驟來重新安裝舊的模擬器架構，以允許開發人員繼續工作：

> [!NOTE]
> 這些步驟假設您有兩個 Xcode 應用程式：
>
> - **Xcode11-Beta1** -不適用於模擬器和 Visual Studio for Mac 的 Beta 版。
> - **Xcode102** – Xcode 10 的穩定版本。 您的**應用程式**也可能稱為 Xcode。
>
> 視情況變更適用于您設定的命令列範例。

1. 請確定您已透過 Xcode 選取 Xcode 11-select：

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. 如有需要，請在第一次執行安裝工具。

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. 移除下列架構：

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. 切換回舊的 Xcode 版本

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. 針對您剛才選取的_舊_Xcode 版本重新執行第一個啟動工具

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

遵循這些步驟之後，您應該就可以再次使用 iOS 模擬器。
