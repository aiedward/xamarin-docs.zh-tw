---
title: 為什麼不 Visual Studio 包括參考的程式庫專案中的我的組建？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: cb9b3689ab6a12d99f9694583cd0fd50a6f5c72c
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>為什麼不 Visual Studio 包括參考的程式庫專案中的我的組建

Visual Studio 會使用**Configuration Manager**來判斷哪一個方案中的專案會自動包含在指定的組建或部署組態。

與參考的程式庫專案會產生某些範本已參考組態中包含的程式庫但是，否則它就必須手動設定。

## <a name="how-to-use-the-configuration-manager"></a>如何使用 Configuration Manager

1. 開啟**建置 > 組態管理員**
2. 選取要自訂，例如組態**偵錯 | iPhone**
3. 選取您要加入專案的核取方塊。

> [!NOTE]
> 灰色方塊會自動處理，而應該不需要任何變更。

錄影畫面的步驟執行： [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
