---
title: Xamarin 中的國際化編碼
description: 本檔說明 Xamarin 中的國際化編碼方式，討論可用的編碼方式，以及如何將它們新增至應用程式。
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: b832bdd25b8449ce365f87e145812ec3db27f393
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628744"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Xamarin 中的國際化編碼

依預設，並非所有編碼都包含在 Xamarin 類別庫中。

為了減少應用程式的大小，Xamarin 不包含任何特定的編碼方式，而且您必須指示 mtouch 包含包含所需編碼支援的元件。

若要完成此動作，請在 Visual Studio for Mac 或 Visual Studio 的 [iOS 組建/Advanced] 窗格中選取額外的編碼：

 [![螢幕擷取畫面顯示在 Visual Studio for Mac 中選取額外的編碼方式。](encodings-images/00.png)](encodings-images/00.png#lightbox)

 [![螢幕擷取畫面顯示在 Visual Studio 中選取額外的編碼方式。](encodings-images/00a.png)](encodings-images/00a.png#lightbox)

您可以選取下列其中一項：

- cjk：適用于 Chineese、日文和韓文
- mideast：阿拉伯文、希伯來文、土耳其文和 Latin5。
- 其他：斯拉夫文、波羅的海文、越南文、烏克蘭文和泰文
- 罕見： EBCDIC 編碼和其他罕見的字碼頁
- 西歐：拉丁語言、復活節和西歐
- all

 <a name="cjk"></a>

## <a name="cjk"></a>cjk

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast"></a>

## <a name="mideast"></a>中東

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other"></a>

## <a name="other"></a>其他

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare"></a>

## <a name="rare"></a>罕見

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

 <a name="west"></a>

## <a name="west"></a>西部

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
