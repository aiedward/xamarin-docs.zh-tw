---
title: 為什麼我 iOS 9 的應用程式失敗與： System.Exception： 無法封送處理 OBJECTIVE-C 物件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f7382ac963249a3f3646a917d8700e3a12873ec9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>為什麼我 iOS 9 的應用程式失敗與： System.Exception： 無法封送處理 OBJECTIVE-C 物件？

您可能會看到這種形式的錯誤：

> System.Exception： 無法封送處理 OBJECTIVE-C 物件...找不到此物件的現有受管理的執行個體...

IOS 9 中的應用程式開發介面變更要求為基礎的 API，以立即呼叫 unmanaged 程式碼，需要時使用的回呼建構函式。 您可以使用下列行回呼建構函式加入類別： 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>後續步驟

如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。 
