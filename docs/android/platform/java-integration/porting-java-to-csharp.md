---
title: 將 JAVA 移植C#到 Xamarin. Android
description: 在 Xamarin Android 應用程式中使用 JAVA 的第三個選項是將 JAVA 原始程式碼移植到C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027199"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>將 JAVA 移植C#到 Xamarin. Android

這種方法可能會對下列組織感意義：

- **正在將技術堆疊從 JAVA 切換C#至。**
- **必須維護相同C#產品的和 JAVA 版本。**
- **想要擁有 .NET 版本的熱門 JAVA 程式庫。**

有兩種方式可以將 JAVA 程式碼C#移植到。 第一種方式是手動移植程式碼。 這牽涉到熟練的開發人員，瞭解 .NET 和 JAVA，並熟悉各種語言的適當慣用語。 這種方法最適合用於少量的程式碼，或適用于想要完全從 JAVA 移至C#的組織。

第二種移植方法是使用程式碼轉換器（例如「[銳化](https://github.com/mono/sharpen)」）來嘗試和自動化進程。 「[銳化](https://github.com/mono/sharpen)」是 Versant 的開放原始碼轉換器，原先用來將從 JAVA *db4o*的程式碼C#移植到。 db4o 是物件導向的資料庫，Versant 以 JAVA 開發，然後移植到 .NET。 對於必須同時存在於這兩種語言，而且需要兩者之間有一些同位檢查的專案而言，使用程式碼轉換器可能很有意義。

在[ngit](https://github.com/mono/ngit)專案中可以看到自動化程式碼轉換工具有意義的範例。
Ngit 是 JAVA 專案[jgit](https://eclipse.org/)的埠。
Jgit 本身是[Git](https://git-scm.com/)原始程式碼管理系統的 JAVA 執行。 為了從C# JAVA 產生程式碼，ngit 程式設計人員會使用自訂的自動化系統，將 java 程式碼從 jgit 解壓縮、套用一些修補程式以容納轉換程式，然後執行「 C#銳化」來產生程式碼。 這可讓 ngit 專案受益于在 jgit 上進行的持續性、持續性工作。

啟動自動化程式碼轉換工具時，通常會涉及非常少的工作量，而且這可能會證明要使用的屏障。 在許多情況下，以手動方式將 JAVA 移植到C#是比較簡單又容易。

## <a name="related-links"></a>相關連結

- [銳化轉換工具](https://github.com/mono/sharpen)
