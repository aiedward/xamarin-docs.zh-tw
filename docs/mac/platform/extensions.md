---
title: "Xamarin.Mac 延伸模組支援"
description: "本文涵蓋 Xamarin.Mac 2.10 （和更高） 的版本中的延伸模組支援。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 530e53230e9f0dea165b083fa6795558025a293f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 延伸模組支援

Xamarin.Mac 2.10 預覽中新增多個 macOS 擴充點的支援：

- 搜尋
- 共用
- 今天

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>限制與已知的問題

下列的限制，而且知道開發 Xamarin.Mac 中的延伸模組時，可能會發生的問題：

* 目前沒有 Visual Studio 中的偵錯支援 for mac。 所有偵錯需要透過**NSLog**和**主控台**。 請參閱提示下的一節以取得詳細資料。
* 必須包含擴充功能時在主機應用程式中，以系統登錄執行一次。 然後必須啟用中**延伸**區段**系統偏好設定**。 
* 某些延伸模組的當機可能造成不穩定的主應用程式，並導致奇怪的行為。 特別是， **Finder**和**今天**區段**通知中心**可能會變成 「 夾紙 」，而且沒有回應。 這已擴充功能專案中在 Xcode 中，有經驗，而且目前出現 Xamarin.Mac 無關。 這通常出現在系統記錄檔中 (透過**主控台**，如需詳細資訊，請參閱秘訣) 列印重複發生的錯誤訊息。 重新啟動 macOS 會出現修正此問題。

<a name="Tips" />

## <a name="tips"></a>秘訣

使用 Xamarin.Mac 中延伸模組時，下列秘訣可以是很有幫助：

- 因為 Xamarin.Mac 目前不支援偵錯擴充功能，偵錯的體驗主要取決於執行和`printf`喜歡陳述式。 不過，擴充功能沙箱處理序中執行，因此`Console.WriteLine`將不會如同其他 Xamarin.Mac 應用程式中。 叫用[`NSLog`直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)會輸出至系統記錄檔的偵錯訊息。
- 任何無法攔截的例外狀況將會損毀提供只有少量的有用資訊的延伸模組程序**系統記錄檔**。 有問題的程式碼中自動換行`try/catch`（例外狀況） 區塊`NSLog`的之前重新擲回可能會很有用。
- **系統記錄檔**可以存取從**主控台**待測應用程式**應用程式** > **公用程式**:

    [![](extensions-images/extension02.png "系統記錄檔")](extensions-images/extension02.png#lightbox)
- 如先前所述，執行擴充主應用程式會向系統。 刪除應用程式資源存放區中取消登錄該。 
- 如果已登錄的應用程式擴充功能的 「 偏離 」 版本，請使用下列命令來找到這些服務 （以便它們可以被刪除）： `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>逐步解說和範例應用程式

因為開發人員會建立，以及做為 Xamarin.iOS 擴充功能相同的方式處理 Xamarin.Mac 擴充功能，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)文件以取得詳細資料。

包含小型範例 Xamarin.Mac 專案，可以找到每個擴充功能類型的實用範例[這裡](https://developer.xamarin.com/samples/mac/ExtensionSamples/)。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得快速查看使用 Xamarin.Max 版本 2.10 （和大於） 應用程式中的擴充功能。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
