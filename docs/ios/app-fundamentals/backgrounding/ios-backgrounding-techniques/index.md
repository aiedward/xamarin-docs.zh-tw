---
title: iOS 背景處理技術
description: 這份文件連結至說明各種 backgrounding 的技巧，在 iOS 中的輔助線： 背景工作、 背景傳送服務、 背景擷取和遠端通知。
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 00cca0c75cc79858f6edda5d6fb954611d81161b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169602"
---
# <a name="ios-backgrounding-techniques"></a>iOS 背景處理技術

在下列章節中，我們將探討下列的 iOS 功能，以及現有 backgrounding 選項：

-  [隨機的背景工作](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7)-保留裝置醒著的其他處理程序時，請在隨機區塊 （chunk） 中執行背景工作的電池壽命。
-  [背景傳送服務](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers)-可靠地將上傳和下載檔案，無論網路狀態或檔案大小。
-  [背景擷取](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch)-重新整理背景應用程式在系統決定的時間間隔。
-  [遠端通知](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications)-使用推播通知來觸發在背景中的內容更新之前在使用者開啟應用程式時，通知使用者，或以無訊息模式更新的選項。
-  **背景的 UI 更新**-準備應用程式 UI 的使用者，並更新應用程式的快照集，所有的背景。
