---
title: 含 RegisterServicePort 的 iOS 設計工具錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fc4c143d6b5f7c211d24e6e3ed2ed3bb8d264410
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421966"
---
# <a name="ios-designer-error-with-registerserviceport"></a>含 RegisterServicePort 的 iOS 設計工具錯誤

## <a name="sample-error"></a>範例錯誤
> System.AggregateException:---> System.SystemException 時，發生一或多個錯誤：RegisterServicePort （com.xamarin.MTHosting.2a0b1、 com.apple.PowerManagement.control）：傳回的核心:-308 (-308): 終止 (ipc/mig) 伺服器

## <a name="explanation"></a>說明
錯誤`RegisterServicePort`和這類類似的錯誤訊息，上面通常是間諜軟體/惡意程式碼的電腦上的問題。 請考慮[此錯誤報告的註解](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)如需詳細資訊，並連結可供[Apple 論壇討論](https://discussions.apple.com/thread/5596008)如何移除可能感染。 

若要協助診斷問題，請開啟 macOS 應用程式**主控台**，並刪除每個檔案內**使用者診斷報告**一節[ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy)。 然後啟動 Visual Studio for Mac，然後再次嘗試使用設計工具。 如果設計工具無法初始化之後，任何新的記錄檔會出現在本節中，請儲存這些讓我們來分析。  

請注意最重要的是，若要檢查此檔案： 
> /usr/lib/libimckit.dylib

不論上述結果中，如果該檔案存在，上述的間諜軟體/惡意程式碼問題是出現在您的電腦上。  

下列連結已經移除間諜軟體/惡意軟體的步驟： [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

