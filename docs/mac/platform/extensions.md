---
title: Xamarin.Mac 延伸模組支援
description: 本文件說明 Xamarin.Mac 的支援搜尋、 共用及現今的延伸模組。 它會檢查限制與已知的問題，逐步解說和範例應用程式的連結，並提供用於擴充功能的秘訣。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 0f4d6bb042f8bc8d48b45d7148984a53e3ce3437
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032508"
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 延伸模組支援

在 Xamarin.Mac 2.10 支援已針對多個 macOS 擴充點：

- 搜尋工具
- 共用
- 今天

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>限制與已知的問題

下列的限制，而且知道開發 Xamarin.Mac 中的擴充功能時可能發生的問題：

* 目前沒有偵錯支援在 Visual Studio for mac。 所有偵錯需要透過**NSLog**並**主控台**。 請參閱下列秘訣區段，如需詳細資訊。
* 延伸模組都必須包含主應用程式中時，請執行與暫存器與系統的一次。 然後必須啟用中**延伸模組**一節**系統偏好設定**。 
* 有些延伸模組當機可能造成不穩定的主機應用程式，而且會導致奇怪的行為。 特別的是， **Finder**並**今天**區段**通知中心**可能會變成 「 夾紙 」，並會變成沒有回應。 這已擴充功能專案中在 Xcode 中，有經驗，而且目前出現 不相關，至 Xamarin.Mac。 通常您可以在系統記錄檔中 (透過**主控台**，如需詳細資訊，請參閱提示) 列印重複發生的錯誤訊息。 重新啟動 macOS 似乎會修正此問題。

<a name="Tips" />

## <a name="tips"></a>秘訣

使用 Xamarin.Mac 中的延伸模組時，下列秘訣可以是很有幫助：

- 由於 Xamarin.Mac 目前不支援偵錯擴充功能，偵錯的體驗主要取決於執行和`printf`喜歡陳述式。 不過，延伸模組以沙箱處理序執行，因此`Console.WriteLine`未做其他 Xamarin.Mac 應用程式中所顯示的一樣。 叫用[`NSLog`直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)會輸出至系統記錄檔的偵錯訊息。
- 任何未攔截的例外狀況將會損毀提供只有少量的有用資訊的延伸模組程序**系統記錄檔**。 麻煩的程式碼中包裝`try/catch`(Exception) 區塊`NSLog`的之前重新擲回的內容可能會很有用。
- **系統記錄檔**您可以從存取**主控台**下的應用程式**應用程式** > **公用程式**:

    [![](extensions-images/extension02.png "系統記錄檔")](extensions-images/extension02.png#lightbox)
- 如先前所述，執行延伸模組的主應用程式會向系統。 刪除應用程式套件組合，以取消註冊。 
- 如果已註冊的應用程式的延伸模組的 「 群組 」 版本，請使用下列命令來尋找它們 （因此它們可以被刪除）： `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>逐步解說和範例應用程式

因為開發人員會建立，並為 Xamarin.iOS 延伸模組相同的方式使用 Xamarin.Mac 延伸模組，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)文件，如需詳細資訊。

包含小型範例 Xamarin.Mac 專案中，可以找到每個延伸模組類型的實用範例[此處](https://developer.xamarin.com/samples/mac/ExtensionSamples/)。

<a name="Summary" />

## <a name="summary"></a>總結

本文所使用 Xamarin.Mac 版本 2.10 （和更新版本） 應用程式中的擴充功能的概觀。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [macOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) \(英文\)
