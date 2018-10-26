---
title: 已知的問題與因應措施
description: 本文件將說明 Xamarin 活頁簿的已知的問題和因應措施。 它討論 CultureInfo 問題、 JSON 問題和更多功能。
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 221ed97db17da62f513448b6c85d4df205a7cbaf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110302"
---
# <a name="known-issues--workarounds"></a>已知的問題與因應措施

## <a name="persistence-of-cultureinfo-across-cells"></a>儲存格之間的持續性的 CultureInfo

設定`System.Threading.CurrentThread.CurrentCulture`或是`System.Globalization.CultureInfo.CurrentCulture`不會保存在 Mono 為基礎的活頁簿目標 （Mac、 iOS 和 Android） 上的活頁簿資料格之間由於[在 Mono 中的 bug `AppContext.SetSwitch` ] [ appcontext-bug]實作.

### <a name="workarounds"></a>替代解決辦法

* 設定應用程式的網域本機`DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* 或更新活頁簿 1.2.1 或更新版本，這將會重寫指派給`System.Threading.CurrentThread.CurrentCulture`和`System.Globalization.CultureInfo.CurrentCulture`提供所要的行為 （解決 Mono 的 bug）。

## <a name="unable-to-use-newtonsoftjson"></a>無法使用 Newtonsoft.Json

### <a name="workaround"></a>因應措施

* 更新活頁簿 1.2.1，如此便會安裝 Newtonsoft.Json 9.0.1 （英文)。
  1.3 的活頁簿目前處於 alpha 色板，支援 10 及更新版本的版本。

### <a name="details"></a>詳細資料

這藉其相依性 Microsoft.CSharp 互相衝突的活頁簿發行支援發行 Newtonsoft.Json 10 `dynamic`。 為解決此問題在活頁簿 1.3 預覽版本中，但現在，我們已解決這個問題的釘選的 Newtonsoft.Json，專為版本 9.0.1 （英文）。

明確地根據 Newtonsoft.Json 10 或更新版本的 NuGet 套件只支援活頁簿 1.3 中目前處於 alpha 色板。

## <a name="code-tooltips-are-blank"></a>程式碼的工具提示則空白

沒有[摩納哥編輯器中的 bug] [ monaco-bug]在 Safari/WebKit，用 Mac 活頁簿應用程式中，這會導致程式碼不包含文字的工具提示呈現。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>因應措施

* 按一下之後它會出現工具提示會強制要呈現的文字。

* 或更新活頁簿 1.2.1 或更新版本

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>SkiaSharp 轉譯器為活頁簿 1.3 中遺失

從活頁簿 1.3 開始，我們已移除我們隨附 SkiaSharp 轉譯器中的活頁簿 0.99.0，改用 SkiaSharp 本身提供的轉譯器，請使用我們[SDK](~/tools/workbooks/sdk/index.md)。

### <a name="workaround"></a>因應措施

* 在 NuGet 中的最新版本更新 SkiaSharp。 在撰寫本文時，這是 1.57.1。

## <a name="related-links"></a>相關連結

- [回報 Bug](~/tools/workbooks/install.md#reporting-bugs)
