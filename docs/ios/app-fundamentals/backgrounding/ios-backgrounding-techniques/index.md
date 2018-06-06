---
title: iOS Backgrounding 技術
description: 這份文件連結到指南描述在 iOS 中的各種 backgrounding 技術： 背景工作、 背景傳送服務、 背景擷取和遠端的通知。
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ebf3c07a319a79994093f89f8e54f4cba7402533
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783751"
---
# <a name="ios-backgrounding-techniques"></a>iOS Backgrounding 技術

在下列章節中，我們將探討下列 iOS 功能以及現有 backgrounding 選項：

-  [隨機的背景工作](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7)-保留裝置醒著的其他處理程序時，隨機區塊中執行的背景工作的電池壽命。
-  [背景傳送服務](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers)-可靠地上傳和下載檔案，不論網路狀態或檔案大小。
-  [背景提取](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch)-重新整理應用程式與背景系統所決定的間隔。
-  [遠端通知](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications)-使用推播通知給使用者開啟應用程式，通知使用者，或以無訊息模式更新的選項之前，觸發程序在背景中的內容更新。
-  **背景 UI 更新**-準備應用程式 UI 的使用者，並更新應用程式的快照集，所有的背景。
