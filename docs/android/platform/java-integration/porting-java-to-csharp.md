---
title: 移植到 C# 的 Java
description: 使用 Java Xamarin.Android 應用程式中的第三個選項是 Java 來源程式碼移植到 C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2016
ms.openlocfilehash: c2d05b101c627dab42dc1343eab2a408d1bd010f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762593"
---
# <a name="porting-java-to-c"></a>移植到 C# 的 Java

_使用 Java Xamarin.Android 應用程式中的第三個選項是 Java 來源程式碼移植到 C#。_

## <a name="overview"></a>總覽

這個方法可能會感興趣的組織的：

-  **改用技術堆疊 Java 從 C#。**
-  **必須維護 C# 和 Java 版本相同的產品。**
-  **想要擁有受歡迎的 Java 程式庫的.NET 版本。**


有兩種方式，使用 Java 程式碼移植到 C#。 第一種方式是將程式碼手動移植。 這牽涉到熟練的開發人員了解.NET 和 Java，並十分熟悉每種語言正確的語言。 這種方法最適合用於小量的程式碼，或想要完全以 C# 將移開 Java 的組織。

第二個移植方法是嘗試使用的程式碼轉換子，像是自動化的程序[銳利](https://github.com/mono/sharpen)。 [銳利化](https://github.com/mono/sharpen)是開放原始碼轉換子，從最初用來連接埠的程式碼的 Versant *db4o*從 Java C#。 db4o 是物件導向的資料庫，Versant 開發在 Java 中，並接著移植到.NET。 使用程式碼轉換器合理的專案必須存在於這兩種語言，以及需要某些兩者之間的同位檢查。

當自動程式碼轉換工具有意義的範例所示[ngit](https://github.com/mono/ngit)專案。
Ngit 是 Java 專案的連接埠[jgit](http://eclipse.org/)。
Jgit 本身是以 Java 撰寫的[Git](http://git-scm.com/)來源的程式碼管理系統。 若要從 Java 產生 C# 程式碼，ngit 程式設計人員用於自訂的自動化的系統從 jgit 擷取 Java 程式碼，套用某些修補程式，以容納轉換過程中，然後再執行 銳利化，產生的 C# 程式碼。 這可讓 ngit 專案受益於 jgit 便已完成的連續、 進行中工作。

通常會有非一般的工作數量涉及啟動自動程式碼轉換工具，這可能會經過證明一道障礙，可使用。 在許多情況下，它可能會更簡單且更容易使用 C# 的連接埠 Java 以手動方式。



## <a name="related-links"></a>相關連結

- [銳利化轉換工具](https://github.com/mono/sharpen)
