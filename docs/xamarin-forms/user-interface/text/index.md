---
title: 在 Xamarin.Forms 中的文字
description: Xamarin.Forms 具有三個主要的檢視，可處理的文字，以及這篇文章說明如何使用它們來輸入及顯示在 Xamarin.Forms 應用程式中的文字。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
ms.openlocfilehash: 3e30e3d4ea3bf0ec58aff842ffee68885a47d44a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656183"
---
# <a name="text-in-xamarinforms"></a>在 Xamarin.Forms 中的文字

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_使用 Xamarin.Forms 來輸入或顯示的文字。_

Xamarin.Forms 會具有三個主要的檢視，可搭配文字使用：

- **[標籤](#Label)** &mdash;呈現單一或多行文字。 可以在同一行顯示使用多個的格式化選項的文字。
- **[項目](#Entry)** &mdash;只有一行的文字輸入。 項目都有密碼模式。
- **[編輯器](#Editor)** &mdash;可能需要多行的文字輸入。

可以使用內建或自訂變更文字的外觀[樣式](#Styles)及部分控制項支援自訂[字型](#Fonts)。

<a name="Label" />

## <a name="labellabelmd"></a>[Label](label.md)

`Label`檢視用來顯示文字。 它可以顯示多行文字 」 或 「 單行文字。 `Label` 可以有多個用於內嵌的格式化選項呈現文字。 標籤檢視可以換行，或截斷時它不能放在一行的文字。

![](images/label.png "標籤範例")

請參閱[標籤](label.md)一文中的詳細資訊。

如需自訂標籤中所使用的字型資訊，請參閱[字型](fonts.md)。

<a name="Entry" />

## <a name="entryentrymd"></a>[Entry](entry.md)

`Entry` 用來接受單行文字輸入。 `Entry` 色彩和字型的供應項目控制。 `Entry` 具有密碼的模式，可以顯示預留位置文字，直到在輸入文字。

![](images/entry.png "項目範例")

請參閱[項目](entry.md)文件，如需詳細資訊。

請注意，不同於`Label`，`Entry`不能有自訂的字型設定。

<a name="Editor" />

## <a name="editoreditormd"></a>[編輯器](editor.md)

`Editor` 用來接受多行文字輸入。 `Editor` 色彩和字型的供應項目控制。

![](images/editor.png "編輯器範例")

請參閱[編輯器](editor.md)文件，如需詳細資訊。

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[字型](fonts.md)

許多控制項都使用每個平臺上的內建字型, 或包含在應用程式中的自訂字型, 來支援不同的字型設定。 請參閱[字型](fonts.md)一文中的詳細資訊。

<a name="Styles" />

## <a name="stylesstylesmd"></a>[樣式](styles.md)

請參閱[使用樣式](~/xamarin-forms/user-interface/styles/index.md)若要了解如何設定字型[色彩](~/xamarin-forms/user-interface/colors.md)，和其他適用於跨多個控制項的顯示內容。

## <a name="related-links"></a>相關連結

- [文字 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
