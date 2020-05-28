---
title: 中的文字Xamarin.Forms
description: Xamarin.Forms有三個主要視圖可處理文字，本文說明如何使用它們來輸入和顯示應用程式中的文字 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 579ff44e9c58d7eea538d5478e99b4c480d44ac0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136184"
---
# <a name="text-in-xamarinforms"></a>中的文字Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_使用 Xamarin.Forms 來輸入或顯示文字。_

Xamarin.Forms有三個主要的視圖可用來處理文字：

- **[標籤](#Label)** &mdash;用於呈現單一或多行文字。 可以在同一行中顯示具有多個格式化選項的文字。
- **[專案](#Entry)** &mdash;用於輸入只有一行的文字。 專案具有密碼模式。
- **[編輯器](#Editor)** &mdash;用於輸入可能需要一行以上的文字。

您可以使用內建或[自訂](#Fonts)樣式來變更文字外觀，而某些控制項則支援自訂[字型](#Styles)。

<a name="Label" />

## <a name="label"></a>[標誌](label.md)

此 `Label` 視圖會用來顯示文字。 它可以顯示多行文字或一行文字。 `Label`可以使用內嵌中的多個格式化選項來呈現文字。 標籤視圖可以在無法放在同一行時換行或截斷文字。

![標籤範例](images/label.png)

如需詳細資訊，請參閱[標籤](label.md)一文。

如需自訂標籤中使用之字型的詳細資訊，請參閱[Fonts](fonts.md)。

<a name="Entry" />

## <a name="entry"></a>[上場](entry.md)

`Entry`用來接受單行文字輸入。 `Entry`提供色彩和字型的控制權。 `Entry`具有密碼模式，而且可以在輸入文字之前顯示預留位置文字。

![專案範例](images/entry.png)

如需詳細資訊，請參閱[專案](entry.md)文章。

請注意，與不同的是， `Label` `Entry` 不能有自訂字型設定。

<a name="Editor" />

## <a name="editor"></a>[編輯器](editor.md)

`Editor`用來接受多行文字輸入。 `Editor`提供色彩和字型的控制權。

![編輯器範例](images/editor.png)

如需詳細資訊，請參閱[編輯器](editor.md)文章。

<a name="Fonts" />

## <a name="fonts"></a>[字型](fonts.md)

許多控制項都使用每個平臺上的內建字型，或包含在應用程式中的自訂字型，來支援不同的字型設定。 如需詳細資訊，請參閱字型[一文。](fonts.md)

<a name="Styles" />

## <a name="styles"></a>[樣式](styles.md)

請參閱使用[樣式](~/xamarin-forms/user-interface/styles/index.md)，以瞭解如何設定適用于多個控制項的字型、[色彩](~/xamarin-forms/user-interface/colors.md)和其他顯示內容。

## <a name="related-links"></a>相關連結

- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
