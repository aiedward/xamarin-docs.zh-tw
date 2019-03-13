---
title: 在 Xamarin.iOS 中的國際化編碼
description: 本文件說明 Xamarin.iOS，討論可用的編碼，以及如何將它們新增至應用程式中的國際化編碼。
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: db24c8677b0a3099193132575e92bc43a4c31dea
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675558"
---
# <a name="internationalization-encodings-in-xamarinios"></a>在 Xamarin.iOS 中的國際化編碼

並非所有的編碼預設會包含 Xamarin.iOS 類別庫中。

若要減少應用程式的大小，Xamarin.iOS 不包含任何特定的編碼方式，而且您必須指示 mtouch 以包含組件，其中包含支援，您需要的編碼方式。

這是藉由 for Mac 或 Visual Studio，然後從 Visual Studio 中的 [iOS 組建] / [進階] 窗格中選取額外的編碼：

 [![](encodings-images/00.png "選取額外的編碼方式")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "選取額外的編碼方式")](encodings-images/00a.png#lightbox)

您可以選取其中一個：

-  中日韓： 的中文、 日文和韓文
-  mideast： 阿拉伯文、 希伯來文、 土耳其文和 Latin5。
-  其他： 斯拉夫文、 波羅的海文、 越南文、 烏克蘭文和泰文
-  罕見： EBCDIC 編碼方式和其他少數的字碼頁
-  西部： 拉丁語言時，伊斯特和西歐
-  全部


 <a name="cjk" />


## <a name="cjk"></a>中日韓

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>mideast

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>另一個

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>罕見

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>西部

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

