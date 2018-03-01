---
title: "XAML 標準 （預覽） 控制項"
description: "如何開始瀏覽 Xamarin.Forms 中的 XAML 標準預覽"
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 3f30a77975a9f42380ecf7efd73426763ec83ef0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview-controls"></a>XAML 標準 （預覽） 控制項

![預覽](~/media/shared/preview.png)

此頁面會列出在預覽中，連同其對等的 Xamarin.Forms 控制項可用的 XAML 標準控制項。

另外還有控制項中 XAML 標準具有新的屬性和列舉型別名稱的清單。

## <a name="controls"></a>控制項

<table style="width:300px">
  <tr><th>Xamarin.Forms</th><th>XAML 標準</th></tr>
  <tr><td>Frame</td><td>Border</td></tr>
  <tr><td>選擇器</td><td>ComboBox</td></tr>
  <tr><td>ActivityIndicator</td><td>ProgressRing</td></tr>
  <tr><td>StackLayout</td><td>StackPanel</td></tr>
  <tr><td>ThisAddIn</td><td>TextBlock</td></tr>
  <tr><td>進入</td><td>TextBox</td></tr>
  <tr><td>參數</td><td>ToggleSwitch</td></tr>
  <tr><td>ContentView</td><td>UserControl</td></tr>
</table>

## <a name="properties-and-enumerations"></a>屬性和列舉型別

<table>
  <tr><th>Xamarin.Forms<br/>控制更新的屬性</th><th>Xamarin.Forms<br/>屬性或列舉</th><th>XAML 標準<br/>對等項目</th></tr>
  <tr><td>按鈕、 項目、 標籤、 日期選擇器、 編輯器、 SearchBar、 Timeupdown</td><td>TextColor</td><td>前景</td></tr>
  <tr><td>VisualElement</td><td>BackgroundColor</td><td><i>背景 *</i></td></tr>
  <tr><td>選擇器 按鈕</td><td>BorderColor, OutlineColor</td><td>BorderBrush</td></tr>
  <tr><td>按鈕</td><td>BorderWidth</td><td>BorderThickness</td></tr>
  <tr><td>進度列</td><td>進度</td><td>值</td></tr>
  <tr><td>按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型</td><td>FontAttributes<br/>粗體、 斜體、 無</td><td>FontStyle<br/>斜體、 一般</td></tr>
  <tr><td>按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型</td><td>FontAttributes</td><td><i>FontWeights *</i><br/>粗體、 一般</td></tr>
  <tr><td>InputView</td><td>鍵盤<br/>預設 Url、 數目、 電話、 文字、 聊天室、 電子郵件</td><td><i>InputScopeNameValue *</i><br/>預設 Url、 數字、 TelephoneNumber、 文字、 聊天室、 EmailNameOrAddress</td></tr>
  <tr><td>StackPanel</td><td>StackOrientation</td><td><i>方向 *</i></td></tr>
</table>

> [!IMPORTANT]
> 項目以標示 * 會在目前的預覽版本中不完整


## <a name="related-links"></a>相關連結

- [預覽 NuGet](https://aka.ms/xf-xamlstandard-nuget)
