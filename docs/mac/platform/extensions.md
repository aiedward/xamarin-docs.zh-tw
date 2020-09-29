---
title: Xamarin.Mac 延伸模組支援
description: 本檔說明 Xamarin 支援 Finder、共用和 Today 延伸模組的功能。 它會檢查限制和已知問題、逐步解說和範例應用程式的連結，以及提供使用擴充功能的秘訣。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: abb14f2074a2f519037ef2a9e85f2bf66613b836
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436796"
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 延伸模組支援

在 Xamarin 中，已為多個 macOS 擴充點新增了 Mac 2.10 支援：

- 儀
- 共用
- 今天

<a name="Limitations-and-Known-Issues"></a>

## <a name="limitations-and-known-issues"></a>限制與已知問題

以下是在 Xamarin 中開發延伸模組時可能會發生的限制和已知問題：

- Visual Studio for Mac 目前沒有任何偵錯工具支援。 所有的調試都必須透過 **NSLog** 和 **主控台**完成。 如需詳細資訊，請參閱下面的秘訣一節。
- 延伸模組必須包含在主機應用程式中，當執行一次時，即會向系統註冊。 然後必須在**系統喜好**設定的**擴充**區段中啟用。 
- 某些擴充功能損毀可能會使主機應用程式不穩定，並導致奇怪的行為。 尤其是，「**通知中心**」的 [ **Finder** ] 和 [ **Today** ] 區段可能會變成「已卡住」，而且會變成沒有回應。 Xcode 中的擴充專案也有可能發生這種情況，目前與 Xamarin 無關。 通常，您可以透過 **主控台**在系統記錄檔 (中看到這種情況，請參閱詳細資料) 列印重複錯誤訊息的秘訣。 重新開機 macOS 會出現以修正此問題。

<a name="Tips"></a>

## <a name="tips"></a>提示

在 Xamarin 中使用延伸模組時，下列秘訣可能很有説明：

- 因為 Xamarin 目前不支援調試延伸模組，所以調試經驗主要取決於執行和 `printf` 類似語句。 不過，擴充功能會在沙箱進程中執行，因此 `Console.WriteLine` 不會像在其他 Xamarin 應用程式中一樣運作。 [ `NSLog` 直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)叫用會將調試訊息輸出到系統記錄檔。
- 任何未攔截的例外狀況都會使擴充程式損毀，並在 **系統記錄**檔中只提供少量有用的資訊。 將有問題的程式碼包裝在 `try/catch` (例外狀況中，) 區塊在重新擲回 `NSLog` 之前都可能很有用。
- 您可以從**應用**程式公用程式下的**主控台**應用程式存取**系統記錄**  >  ** **：

    [![系統記錄檔](extensions-images/extension02.png)](extensions-images/extension02.png#lightbox)
- 如上所述，執行擴充功能主機應用程式將會向系統註冊。 刪除應用程式套件組合並將其取消註冊。 
- 如果已註冊應用程式擴充功能的「偏離」版本，請使用下列命令找出它們 (，以便) 刪除這些專案： `plugin kit -mv`

<a name="Walkthrough-and-Sample-App"></a>

## <a name="walkthrough-and-sample-app"></a>逐步解說和範例應用程式

由於開發人員會以和 Xamarin 副檔名相同的方式來建立和使用 Xamarin 延伸模組，如需詳細資料，請參閱我們的擴充功能檔 [簡介](~/ios/platform/extensions.md) 。

您可以在 [這裡](/samples/xamarin/mac-samples/extensionsamples)找到範例 Xamarin 專案，其中包含每個延伸模組類型的小型、可運作範例。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文將快速瞭解如何使用 Xamarin 2.10 版中的擴充功能 (以及更高的) 應用程式。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](/samples/xamarin/mac-samples/extensionsamples)
- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)