---
title: iOS 中的背景處理簡介
description: 本檔說明 iOS 中的背景處理：應用程式狀態、應用程式生命週期方法和背景應用程式重新整理。
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 9fe508d5b0f8d15a26f02b110763cc8e3f4a2e25
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292137"
---
# <a name="introduction-to-backgrounding-in-ios"></a>iOS 中的背景處理簡介

iOS 會非常緊密地控制背景處理，並提供三種方法來執行它：

- **註冊背景工作**-如果應用程式需要完成重要的工作，它可以在應用程式移至背景時，要求 iOS 不要中斷工作。 例如，應用程式可能需要完成使用者的記錄，或完成下載大型檔案。
- **註冊為需要背景的應用程式**-應用程式可以註冊為特定類型的應用程式，其中具有已知的特定背景處理需求，例如*音訊*、 *VoIP* 、*外部附件*、 *Newsstand* 、和*位置*。 這些應用程式允許連續的背景處理許可權，只要它們正在執行已註冊應用程式類型的參數內的工作即可。
- **啟用背景更新**-應用程式可以透過*區域監視*或接聽*重大位置變更*來觸發背景更新。 從 iOS 7，應用程式也可以註冊，以使用*背景提取*或*遠端通知*來更新背景中的內容。


## <a name="application-states-and-application-delegate-methods"></a>應用程式狀態和應用程式委派方法

我們必須先瞭解背景處理如何影響 iOS 應用程式的生命週期，才深入探討在 iOS 中進行背景處理的程式碼。

IOS 應用程式生命週期是應用程式狀態和方法之間的集合，可在其間移動。 應用程式會根據使用者的行為和應用程式的背景處理需求，在狀態之間轉換。 下圖說明此動作：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "應用程式狀態和應用程式委派方法圖表")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **未**執行-應用程式尚未在裝置上啟動。
- 執行中 **/** 作用中-應用程式位於畫面上，並且會在前景中執行程式碼。
- **非**作用中-應用程式會因撥入電話、文字或其他中斷而中斷。
- **背景執行**-應用程式會移至背景，並繼續執行背景程式碼。
- 已**暫**止-如果應用程式沒有任何可在背景中執行的程式碼，或所有程式碼都已完成，則 OS 將會*暫停*應用程式。 暫止應用程式的進程會保持運作狀態，但應用程式無法在此狀態下執行任何程式碼。
- **返回未執行/終止（罕見）** -偶爾會終結應用程式的進程，而應用程式會回到 [*未*執行] 狀態。 這會發生在記憶體不足的情況下，或使用者手動終止應用程式時。


因為多工支援的引進，iOS 很少會終止閒置的應用程式，而是會在記憶體中保持*擱置*的進程。 讓應用程式的進程保持運作，可確保應用程式會在使用者下一次開啟時快速啟動。 這也表示應用程式可以從*暫停*狀態自由地移回*背景執行*狀態，而不需要在系統資源上進行繪製。 iOS 7 利用新的 Api 來運用這項功能，讓應用程式在裝置進入睡眠狀態時暫停背景工作、直接從背景更新內容，而不需要使用者互動等等。 我們將在[IOS 背景處理技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)中討論新的 api。

## <a name="application-lifecycle-methods"></a>應用程式生命週期方法

當應用程式變更狀態時，iOS 會透過`AppDelegate`類別中的事件方法通知應用程式：

- `OnActivated`-這是第一次啟動應用程式時，以及應用程式每次回到前景時所呼叫的。 這是在每次開啟應用程式時放置需要執行之程式碼的位置。
- `OnResignActivation`-如果使用者收到一則中斷，例如文字或電話，則會呼叫此方法，並暫時停用應用程式。 如果使用者接受通話，應用程式將會傳送至背景。
- `DidEnterBackground`-當應用程式進入背景執行狀態時呼叫，這個方法會提供大約5秒的應用程式來準備可能的終止。 使用此時間儲存使用者資料和工作，並從畫面中移除機密資訊。
- `WillEnterForeground`-當使用者返回背景執行或暫止的應用程式，並將它啟動至前景時`WillEnterForeground` ，會呼叫。 這是讓應用程式解除凍結在期間`DidEnterBackground`儲存的任何狀態來準備前景的時間。  `OnActivated`將在這個方法完成之後立即呼叫。
- `WillTerminate`-應用程式已關閉，而且其進程已終結。 只有在裝置上無法使用多工或作業系統版本、記憶體不足，或使用者手動終止背景執行應用程式時，才會呼叫此方法。 請注意，已終止的已暫止應用`WillTerminate`程式將不會呼叫。


下圖說明應用程式狀態和生命週期方法如何彼此配合：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此圖說明應用程式狀態和生命週期方法如何彼此配合")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>IOS 中的背景處理使用者控制項

iOS 7 引進了數種功能，讓使用者能夠更充分掌控應用程式的背景執行狀態。 應用程式切換器和背景應用程式重新整理設定都會影響應用程式生命週期。

### <a name="app-switcher"></a>應用程式切換器

應用程式切換器是 iOS 7 中所引進的重要控制功能。 其啟動方式是按兩下 [**首頁**] 按鈕，並顯示其進程正在運作的應用程式：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用應用程式切換器在應用程式之間移動")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用者可以使用應用程式切換器，逐一查看所有背景執行和已暫止應用程式的快照集。 點擊應用程式會將它啟動至前景。 「向上輕掃」會從背景移除應用程式，並終止其進程。 在下一節中，我們將深入探討[IOS 應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)中的應用程式切換器。

> [!IMPORTANT]
> 應用程式切換器不會顯示背景執行與已擱置應用程式之間的差異。



### <a name="background-app-refresh-settings"></a>背景應用程式重新整理設定

iOS 7 藉由允許使用者選擇不背景處理為[已註冊背景處理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)的應用程式，來提高使用者對應用程式生命週期的控制權。 *這無法防止應用程式執行背景*工作。

使用者可以變更此設定，方法是流覽至**設定 > 一般 > 背景應用**程式重新整理，然後編輯所選應用程式的背景處理許可權。 如果 [背景應用程式重新整理] 設定為 [關閉]，則會在進入背景時立即擱置應用程式，並防止執行任何背景處理：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "背景應用程式重新整理設定")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

開發人員可以使用`BackgroundRefreshStatus` API 檢查背景重新整理應用程式狀態。 如需範例，請參閱[檢查背景重新整理設定配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting)。

我們已涵蓋 iOS 應用程式生命週期的基本概念，以及用來控制應用程式生命週期的功能。 接下來，讓我們來瞭解 iOS 應用程式生命週期的實際運作。

