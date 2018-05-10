---
title: 我要如何收集 Visual Studio 處理序的目前呼叫堆疊？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>我要如何收集 Visual Studio 處理序的目前呼叫堆疊？

GUI 鎖定 （擱置、 凍結） 註冊 Visual Studio 中，重要的收集的診斷資訊時的 Visual Studio 處理序的所有執行緒的呼叫堆疊的集合。 若要儲存這項資訊對於無回應的 Visual Studio 執行個體，您可以使用 Visual Studio 的第二個執行個體：

1. 啟動 Visual Studio 的第二個執行個體 （新的視窗）。

2. 關閉任何開啟的方案在 Visual Studio 的新執行個體。

3. 選取 [偵錯] > [附加至處理序]。

  ![](vs-callstack-images/image1.png "選取 偵錯 > 附加至處理序")

4. 選取原始的無回應執行個體`devenv.exe`從清單中**可用的處理序**。

5. 選取**偵錯 > 全部中斷**。

  ![](vs-callstack-images/image2.png "選取 偵錯 > 全部中斷")

6. 選取**偵錯 > 儲存傾印**。

  ![](vs-callstack-images/image3.png "選取 偵錯 > 儲存傾印")

7. 變更**存檔類型**至**小型傾印 (\*.dmp)**。 這會產生較小的檔案比**小型傾印包含堆積**，而堆積無關通常會為了診斷凍結。

  ![](vs-callstack-images/image4.png "這會產生比小型傾印包含堆積，較小的檔案和堆積無關通常會為了診斷凍結")

8. 儲存傾印檔案。 如果提交線上的檔案，您可以壓縮以減少大小。
