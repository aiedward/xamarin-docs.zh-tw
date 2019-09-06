---
title: 如何收集 Visual Studio 處理序的目前呼叫堆疊？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f07bc4437293bdc49e4811c0104fd7245ccf9738
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282948"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>如何收集 Visual Studio 處理序的目前呼叫堆疊？

當 GUI 在 Visual Studio 中鎖定（停止回應、凍結）時，要收集的一項重要診斷資訊是來自 Visual Studio 進程所有線程的一組呼叫堆疊。 若要為 Visual Studio 的無回應實例儲存這項資訊，您可以使用 Visual Studio 的第二個實例：

1. 啟動 Visual Studio 的第二個實例（新的視窗）。

2. 在 Visual Studio 的新實例中關閉任何已開啟的方案。

3. 選取 [偵錯] > [附加至處理序]。

   ![](vs-callstack-images/image1.png "選取 [Debug > 附加至進程]")

4. 從**可用的進程**清單中`devenv.exe` ，選取原始的無回應實例。

5. 選取 [ **Debug] > [全部中斷**]。

   ![](vs-callstack-images/image2.png "選取 [Debug > 全部中斷]")

6. 選取 [ **Debug > 將傾印儲存為**]。

   ![](vs-callstack-images/image3.png "選取 [Debug > 將傾印儲存為]")

7. 將 [**另存**新檔類型] 變更為小型傾印 **\*（dmp）** 。 這會產生比**使用堆積的小型**傾印更小的檔案，而且堆積通常與診斷凍結無關。

   ![](vs-callstack-images/image4.png "這會產生比使用堆積的小型傾印更小的檔案，而且堆積通常與診斷凍結無關")

8. 儲存傾印檔案。 如果線上提交盤案，您可以將它壓縮以減少大小。
