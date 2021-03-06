---
title: 如何在 Xamarin Studio 中，為 iOS 專案設定 Mono 執行階段環境變數？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: 30585bede5569edfa50ab9f450bcb62e04c8a10d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938581"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>如何在 Xamarin Studio 中，為 iOS 專案設定 Mono 執行階段環境變數？

如果您需要為 Mono 設定任何執行時間環境變數，可以在 [專案選項] 中設定， **> 執行 > 一般**] 頁面。

注意：以這種方式設定的 SGen （MONO GC PARAMS）垃圾收集環境變數 \_ 只會 \_ 在從 Xamarin Studio 啟動時使用。 如果您從裝置啟動應用程式，將會忽略 Sgen 的設定。 

若要永久設定應用程式的環境變數，您必須將此新增為額外的 mtouch 引數（適用于所有相關的設定）：

```csharp
   --setenv=NAME=VALUE
```

若要查看可設定的環境變數，請參閱 Mono 手冊頁面： [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) 請參閱標題為：`ENVIRONMENT VARIABLES`

![設定專案的環境變數](xs-mono-runtime-images/environment-variables.jpg)
