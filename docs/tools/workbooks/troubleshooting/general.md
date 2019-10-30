---
title: Known Issues & Workarounds
description: This document describes known issues and workarounds for Xamarin Workbooks. It discusses CultureInfo issues, JSON issues, and more.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c7b9f93c2d6339ba1fd26b27742ecfc0f438c5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018170"
---
# <a name="known-issues--workarounds"></a>Known Issues & Workarounds

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistence of CultureInfo across cells

Setting `System.Threading.CurrentThread.CurrentCulture` or `System.Globalization.CultureInfo.CurrentCulture` does not persist across workbook cells on Mono-based Workbooks targets (Mac, iOS, and Android) due to a [bug in Mono's `AppContext.SetSwitch`][appcontext-bug] implementation.

### <a name="workarounds"></a>替代解決辦法

- Set the application-domain-local `DefaultThreadCurrentCulture`:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- Or, update to Workbooks 1.2.1 or newer , which will rewrite assignments to `System.Threading.CurrentThread.CurrentCulture` and `System.Globalization.CultureInfo.CurrentCulture` to provide for the desired behavior (working around the Mono bug).

## <a name="unable-to-use-newtonsoftjson"></a>Unable to use Newtonsoft.Json

### <a name="workaround"></a>因應措施

- Update to Workbooks 1.2.1, which will install Newtonsoft.Json 9.0.1.
  Workbooks 1.3, currently in the alpha channel, supports versions 10 and newer.

### <a name="details"></a>詳細資料

Newtonsoft.Json 10 was released which bumped its dependency on Microsoft.CSharp which conflicts with the version Workbooks ships to support `dynamic`. This is addressed in the Workbooks 1.3 preview release, but for now we have worked around this by pinning Newtonsoft.Json specifically to version 9.0.1.

NuGet packages explicitly depending on Newtonsoft.Json 10 or newer are only supported in Workbooks 1.3, currently in the alpha channel.

## <a name="code-tooltips-are-blank"></a>Code Tooltips are Blank

There is a [bug in the Monaco editor][monaco-bug] in Safari/WebKit, which is used in the Mac Workbooks app, that results in code tooltips rendering without text.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>因應措施

- Clicking on the tooltip after it appears will force the text to render.

- Or update to Workbooks 1.2.1 or newer

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>SkiaSharp renderers are missing in Workbooks 1.3

Starting in Workbooks 1.3, we've removed the SkiaSharp renderers that we shipped in Workbooks 0.99.0, in favor of SkiaSharp providing the renderers itself, using our [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>因應措施

- Update SkiaSharp to the latest version in NuGet. At the time of writing, this is 1.57.1.

## <a name="related-links"></a>相關連結

- [Reporting Bugs](~/tools/workbooks/install.md#reporting-bugs)
