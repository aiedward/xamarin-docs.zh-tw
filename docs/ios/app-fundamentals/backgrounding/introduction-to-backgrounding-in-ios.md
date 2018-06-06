---
title: 在 iOS 中 Backgrounding 簡介
description: 本文件說明在 iOS 中 backgrounding： 應用程式狀態、 應用程式生命週期方法，以及背景應用程式重新整理。
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: c8084d8e218ba8e3468529795aaa5fd4eae30947
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783635"
---
# <a name="introduction-to-backgrounding-in-ios"></a>在 iOS 中 Backgrounding 簡介

iOS 規定背景非常緊密地處理，並提供三種實作該類別的方法：

-  **登錄背景工作**-如果應用程式需要完成的重要工作，它可以要求 iOS 不中斷工作，當應用程式移到背景。 例如，應用程式可能需要完成登入使用者，或完成下載大型檔案。
-  **註冊為背景必要應用程式**-應用程式可以如註冊為特定類型的應用程式具有已知 backgrounding 的特定需求，*音訊*， *VoIP* ， *外部裝飾*， *Newsstand* ，和*位置*。 這些應用程式可以在連續的背景處理權限，只要它們執行的工作中註冊的應用程式類型的參數。
-  **啟用背景更新**-應用程式可以觸發背景更新*區域監視*或接聽*位置的重大變更*。 從 iOS 7，開始應用程式也可以註冊更新內容，在背景中使用*背景擷取*或*遠端通知*。


## <a name="application-states-and-application-delegate-methods"></a>應用程式狀態和應用程式的委派方法

我們深入了解的背景處理在 iOS 中的程式碼之前，我們必須了解如何 backgrounding 影響的 iOS 應用程式生命週期。

IOS 應用程式生命週期是應用程式狀態及兩者之間移動的方法的集合。 應用程式之間的轉換取決於使用者的行為以及 backgrounding 需求的應用程式的狀態。 下圖說明移動：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "應用程式狀態和應用程式的委派方法的圖表")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **未執行**-應用程式不尚未已啟動的裝置上。
-  **執行/主動**-應用程式是在畫面上，並在前景執行的程式碼。
-  **非作用中**-連入的通話、 文字或其他中斷中斷應用程式。
-  **Backgrounded** -應用程式將移至背景，並繼續執行背景程式碼。
-  **Suspended** -如果應用程式沒有在背景中執行的任何程式碼，或如果所有的程式碼已完成，應用程式能*Suspended*作業系統。 暫停應用程式的程序就會保持運作，但應用程式無法在此狀態中執行的任何程式碼。
-  **傳回以不執行/終止 (Rare)** -有時候，終結應用程式的程序，並在應用程式回到*未執行*狀態。 此動作會在記憶體不足的情況下，或如果使用者以手動方式終止應用程式。


自多工作業支援引進，iOS 很少會終止閒置的應用程式，並改為保留其程序*Suspended*在記憶體中。 保持應用程式的程序可確保應用程式快速啟動的下次使用者開啟它。 這也表示應用程式可以自由地從移動*Suspended*狀態回*Backgrounded*不繪製系統資源的狀態。 iOS 7 利用新的 Api，可讓裝置進入睡眠，直接從沒有使用者互動，以及其他背景更新內容時，暫停背景工作的應用程式使用這項功能。 我們將討論中的新 Api [iOS Backgrounding 技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)。

## <a name="application-lifecycle-methods"></a>應用程式生命週期方法

當應用程式變更狀態時，iOS 會通知應用程式透過事件方法中`AppDelegate`類別：

-  `OnActivated` -這會呼叫第一次啟動應用程式時，以及每次應用程式恢復到前景。 這是放置執行每次開啟應用程式時所需的程式碼的位置。
-  `OnResignActivation` -如果使用者會收到中斷，例如文字或電話，會呼叫這個方法，應用程式暫時停用狀態。 使用者應該接受來電，將背景傳送應用程式。
-  `DidEnterBackground` -應用程式進入 backgrounded 的狀態時呼叫，這個方法可讓應用程式約五秒鐘做好可能終止。 使用此時間以儲存使用者資料和工作，並從螢幕中移除機密資訊。
-  `WillEnterForeground` -當使用者傳回 backgrounded 或已暫停的應用程式，並啟動到前景，`WillEnterForeground`取得呼叫。 若要準備應用程式的過程中儲存任何狀態重新水合已取得前景次`DidEnterBackground`。  `OnActivated` 將這個方法完成之後立即呼叫。
-  `WillTerminate` -應用程式關閉時，而且其處理序已損毀。 只有如果多工並不適用於裝置或作業系統版本中，如果記憶體不足，或是使用者手動終止 backgrounded 應用程式，會呼叫這個方法。 請注意，取得已經終止已暫停應用程式不會呼叫`WillTerminate`。


下圖說明如何在應用程式狀態和存留週期方法搭配：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此圖說明如何在應用程式狀態和存留週期方法搭配")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>在 iOS 中 Backgrounding 的使用者控制項

iOS 7 導入了幾種功能可以讓使用者更充分掌控應用程式的 backgrounded 狀態。 應用程式切換器和背景重新整理應用程式的設定會影響應用程式生命週期。

### <a name="app-switcher"></a>應用程式切換器

應用程式切換器是 iOS 7 中引進的重要控制項功能。 啟動雙點選**首頁**按鈕，並顯示其處理程序會保持運作的應用程式：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用應用程式切換器的應用程式之間移動")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用應用程式切換器，使用者可以捲動瀏覽所有 backgrounded 和暫停的應用程式的快照集。 點選應用程式啟動到前景。 向上撥動，是在背景中，正在終止其處理序中移除應用程式。 我們要探討中應用程式切換器[iOS 應用程式生命週期的示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)下一節。

> [!IMPORTANT]
> 應用程式切換器不會顯示 backgrounded 和暫停的應用程式之間的差異。



### <a name="background-app-refresh-settings"></a>背景應用程式重新整理設定

iOS 7 增加使用者應用程式生命週期的控制權，讓使用者能夠選擇退出應用程式的 backgrounding[註冊用於背景處理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)。 *這樣做無法防止應用程式執行的背景工作*。

使用者可以變更此設定，請瀏覽至<span class="uiitem">設定 > 一般 > 背景應用程式重新整理</span>和編輯選取的應用程式的 backgrounding 權限。 如果設為 off 的背景重新整理應用程式，會立即遭到暫停時輸入背景，並封鎖任何背景處理應用程式：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "背景應用程式重新整理設定")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

開發人員可以檢查背景重新整理應用程式狀態與`BackgroundRefreshStatus`應用程式開發介面。 如需範例，請參閱[檢查背景重新整理設定配方](https://developer.xamarin.com/recipes/ios/multitasking/check_background_refresh_setting/)。

我們已涵蓋的 iOS 應用程式生命週期和控制應用程式生命週期的功能的基本概念。 接下來，我們來看看 iOS 應用程式生命週期中的動作。

