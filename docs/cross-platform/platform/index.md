---
title: 語言支援
description: 跨平台應用程式功能和概念。
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: f22bb1b37f3e03f63c9e47cf71d5df57ade0042f
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="language-support"></a>語言支援

本節說明一些更進階的跨平台應用程式功能和概念的文件。

## <a name="c"></a>C# 
###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[非同步支援概觀](~/cross-platform/platform/async.md)

C# 5 版導入了兩個新的關鍵字來表示非同步作業： async 和 await。 這些關鍵字可讓您在另一個執行緒中撰寫簡單的程式碼，它會利用工作平行程式庫執行長時間執行的作業 （例如網路存取），並輕鬆地存取在完成的結果。 最新版的 Xamarin.iOS 和 Xamarin.Android 支援 async 和 await-本文件提供說明和使用新語法中使用 Xamarin 的範例。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 語言功能](~/cross-platform/platform/csharp-six.md)

最新版的 C# 語言 – 第 6 版 – 會持續發展成具有較少的未定案、 改良的清晰度和較佳的一致性的語言。 清理初始化語法、 使用的能力`await`中`catch/finally`封鎖，且 null 條件`?`運算子時特別有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

使用 F # 和 Xamarin 建置行動應用程式。

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[移植 Visual basic.net](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支援使用 Visual basic.net，然後併入 Xamarin 應用程式的可攜式類別庫的建立。 本文示範如何建立新的 Visual Basic PCL 並將它用於範例 Xamarin.iOS、 Xamarin.Android 和 Windows Phone 應用程式。

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 範本建置 HTML 檢視](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin 可讓開發人員運用 Razor 範本引擎原本所導入的 ASP.NET MVC 中，以及 C#，以輕鬆地結合資料與 HTML、 Javascript 和 CSS 省去麻煩輕鬆以手動方式建置程式碼中的 HTML 字串。
本文示範如何使用 xamarin 的 Razor 範本，適用於 Android 和 iOS。
