---
title: 逐步解說：使用 Apple 的 Instrument 工具
description: 本文會描述如何使用 Apple 的 Instruments 工具，診斷透過 Xamarin 建置的 iOS 應用程式記憶體問題。 文中會示範如何啟動 Instruments、拍攝堆積快照、分析記憶體成長等等。
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f06af6a2121fc037f5c3f496ee8bd6b91e2969e6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656739"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>逐步解說：使用 Apple 的 Instrument 工具

_本文逐步解說如何使用 Apple 的 Instruments 工具，診斷透過 Xamarin 建置的 iOS 應用程式記憶體問題。文中將示範如何啟動 Instruments、拍攝堆積快照、分析記憶體成長。也會示範如何使用 Instruments 顯示和鎖定造成記憶體問題的確切程式碼行數。_

此頁面會示範如何使用 **Xcode 的 Instruments 工具**來診斷 iOS 應用程式中的記憶體問題。
首先，下載 [MemoryDemo 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)，並在 Visual Studio for Mac 中開啟 **before** 方案。

## <a name="diagnosing-the-memory-issues"></a>診斷記憶體問題

1. 在 Visual Studio for Mac 中，從 [工具] > [啟動 Instruments]  功能表項目啟動 **Instruments**。
2. 選擇 [執行] > [上傳至裝置]  功能表項目，將應用程式上傳至裝置。
3. 選擇 [Allocations]  範本 (具有白色方塊的橘色圖示)

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "選擇 [Allocations] 範本")

4. 在視窗頂端的 [Choose a profiling template for]  \(針對下列項目選擇分析範本\) 清單中，選取 **Memory Demo** 應用程式。 首先在 iOS 裝置上按一下，以展開顯示已安裝應用程式的功能表。

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "選取 Memory Demo 應用程式")

5. 按下 [Choose]  \(選擇\) 按鈕 (視窗右下方) 來啟動 **Instruments**。 ThiJs 範本會在上方窗格中顯示兩個項目：[配置] 和 [VM Tracker] \(VM 追蹤器\)。

6. 按下 Instruments 中的 [Record] \(錄製\)  按鈕 (左上方的紅色圓圈)，這將會啟動應用程式。

7. 選取上方窗格中的 [VM Tracker] \(VM 追蹤器\)  列 (由於應用程式正在執行，因此會包含兩個區段：[已變更] 和 [Resident Size] \(常駐大小\))。 在 [Inspector] \(檢查\)  窗格中，選擇 [Show Display Settings] \(顯示顯示設定\)  選項 (齒輪圖示)，然後勾選 [Automatic Snapshotting] \(自動建立快照\)  核取方塊，如此螢幕擷取畫面右下方所示：

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "選擇 [Show Display Settings] \(顯示顯示設定\) 選項齒輪圖示，然後勾選 [Automatic Snapshotting] \(自動建立快照\) 核取方塊")

8. 選取上方窗格中的 [Allocations] \(配置\)  列 (既然應用程式正在執行，它將會顯示 [All Heap and Anonymous VM] \(所有堆積和匿名 VM\)  )
9. 在 [Inspector] \(檢查\)  窗格中，選擇 [Show Display Settings] \(顯示顯示設定\)  選項 (齒輪圖示)，然後按一下 [Mark Generation] \(標示世代\)  按鈕來建立基準。 小型紅色旗標將會出現在視窗頂端的時間軸上
10. 捲動應用程式，然後再次選取 [Mark Generation] \(標示世代\)  (重複幾次)
11. 按一下 [Stop] \(停止\)  按鈕。
12. 展開具有最大 [Growth] \(成長\)  的 [Generation] \(世代\)  節點，並依 [Growth] \(成長\)  排序 (遞減)。
13. 將 [Inspector] \(檢查\)  窗格變更為 [Show Extended Detail] \(顯示延伸的詳細資料\)  ("E")，如此會顯示 [Stack Trace] \(堆疊追蹤\)  。

14. 請注意， **&lt;non-object>** 節點會顯示過多的記憶體成長。 按一下此節點旁邊的箭號以查看更多詳細資料：在堆疊追蹤中按一下滑鼠右鍵，將 [Source Location] \(來源位置\)  新增至窗格：

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "將 [Source Location] \(來源位置\) 新增至窗格")

15. 依 [Size] \(大小\)  排序，並顯示 [Extended Detail] \(延伸的詳細資料\)  檢視：

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "依 [Size] \(大小\) 排序，並顯示 [Extended Detail] \(延伸的詳細資料\) 檢視")

16. 按一下呼叫堆疊中所需的項目，以查看相關的程式碼：

    ![](walkthrough-apples-instrument-images/05-related-code.png "檢視相關的程式碼")

在此案例中，將會建立新的影像，並儲存在每個儲存格的集合中，但不會重複使用現有的集合檢視儲存格。

## <a name="resolving-the-memory-issues"></a>解決記憶體問題

您可以透過 Instruments 解決這些問題，並重新執行應用程式。

透過在類別層級宣告單一執行個體，可以重複使用影像，而且可以從現有的集區重複使用儲存格物件，而不是每次都建立儲存格物件，如下所示：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Dequeue a cell from the reuse pool
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);

    // Reuse the image declared at the class level
    imageCell.ImageView.Image = image;

    return imageCell;
}
```

現在，當應用程式執行時，就會大幅降低記憶體使用量，層代間的**成長**現在會以 Kib (KB) (而不是 MiB (MB)) 測量，因為這是在修正程式碼之前：

![](walkthrough-apples-instrument-images/06-reduced-memory.png "顯示應用程式記憶體使用量")

在 Visual Studio for Mac 的 **after** 方案中，會在 [MemoryDemo 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)內提供改良的程式碼。

有關 [Xamarin.iOS 記憶體回收](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)的這個社群部落格在處理 Xamarin.iOS 記憶體問題方面，是一個實用的參考。

## <a name="summary"></a>總結

本文示範如何使用 Instruments 來診斷記憶體問題。
它說明如何從 Visual Studio for Mac 內啟動 Instruments、載入記憶體配置範本，並逐步使用快照鎖定記憶體問題。
最後，應用程式會經過重新檢查以確認問題已獲得修正。

## <a name="related-links"></a>相關連結

- [MemoryDemo 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)
- [Xamarin.iOS 記憶體回收 (部落格文章)](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
