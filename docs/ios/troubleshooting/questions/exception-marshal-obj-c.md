---
title: 為什麼我的 iOS 9 應用程式失敗，併發生： system.exception：無法封送處理目標 C 物件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031101"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>為什麼我的 iOS 9 應用程式失敗，併發生： system.exception：無法封送處理目標 C 物件？

您可能會看到此表單的錯誤：

> Exception：無法封送處理目標-C 物件 。找不到此物件的現有受控實例 。

IOS 9 中的 API 變更要求在呼叫非受控碼時使用回呼的函式，因為基礎 API 現在會預期它。 使用下列程式程式碼將回呼函式新增至類別： 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>後續步驟

如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊. 
