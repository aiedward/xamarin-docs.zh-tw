---
title: 將 Java 移植到 C#
description: 第三個選項在 Xamarin.Android 應用程式中使用 Java 移植到 Java 原始程式碼是C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: 9beb6d59c9376a404c06af7f0cd1efd985929843
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075138"
---
# <a name="porting-java-to-c"></a>將 Java 移植到 C#

_第三個選項在 Xamarin.Android 應用程式中使用 Java 移植到 Java 原始程式碼是C#。_

## <a name="overview"></a>總覽

這種方法可能感興趣的組織的：

-  **從 Java 切換技術堆疊C#。**
-  **至少要有C#和相同的產品的 Java 版本。**
-  **想要擁有熱門的 Java 程式庫的.NET 版本。**


有兩種方式，Java 程式碼移植到C#。 第一種方式是將程式碼手動移植。 這牽涉到技術熟練的開發人員了解.NET 和 Java，並十分熟悉每種語言正確的習慣用語。 這種方法有意義的最少量的程式碼，或想要完全從 Java 移動的組織C#。

第二個的移植方法是嘗試使用程式碼轉換器，例如自動化程序[銳利化](https://github.com/mono/sharpen)。 [精進](https://github.com/mono/sharpen)開放原始碼轉換器，從原本用來連接埠的程式碼的 Versant *db4o*從 Java C#。 db4o 是物件導向的資料庫，Versant 開發在 Java 中，然後移植到.NET。 使用程式碼轉換器合情合理，必須存在於這兩種語言，而且需要某些兩者之間的同位檢查的專案。

舉例來說，當自動程式碼轉換工具合理示[ngit](https://github.com/mono/ngit)專案。
Ngit 是 Java 專案的連接埠[jgit](http://eclipse.org/)。
Jgit 本身是以 Java 撰寫的[Git](http://git-scm.com/)原始程式碼管理系統。 若要產生C#從自訂自動化系統，以從 jgit 擷取 Java 程式碼，適用於某些修補程式，以容納轉換過程中，然後再執行銳利化，它會產生 ngit 程式設計人員使用 Java 程式碼C#程式碼。 這可讓 ngit 專案可受益於 jgit 進行的連續、 進行中工作。

很多工作的非一般數量與啟動程序自動化的程式碼轉換工具有關，這可能證明是障礙，可使用。 在許多情況下，它可能是更簡單且更容易使用的連接埠 JavaC#以手動方式。



## <a name="related-links"></a>相關連結

- [精進轉換工具](https://github.com/mono/sharpen)
