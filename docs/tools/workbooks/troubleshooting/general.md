---
title: 已知的問題及因應措施
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 0dcff6402612fb8dad1e80f9158298b1171da75a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="known-issues--workarounds"></a>已知的問題及因應措施

## <a name="persistence-of-cultureinfo-across-cells"></a>在資料格之間的持續性的 CultureInfo

設定`System.Threading.CurrentThread.CurrentCulture`或`System.Globalization.CultureInfo.CurrentCulture`不會保存跨 Mono 架構活頁簿目標 （Mac、 iOS 和 Android） 上的活頁簿資料格因為[中 Mono 的 bug `AppContext.SetSwitch` ] [ appcontext-bug]實作.

### <a name="workarounds"></a>替代解決辦法

* 設定應用程式網域本機`DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* 或更新活頁簿 1.2.1 或更新版本中，這將會重寫指派給`System.Threading.CurrentThread.CurrentCulture`和`System.Globalization.CultureInfo.CurrentCulture`以提供所要的行為 （解決單聲道 bug）。

## <a name="unable-to-use-newtonsoftjson"></a>無法使用 Newtonsoft.Json

### <a name="workaround"></a>因應措施

* 更新活頁簿 1.2.1，如此可安裝 Newtonsoft.Json 9.0.1。
  活頁簿 1.3、 alpha 色板，目前在支援版本 10 和更新版本。

### <a name="details"></a>詳細資料

只有當相依於 Microsoft.CSharp 支援隨附的活頁簿互相衝突的發行 Newtonsoft.Json 10 `dynamic`。 這定址在活頁簿 1.3 預覽版本中，但現在我們已致力解決這個問題的釘選的 Newtonsoft.Json 版本 9.0.1 皆是特別為了。

明確地根據 Newtonsoft.Json 10 或更新版本的 NuGet 封裝只能在活頁簿 1.3 目前中支援 alpha 色板。

## <a name="code-tooltips-are-blank"></a>程式碼的工具提示則為空白

沒有[摩納哥編輯器中的 bug] [ monaco-bug] Safari WebKit/，其中 Mac 活頁簿應用程式中使用時，所產生的程式碼不包含文字的工具提示呈現。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>因應措施

* 之後它會出現在工具提示上按一下 將會強制要呈現的文字。

* 或更新活頁簿 1.2.1 或更新版本

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>SkiaSharp 轉譯器沒有在活頁簿 1.3

從活頁簿 1.3 開始，我們便移除了我們隨附 SkiaSharp 轉譯器在活頁簿 0.99.0，改用 SkiaSharp 本身提供的轉譯器，請使用我們的 [SDK] [/ 輔助線/跨-平台/活頁簿/sdk /]。

### <a name="workaround"></a>因應措施

* 更新 SkiaSharp NuGet 中的最新版本。 在撰寫本文時，這是 1.57.1。

## <a name="related-links"></a>相關連結

- [回報 Bug](~/tools/workbooks/install.md#reporting-bugs)
