---
title: 中的文字 Xamarin.Forms
description: Xamarin.Forms 有三個主要的視圖可使用文字，而本文說明如何使用它們在應用程式中輸入和顯示文字 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37973db6288f092d756a29de61731a296eac3924
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "93375404"
---
# <a name="text-in-xamarinforms"></a>中的文字 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-text)

_使用 Xamarin.Forms 來輸入或顯示文字。_

Xamarin.Forms 有三個主要的視圖可使用文字：

- **[標籤](#label)** &mdash; 用於呈現單一或多行文字。 可以在同一行中顯示具有多個格式化選項的文字。
- **[專案](#entry)** &mdash; 用於輸入只是一行的文字。 專案具有密碼模式。
- **[編輯器](#editor)** &mdash; 輸入可能需要一行以上的文字。

您可以使用內建或[自訂](#fonts)樣式來變更文字外觀，而某些控制項則支援自訂[字型](#styles)。

## <a name="label"></a>[標籤](label.md)

此 `Label` 視圖可用來顯示文字。 它可以顯示多行文字或一行文字。 `Label` 可以使用內嵌中使用的多個格式化選項來呈現文字。 標籤視圖可以在無法容納一行時，包裝或截斷文字。

![標籤範例](images/label.png)

如需詳細資訊，請參閱 [標籤](label.md) 文章。

如需自訂標籤中所使用之字型的詳細資訊， [請參閱字型](fonts.md)。

## <a name="entry"></a>[進入](entry.md)

`Entry` 用來接受單行文字輸入。 `Entry` 提供色彩和字型的控制權。 `Entry` 具有密碼模式，而且可以在輸入文字之前顯示預留位置文字。

![專案範例](images/entry.png)

如需詳細資訊，請參閱 [專案](entry.md) 文章。

請注意，與不同 `Label` ， `Entry` 不能有自訂的字型設定。

## <a name="editor"></a>[編輯器](editor.md)

`Editor` 用來接受多行文字輸入。 `Editor` 提供色彩和字型的控制權。

![編輯器範例](images/editor.png)

如需詳細資訊，請參閱 [編輯器](editor.md) 文章。

## <a name="fonts"></a>[字型](fonts.md)

許多控制項都支援使用每個平臺上內建字型的不同字型設定，或應用程式隨附的自訂字型。 如需詳細資訊，請參閱「字型 [」](fonts.md) 一文。

## <a name="styles"></a>[樣式](styles.md)

請參閱 [使用樣式](~/xamarin-forms/user-interface/styles/index.md) 來學習如何設定適用于多個控制項的字型、 [色彩](~/xamarin-forms/user-interface/colors.md)和其他顯示內容。

## <a name="related-links"></a>相關連結

- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)