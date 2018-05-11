---
title: ApiDefinitions & StructsAndEnums 檔案
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: cd0d32d48d96e2f4c2c109bfb79864fa77b8268f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums 檔案

在目標 Sharpie 曾順利執行，它會產生`Binding/ApiDefinitions.cs`和`Binding/StructsAndEnums.cs`檔案。
這些兩個檔案加入至繫結專案在 Visual Studio for Mac，或直接傳遞至`btouch`或`bmac`工具來產生最後的繫結。

在*某些*但更頻繁的次數，開發人員將需要手動修改這些產生的檔案以修正任何問題，可能不會自動處理工具 （例如那些加上旗標，這些產生的檔案可能會在需要時，所有的案例與[`Verify`屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md))。

接下來的步驟包括：

- **調整名稱**： 有時您會想要調整的方法和類別，以符合.NET Framework 設計方針的名稱。
- **方法或屬性**： 有時目標 Sharpie 所使用的啟發學習法會挑選要轉換成屬性的方法。 此時，您可能會決定這是否預期的行為。
- **連結事件**： 您無法連結您委派類別的類別，並自動產生的事件。
- **通知連結**： 不可能從純標頭檔中擷取 API 合約的通知，這將需要應用程式開發介面文件的路線。 如果您想強類型的通知，您必須更新結果。
- **應用程式開發介面策展**： 此時，您可以選擇提供額外的建構函式、 將方法 （以允許 C# 初始化上建構語法）、 運算子多載和實作您自己的介面上的額外的定義檔。

請參閱[繫結 API](~/cross-platform/macios/binding/objective-c-libraries.md)描述，請參閱 < 如何將這些檔案放入繫結程序中，如下圖所示：

![](apidefinitions-structsandenums-images/binding-flowchart.png "在此圖中顯示繫結程序")

請參閱[繫結型別參考](~/cross-platform/macios/binding/binding-types-reference.md)如需這些檔案的內容。

