---
title: 在 iOS 中背景簡介
description: 本文件說明 ios 中的背景： 應用程式狀態、 應用程式生命週期方法，以及背景應用程式的重新整理。
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 804a99817f664989bbac67a4c662357f4ee628c5
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242273"
---
# <a name="introduction-to-backgrounding-in-ios"></a>在 iOS 中背景簡介

iOS 背景處理非常緊密，及提供三種實作它的方法：

-  **註冊背景工作**-如果應用程式必須完成一項重要工作，它可以要求 iOS 不中斷工作應用程式移到背景時。 例如，應用程式可能需要完成登入使用者，或下載大型檔案完成。
-  **註冊為背景必要應用程式**-應用程式就可以註冊為特定類型的應用程式有已知背景的特定需求，例如*音訊*， *VoIP* ， *外部配件*， *Newsstand* ，並*位置*。 連續的背景處理權限，只要它們執行的已註冊的應用程式類型的參數內的工作時，會允許這些應用程式。
-  **啟用背景更新**-應用程式可以觸發背景更新*區域監視*或接聽*位置的重大變更*。 IOS 7、 從應用程式也可以註冊更新在背景中使用的內容*背景提取*或是*遠端通知*。


## <a name="application-states-and-application-delegate-methods"></a>應用程式狀態和應用程式的委派方法

我們會探討背景處理在 iOS 中的程式碼之前，我們需要了解如何在背景會影響的 iOS 應用程式生命週期。

IOS 應用程式生命週期是應用程式狀態和其間移動方法的集合。 應用程式會根據使用者的行為和 backgrounding 需求的應用程式的狀態之間轉換。 下圖說明移動：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "應用程式狀態和應用程式的委派方法的圖表")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **未執行**-應用程式不尚未啟動的裝置上。
-  **執行/主動**-應用程式會在畫面上，並在前景中執行程式碼。
-  **非使用中**-應用程式中斷的連入的通話、 文字或其他中斷。
-  **背景**-應用程式進入背景，並繼續執行背景程式碼。
-  **Suspended** -如果應用程式並沒有任何程式碼，以在背景中執行，或如果已完成所有的程式碼，應用程式能*Suspended*由 OS。 暫停應用程式的程序會保持運作，但應用程式無法在此狀態下執行任何程式碼。
-  **傳回以不執行/終止 (Rare)** -偶而，損毀應用程式的程序，和應用程式返回*未執行*狀態。 此動作會在記憶體不足的情況下，或如果使用者手動終止應用程式。


IOS 很少會終止閒置的應用程式，和而是會保留他們的程序的多工支援自從*Suspended*在記憶體中。 保持運作的應用程式的程序可確保應用程式會快速地啟動下一次使用者開啟它。 這也表示應用程式可以自由移動，從*Suspended*回狀態*Backgrounded*不繪製系統資源的狀態。 iOS 7 利用新的 Api，可讓裝置進入睡眠狀態，直接從背景無須使用者互動，以及其他的更新內容時，暫停背景工作的應用程式使用這項功能。 我們將討論中的新 Api [iOS 背景技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)。

## <a name="application-lifecycle-methods"></a>應用程式生命週期方法

當應用程式變更狀態時，iOS 會通知應用程式透過事件中的方法`AppDelegate`類別：

-  `OnActivated` -這稱為第一次啟動應用程式時，以及每次應用程式回到前景。 這是放置執行每次開啟應用程式時所需的程式碼的位置。
-  `OnResignActivation` -如果使用者收到中斷，例如文字或撥打電話時，會呼叫這個方法，應用程式暫時停用。 使用者應該接受撥打電話，應用程式會傳送到背景工作。
-  `DidEnterBackground` -應用程式進入 backgrounded 的狀態時呼叫，這個方法可讓應用程式約五秒鐘準備可能終止。 利用這段時間，以儲存使用者資料和工作，並從螢幕中移除機密資訊。
-  `WillEnterForeground` -當使用者傳回至背景或已暫停的應用程式，並放置到前景，將其啟動`WillEnterForeground`呼叫。 這正是準備應用程式的解除凍結期間儲存任何狀態取得前景`DidEnterBackground`。  `OnActivated` 這個方法完成之後，立即就會呼叫。
-  `WillTerminate` -應用程式也會關閉，而且其程序損毀。 只有如果多工並不適用於裝置或作業系統版本中，如果記憶體不足，或是使用者手動結束 backgrounded 應用程式，會呼叫這個方法。 請注意，終止的暫止應用程式將不會呼叫`WillTerminate`。


下圖說明應用程式的狀態如何，以及存留週期方法結合在一起：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此圖說明應用程式的狀態如何，以及存留週期方法結合在一起")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>在 iOS 中背景的使用者控制項

iOS 7 引進了數個功能，讓使用者更充分掌控應用程式的 backgrounded 狀態。 應用程式切換器和背景重新整理應用程式的設定會影響應用程式生命週期。

### <a name="app-switcher"></a>應用程式切換器

應用程式切換器是在 iOS 7 中引進了重要的控制項功能。 啟動雙精度浮點數點選**首頁**按鈕，並顯示其處理程序會保持運作的應用程式：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用應用程式切換器的應用程式之間移動")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用應用程式切換器，使用者可以捲動以查看所有的背景和暫停應用程式的快照集。 點選應用程式會將它放置到前景中啟動。 向上撥動，是在背景中，正在終止其處理序中移除應用程式。 我們要仔細看看應用程式切換器中[iOS 應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)下一節。

> [!IMPORTANT]
> 應用程式切換器不會顯示背景和暫停應用程式之間的差異。



### <a name="background-app-refresh-settings"></a>背景應用程式重新整理設定

iOS 7 增加使用者的應用程式生命週期的控制權讓使用者能夠選擇退出應用程式的背景[註冊為背景處理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)。 *這樣做無法防止應用程式無法執行背景工作*。

使用者可以變更此設定，瀏覽至<span class="uiitem">設定 > 一般 > 背景應用程式重新整理</span>和編輯選取的應用程式的 backgrounding 權限。 如果背景重新整理應用程式設定為 off，就會立即遭到暫停時進入背景，並封鎖任何背景處理應用程式：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "背景應用程式重新整理設定")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

開發人員可以檢查背景重新整理應用程式狀態與`BackgroundRefreshStatus`API。 如需範例，請參閱[檢查背景重新整理設定配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting)。

我們所討論 iOS 應用程式生命週期，來控制應用程式生命週期功能基本的概念。 接下來，我們來看看作用中的 iOS 應用程式生命週期。

