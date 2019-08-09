---
title: 編譯錯誤:無法在產生的散佈位置中編碼位移 X
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876227"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>編譯錯誤:無法在產生的散佈位置中編碼位移 X

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

當最後一個二進位檔對原生工具鏈而言太大時, 建立32位架構 (例如 ARMv7) 時, 就會發生此問題。

若要解決此問題:

- 在 [專案選項] 的 [iOS 組建] 窗格中, 放置該架構的組建。
- 啟用連結或手動移除程式碼, 以減少最終可執行檔案大小
