---
title: 已知問題 & 因應措施
description: 本檔說明 Xamarin Workbooks 的已知問題和因應措施。 其中討論了 CultureInfo 問題、JSON 問題等等。
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 3701a2a111c5c18f694def241b1e888fa6f4ce19
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120323"
---
# <a name="known-issues--workarounds"></a>已知問題 & 因應措施

## <a name="persistence-of-cultureinfo-across-cells"></a>資料格之間的 CultureInfo 持續性

設定`System.Threading.CurrentThread.CurrentCulture` 或`System.Globalization.CultureInfo.CurrentCulture`不會保存在 mono 型活頁簿 (Mac、iOS 和 Android) 上的活頁簿資料格, 因為[mono `AppContext.SetSwitch`的執行中有錯誤][appcontext-bug]。

### <a name="workarounds"></a>替代解決辦法

- 設定應用程式-網域-本機`DefaultThreadCurrentCulture`:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- 或者, 更新至活頁簿1.2.1 或更新版本, 這會將`System.Threading.CurrentThread.CurrentCulture`指派`System.Globalization.CultureInfo.CurrentCulture`重寫至和, 以提供所需的行為 (解決 Mono 錯誤)。

## <a name="unable-to-use-newtonsoftjson"></a>無法使用 Newtonsoft

### <a name="workaround"></a>因應措施

- 更新為1.2.1 版的活頁簿, 將會安裝 Newtonsoft 9.0.1。
  目前在 Alpha 通道中的活頁簿1.3 支援10和更新版本。

### <a name="details"></a>詳細資料

已發行 Newtonsoft, 其 i 藉相依于與支援`dynamic`的活頁簿版本相衝突的 Microsoft CSharp。 這在活頁簿 1.3 preview 版本中已解決, 但現在我們已藉由將 Newtonsoft 特別釘選至9.0.1 版來解決此問題。

NuGet 套件會根據 Newtonsoft 明確地進行, 只有在目前位於 Alpha 色板的活頁簿1.3 中才支援。

## <a name="code-tooltips-are-blank"></a>程式碼工具提示空白

Safari/WebKit 中的[摩納哥編輯器][monaco-bug]中有一個 bug, 用於 Mac 活頁簿應用程式, 這會導致程式碼工具提示不使用文字呈現。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>因應措施

- 在工具提示出現之後按一下它, 將會強制文字呈現。

- 或更新為1.2.1 或更新版本的活頁簿

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>活頁簿1.3 中遺漏 SkiaSharp 轉譯器

從活頁簿1.3 開始, 我們移除了在 [活頁簿] 0.99.0 中所提供的 SkiaSharp 轉譯器, 而是使用我們的[SDK](~/tools/workbooks/sdk/index.md), 以 SkiaSharp 提供轉譯器本身。

### <a name="workaround"></a>因應措施

- 將 SkiaSharp 更新為 NuGet 中的最新版本。 在撰寫本文時, 這是1.57.1。

## <a name="related-links"></a>相關連結

- [報告錯誤](~/tools/workbooks/install.md#reporting-bugs)
