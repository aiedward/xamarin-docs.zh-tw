---
title: 將 JaVA 移植到 Xamarin.安卓的 C#
description: 在 Xamarin.Android 應用程式中使用 Java 的第三個選項是將 Java 原始程式碼移植到 C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027199"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>將 JaVA 移植到 Xamarin.安卓的 C#

這種辦法可能引起各組織的興趣::

- **正在將技術堆疊從 JAVA 切換到 C#。**
- **必須維護同一產品的 C# 和 Java 版本。**
- **希望擁有流行的 Java 庫的 .NET 版本。**

有兩種方法可以將 JAVA 代碼移植到 C#。 第一種方法是手動移植代碼。 這涉及到熟練的開發人員,他們同時理解 .NET 和 Java,並且熟悉每種語言的正確習語。 此方法對於少量代碼或希望完全從 JAVA 遷移到 C# 的組織來說最為有意義。

第二種移植方法是嘗試使用代碼轉換器(如[銳化](https://github.com/mono/sharpen))來自動執行該過程。 [銳化](https://github.com/mono/sharpen)是從 Versant 的開源轉換器,最初用於將*db4o*的代碼從 Java 移植到 C#。 db4o 是 Versant 在 Java 中開發的面向物件資料庫,然後移植到 .NET。 對於兩種語言都必須存在且需要兩者之間的某種奇偶校驗的專案,使用代碼轉換器可能有意義。

在[ngit](https://github.com/mono/ngit)專案中可以看到自動代碼轉換工具有意義時的範例。
Ngit是JAVA專案[jgit](https://eclipse.org/)的埠。
Jgit 本身是[Git](https://git-scm.com/)原始程式碼管理系統的 Java 實現。 要從 Java 生成 C# 代碼,ngit 程式師使用自訂自動化系統從 jgit 中提取 Java 代碼,應用一些修補程式來適應轉換過程,然後運行銳化(生成 C# 代碼)。 這使得ngit專案受益於在jgit上正在進行的持續工作。

引導自動代碼轉換工具通常涉及大量工作,這可能成為使用的障礙。 在許多情況下,手動將 Java 移植到 C# 可能更簡單、更容易。

## <a name="related-links"></a>相關連結

- [銳化轉換工具](https://github.com/mono/sharpen)
