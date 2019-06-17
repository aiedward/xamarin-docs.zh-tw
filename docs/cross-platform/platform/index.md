---
title: 程式設計語言支援，在 Xamarin 中
description: 本文件說明 Xamarin 支援各種程式設計語言。 它會討論C#， F#，可攜式 Visual Basic.NET 和 Razor 範本。
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 715f63a0be54ba3342bd63c1c76d89656313359a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035895"
---
# <a name="programming-language-support-in-xamarin"></a>程式設計語言支援，在 Xamarin 中

## <a name="c"></a>C# 

###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[非同步支援概觀](~/cross-platform/platform/async.md)

第 5 版的C#導入兩個新的關鍵字來表示非同步作業： async 和 await。 這些關鍵字可讓您撰寫簡單的程式碼會使用工作平行程式庫來執行長時間執行的作業 （例如網路存取），另一個執行緒，並輕鬆地存取完成的結果。 Xamarin.iOS 和 Xamarin.Android 的最新版本支援 async 和 await-本文提供說明和使用 Xamarin 的新語法的範例。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 語言功能](~/cross-platform/platform/csharp-six.md)

最新版的C#語言 – 第 6 版 – 持續發展的語言有較少的未定案、 改善的清晰度和較佳的一致性。 更簡潔的初始化語法、 使用的能力`await`中`catch/finally`區塊，以及 null 條件`?`運算子時特別有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

建置行動應用程式與F#和 Xamarin。

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[可攜式 Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支援使用 Visual Basic.NET 然後可以結合到 Xamarin 應用程式的可攜式類別庫建立。 本文說明如何建立新的 Visual Basic PCL，並將它用於 Xamarin.iOS、 Xamarin.Android 和 Windows Phone 應用程式範例。

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 範本建置 HTML 檢視](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin 可讓開發人員利用 Razor 範本化引擎，原本推出與 ASP.NET MVC 中，連同C#輕鬆地使用 HTML、 Javascript 和 CSS 結合資料，而不必煩惱手動建置程式碼中的 HTML 字串。
這篇文章會示範如何搭配 Xamarin 使用 Razor 範本，適用於 Android 和 iOS。
