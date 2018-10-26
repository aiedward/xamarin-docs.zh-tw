---
title: 為什麼我的 iOS 9 應用程式無法使用： System.Exception： 無法封送處理 OBJECTIVE-C 物件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: dfe1918d271946eb96d1f57d32c533a075f6d0bd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103357"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>為什麼我的 iOS 9 應用程式無法使用： System.Exception： 無法封送處理 OBJECTIVE-C 物件？

您可能會看到這種形式的錯誤：

> System.Exception： 無法封送處理 OBJECTIVE-C 物件...找不到這個物件的現有受管理的執行個體...

在 iOS 9 的 API 變更需要回呼的建構函式，呼叫 unmanaged 程式碼，為基礎的 API 現在預期它時使用。 您可以使用下面這一行來回呼的建構函式加入類別： 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>後續步驟

如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。 
