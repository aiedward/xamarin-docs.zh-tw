---
title: Xamarin.Mac 延伸模組支援
description: 本檔說明 Xamarin 對搜尋工具、共用和今日延伸模組的支援。 它會檢查限制和已知問題、逐步解說和範例應用程式的連結，並提供使用延伸模組的秘訣。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 9a9dbb63b78b00a9bcac9d7833530da02890afc6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017304"
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 延伸模組支援

在 2.10 Xamarin 中，已為多個 macOS 擴充點新增支援：

- 搜尋工具
- 共用
- 今天

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>限制與已知問題

以下是在 Xamarin 中開發延伸模組時可能會發生的限制和已知問題：

- Visual Studio for Mac 中目前沒有任何調試支援。 所有的調試作業都必須透過**NSLog**和**主控台**來完成。 如需詳細資訊，請參閱以下的秘訣一節。
- 延伸模組必須包含在主應用程式中，當執行一次與系統的註冊時，就會發生此情況。 然後必須在 [**系統偏好**設定] 的 [**延伸**模組] 區段中啟用。 
- 某些延伸模組損毀可能會使主機應用程式不穩定，並會造成奇怪的行為。 特別是，「搜尋者」和「**今天**」的「**通知中心**」區段可能會變成「卡住 **」，而且**會變成沒有回應。 這在 Xcode 的擴充專案中也有經驗，目前與 Xamarin 無關。 通常可以在系統記錄檔中看到這種情況（透過**主控台**，請參閱詳細資訊的秘訣）列印重複的錯誤訊息。 重新開機 macOS 會顯示修正此問題。

<a name="Tips" />

## <a name="tips"></a>祕訣

在 Xamarin. Mac 中使用擴充功能時，下列秘訣會很有説明：

- 因為 Xamarin 目前不支援調試延伸模組，所以調試經驗主要取決於執行和 `printf` like 語句。 不過，延伸模組會在沙箱進程中執行，因此 `Console.WriteLine` 不會像在其他 Xamarin. Mac 應用程式中一樣。 直接叫用[`NSLog`](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)會將調試訊息輸出至系統記錄檔。
- 任何未攔截的例外狀況都會損毀延伸模組進程，在**系統記錄**檔中只提供少量有用的資訊。 Wrapping troublesome code in a `try/catch` (Exception) block that `NSLog`’s before re-throwing may be useful.
- The **System Log** can be accessed from the **Console** app under **Applications** > **Utilities**:

    [![](extensions-images/extension02.png "The system log")](extensions-images/extension02.png#lightbox)
- As noted above, running the extension host application will register it with the system. Deleting the application bundle with unregister it. 
- If “stray” versions of an app's extensions are registered, use the following command to locate them (so they can be deleted): `plugin kit -mv`

<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Walkthrough and Sample App

Since the developer will create and work with Xamarin.Mac extensions in the same way as Xamarin.iOS extensions, please refer to our [Introduction to Extensions](~/ios/platform/extensions.md) documentation for more details.

An example Xamarin.Mac project containing small, working samples of each extension type can be found [here](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples).

<a name="Summary" />

## <a name="summary"></a>總結

This article has taken a quick look at working with extensions in a Xamarin.Mac version 2.10 (and greater) app.

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)
- [macOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) \(英文\)
