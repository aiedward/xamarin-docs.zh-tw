---
title: iOS 與 RegisterServicePort 的設計工具時發生錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62d4a06c62bffb23566f4f59f42a0c980f417c45
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-designer-error-with-registerserviceport"></a>iOS 與 RegisterServicePort 的設計工具時發生錯誤

## <a name="sample-error"></a>範例錯誤
> System.AggregateException:---> System.SystemException 發生一個或多個錯誤： RegisterServicePort （com.xamarin.MTHosting.2a0b1、 com.apple.PowerManagement.control）： 傳回的核心:-308 (-308): 已中斷 (ipc/mig) 伺服器

## <a name="explanation"></a>說明
錯誤與`RegisterServicePort`和類似的錯誤訊息，上面通常是間諜軟體/惡意程式碼的電腦上的問題。 請考慮[這個 bug 報表上的註解](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)如需詳細資訊，以及連結至[Apple 論壇討論](https://discussions.apple.com/thread/5596008)了解如何移除可能感染。 

若要協助診斷問題，請開啟 macOS 應用程式**主控台**並刪除每個檔案內部**使用者診斷報告**區段[ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy)。 然後啟動 Visual Studio for Mac，並嘗試使用設計工具。 如果設計工具無法初始化之後，任何新的記錄檔會出現在這個區段，請儲存這些我們分析。  

請注意若要檢查的最重要的項目是這個檔案： 
> /usr/lib/libimckit.dylib

不論上述結果中，如果該檔案已經存在，上述間諜軟體/惡意程式碼問題通常是存在於您的電腦上。  

下列連結有要移除間諜軟體/惡意軟體的步驟： [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

