---
title: iOS 背景處理技術
description: 本檔連結的指南會描述 iOS 中的各種背景處理技術：背景工作、背景傳送服務、背景提取和遠端通知。
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: a81d6a651ee980b444da19341a89206afa8c4375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010820"
---
# <a name="ios-backgrounding-techniques"></a>iOS 背景處理技術

在下列各節中，我們將探索下列 iOS 功能與現有的背景處理選項：

- 可執行檔[背景](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7)工作-當裝置處於喚醒狀態以進行其他處理時，請在機會區塊中執行背景工作，以保留電池壽命。
- [背景傳送服務](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers)-無論網路狀態或檔案大小，都能可靠地上傳和下載檔案。
- [背景提取](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch)-在系統決定的間隔期間，從背景重新整理應用程式。
- [遠端通知](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications)-使用推播通知，在使用者開啟應用程式之前，于背景觸發內容更新，並提供通知使用者或以無訊息方式更新的選項。
- **背景 UI 更新**-準備使用者的應用程式 UI，並從背景更新應用程式的快照集。
