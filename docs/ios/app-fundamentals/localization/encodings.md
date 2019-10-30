---
title: Xamarin 中的國際化編碼
description: 本檔說明在 Xamarin 中的國際化編碼，討論可用的編碼方式，以及如何將它們新增至應用程式。
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 2d204aeac18fc08bf6eb2221b6f64ecbbded8121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009997"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Xamarin 中的國際化編碼

根據預設，所有編碼都不會包含在 Xamarin. iOS 類別庫中。

為了減少應用程式的大小，Xamarin 不會包含任何特定的編碼，而且您必須指示 mtouch 包含包含所需編碼支援的元件。

做法是在 Visual Studio for Mac 或 Visual Studio 的 [iOS 組建]/[高級] 窗格中選取額外的編碼方式：

 [![](encodings-images/00.png "Selecting the extra encodings")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Selecting the extra encodings")](encodings-images/00a.png#lightbox)

您可以選取下列其中一項：

- cjk：適用于 Chineese、日文和韓文
- 務必：阿拉伯文、希伯來文、土耳其文和 Latin5。
- 其他：斯拉夫文，波羅語，越南文，烏克蘭和泰文
- 罕見： EBCDIC 編碼和其他罕見字碼頁
- 西部：拉丁語言、復活節和西歐
- all

 <a name="cjk" />

## <a name="cjk"></a>cjk

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast" />

## <a name="mideast"></a>務必

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other" />

## <a name="other"></a>另一個

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare" />

## <a name="rare"></a>比較

- CP1026
- CP1047
- CP1140
- CP1141
- CP1142
- CP1143
- CP1144
- CP1145
- CP1146
- CP1147
- CP1148
- CP1149
- CP20273
- CP20277
- CP20278
- CP20280
- CP20284
- CP20285
- CP20290
- CP20297
- CP20420
- CP20424
- CP20871
- CP21025
- CP37
- CP500
- CP708
- CP852
- CP855
- CP857
- CP858
- CP862
- CP864
- CP866
- CP869
- CP870
- CP875

 <a name="west" />

## <a name="west"></a>west

- CP10000
- CP10079
- CP1250
- CP1252
- CP1253
- CP28592
- CP28593
- CP28597
- CP28605
- CP437
- CP850
- CP860
- CP861
- CP863
- CP865
