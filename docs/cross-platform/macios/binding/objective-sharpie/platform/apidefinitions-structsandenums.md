---
title: ApiDefinitions & 與 structsandenums 檔案
description: 本檔說明目標 Sharpie 所產生的 ApiDefinitions.cs 和 StructsAndEnums.cs 檔案。 然後，這些檔案會用來從C#存取目標-C 程式碼。
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 870733554f3f69b7a0cd9b35c1b89e24c62b264d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016204"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & 與 structsandenums 檔案

當目標 Sharpie 成功執行時，它會產生 `Binding/ApiDefinitions.cs` 和 `Binding/StructsAndEnums.cs` 檔案。
這兩個檔案會加入至 Visual Studio for Mac 中的系結專案，或直接傳遞至 `btouch` 或 `bmac` 工具以產生最終的系結。

在*某些*情況下，這些產生的檔案可能就是您所需要的，不過，開發人員通常需要手動修改這些產生的檔案，以修正工具無法自動處理的任何問題（例如以`Verify` 標示的問題）。 [屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)）。

後續步驟包括：

- **調整名稱**：有時候您會想要調整方法和類別的名稱，以符合 .NET Framework 的設計方針。
- **方法或屬性**：目標 Sharpie 所使用的啟發學習法有時會選擇要轉換成屬性的方法。 此時，您可以決定這是否為預期的行為。
- 連接**事件**：您可以連結類別與您的委派類別，並自動產生事件。
- 連結**通知**：無法從單純的標頭檔中解壓縮通知的 API 合約，這將需要一趟 api 檔。 如果您想要強型別通知，就必須更新結果。
- **API 鑒藏**：此時，您可能會選擇提供額外的C#函式、新增方法（以允許初始化-在結構化語法中）、運算子多載，並在額外的定義檔案上執行您自己的介面。

請參閱系結[API](~/cross-platform/macios/binding/objective-c-libraries.md)描述，以查看這些檔案如何符合系結程式，如下圖所示：

![](apidefinitions-structsandenums-images/binding-flowchart.png "The binding process is shown in this diagram")

如需這些檔案內容的詳細資訊，請參閱系結[類型參考](~/cross-platform/macios/binding/binding-types-reference.md)。
