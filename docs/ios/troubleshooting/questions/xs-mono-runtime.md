---
title: 如何在 Xamarin Studio 中，為 iOS 專案設定 Mono 執行階段環境變數？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: fdd208122934b6fa8194a644592d1e23c4000d57
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030895"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>如何在 Xamarin Studio 中，為 iOS 專案設定 Mono 執行階段環境變數？

如果您需要為 Mono 設定任何執行時間環境變數，可以在 專案選項 中設定， **> 執行 > 一般** 頁面。

注意：以這種方式設定的 SGen （MONO\_GC\_PARAMS）垃圾收集環境變數只會在從 Xamarin Studio 啟動時使用。 如果您從裝置啟動應用程式，將會忽略 Sgen 的設定。 

若要永久設定應用程式的環境變數，您必須將此新增為額外的 mtouch 引數（適用于所有相關的設定）：

```csharp
   --setenv=NAME=VALUE
```

若要查看可設定的環境變數，請參閱 Mono 手冊頁面： [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1))查看標題為： `ENVIRONMENT VARIABLES` 的區段

![](xs-mono-runtime-images/environment-variables.jpg "Setting environment variables for a project")
