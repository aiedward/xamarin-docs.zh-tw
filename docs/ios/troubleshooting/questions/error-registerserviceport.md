---
title: 含 RegisterServicePort 的 iOS 設計工具錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: b76de0f0dc8f1fd2124eac9cfe03b872c71095b1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292267"
---
# <a name="ios-designer-error-with-registerserviceport"></a>含 RegisterServicePort 的 iOS 設計工具錯誤

## <a name="sample-error"></a>範例錯誤
> AggregateException：發生一或多個錯誤---> SystemException：含 registerserviceport （.com. MTHosting. 2a0b1，.com. PowerManagement. control）：傳回的核心：-308 （-308）：（ipc/svrmig.mig）伺服器壞掉

## <a name="explanation"></a>說明
`RegisterServicePort` 的錯誤和類似的錯誤訊息，通常是電腦上的間諜軟體/惡意程式碼所發生的問題。 如需詳細資訊，請考慮關於[此錯誤報表的意見](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)，以及有關如何移除可能感染的[Apple 論壇討論](https://discussions.apple.com/thread/5596008)的連結。 

若要協助診斷問題，請開啟 macOS 應用程式**主控台**，並刪除 [**使用者診斷報告**] 區段中的每個檔案[https://screencast.com/t/y9i3NKcuMy](https://screencast.com/t/y9i3NKcuMy)。 然後啟動 Visual Studio for Mac，並嘗試使用設計工具。 如果在設計工具無法初始化之後，此區段中出現任何新的記錄檔，請儲存這些檔案供我們分析。  

請注意，要檢查的最重要事項是此檔案： 
> /usr/lib/libimckit.dylib

無論上述結果為何，如果該檔案存在，電腦上就會出現前述的間諜軟體/惡意程式碼問題。  

下列連結包含移除此間諜軟體/惡意程式碼的步驟： [https://www.thesafemac.com/arg-genieo/](https://www.thesafemac.com/arg-genieo/)  
