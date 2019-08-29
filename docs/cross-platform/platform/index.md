---
title: Xamarin 中的程式設計語言支援
description: 本檔說明 Xamarin 支援的各種程式設計語言。 它討論C#、 F#、可移植的視覺效果 Basic.NET 和 Razor 範本。
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 2ec934b2747f89e959d659615629489e86449660
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065159"
---
# <a name="programming-language-support-in-xamarin"></a>Xamarin 中的程式設計語言支援

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[非同步支援概觀](~/cross-platform/platform/async.md)

第5版C#引進了兩個新的關鍵字來表示非同步作業: async 和 await。 這些關鍵字可讓您撰寫簡單的程式碼, 利用工作平行程式庫在另一個執行緒中執行長時間執行的作業 (例如網路存取), 並在完成時輕鬆地存取結果。 最新版本的 Xamarin 和 Xamarin 支援 async 和 await-本檔提供使用新語法與 Xamarin 的說明和範例。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 語言功能](~/cross-platform/platform/csharp-six.md)

最新版的C#語言–第6版–會繼續發展語言, 使其具有較少的重複使用性、改良的清晰度, 以及更多的一致性。 清除程式初始化語法、區塊中`await` `catch/finally`使用的功能, 以及 null 條件`?`運算子特別有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

使用F#和 Xamarin 建立行動裝置應用程式。

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[可移植的視覺效果 Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支援使用 Visual Basic.NET 建立可移植的類別庫, 然後再將其併入 Xamarin 應用程式中。 本文說明如何建立新的 Visual Basic PCL, 然後在範例 Xamarin. iOS、Xamarin 和 Windows Phone 應用程式中使用它。

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 範本建立 HTML 視圖](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin 可讓開發人員運用 Razor 樣板化引擎, 其原本是以 ASP.NET MVC 引進C# , 並可輕鬆地結合 Html、JAVASCRIPT 和 CSS 的資料, 而不需要在程式碼中手動建立 html 字串。
本文示範如何搭配適用于 Android 和 iOS 的 Xamarin 使用 Razor 範本。
