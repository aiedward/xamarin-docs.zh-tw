---
title: 如何收集 Visual Studio 處理序的目前呼叫堆疊？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159122"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>如何收集 Visual Studio 處理序的目前呼叫堆疊？

GUI 鎖在 Visual Studio 中的 （當機，凍結），要收集的診斷資訊的一項重要時，Visual Studio 處理序的所有執行緒的呼叫堆疊的集合。 若要儲存這項資訊的 Visual Studio 停止回應的執行個體，您可以使用 Visual Studio 的第二個執行個體：

1. 啟動 Visual Studio 的第二個執行個體 （新視窗）。

2. 關閉任何開啟的方案在 Visual Studio 的新執行個體。

3. 選取 [偵錯] > [附加至處理序]。

  ![](vs-callstack-images/image1.png "選取 偵錯 > 附加至處理序")

4. 選取的原始無回應的執行個體`devenv.exe`從清單中的**可用的處理序**。

5. 選取 **偵錯 > 全部中斷**。

  ![](vs-callstack-images/image2.png "選取 偵錯 > 全部中斷")

6. 選取 **偵錯 > 儲存傾印**。

  ![](vs-callstack-images/image3.png "選取 偵錯 > 儲存傾印")

7. 變更**將儲存為類型**要**小型傾印 (\*.dmp)**。 這會產生較小的檔案比**包含堆積的小型傾印**，和堆積無關通常用於診斷凍結。

  ![](vs-callstack-images/image4.png "這會產生比小型傾印包含堆積，較小的檔案和堆積無關通常用於診斷凍結")

8. 儲存傾印檔案。 如果提交線上的檔案，您可以壓縮它以減少大小。
