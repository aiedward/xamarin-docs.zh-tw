---
title: Text
description: 使用 Xamarin.Forms，輸入或顯示的文字。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 37289c4c118c3a84b8c81d3a1c1502ff80564bb9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="text"></a>Text

_使用 Xamarin.Forms，輸入或顯示的文字。_

Xamarin.Forms 有三個主要的檢視，使用文字的：

- **[標籤](#Label)** &mdash;呈現單一或多行文字。 可以在同一行顯示有多個格式設定選項的文字。
- **[項目](#Entry)** &mdash;只有一行的文字輸入。 項目中的密碼模式。
- **[編輯器](#Editor)** &mdash;採取多行文字輸入。

可以使用內建或自訂變更文字的外觀[樣式](#Styles)和有些控制項支援自訂[字型](#Fonts)。

<a name="Label" />

## <a name="labellabelmd"></a>[Label](label.md)

`Label`檢視用來顯示文字。 它可以顯示多行文字 」 或 「 單行文字。 `Label` 可以有多個用於內嵌的格式化選項呈現文字。 [標籤] 檢視可以換行，或截斷時它無法放在一行的文字。

![](images/label.png "標籤範例")

請參閱[標籤](label.md)文件如需詳細資訊。

如需自訂標籤中所使用的字型，請參閱[字型](fonts.md)。

<a name="Entry" />

## <a name="entryentrymd"></a>[Entry](entry.md)

`Entry` 用來接受單行文字輸入。 `Entry` 提供控制色彩，但不能有自訂的字型。 `Entry` 具有密碼模式，而且可以顯示預留位置文字，直到輸入文字。

![](images/entry.png "項目範例")

請參閱[項目](entry.md)文件以取得詳細資訊。

請注意，不同於`Label`，`Entry`不能有自訂的字型設定。

<a name="Editor" />

## <a name="editoreditormd"></a>[編輯器](editor.md)

`Editor` 用來接受多行文字輸入。 `Editor` 可以具有自訂背景色彩，但文字色彩和字型無法改變。

![](images/editor.png "編輯器範例")

請參閱[編輯器](editor.md)文件以取得詳細資訊。

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[字型](fonts.md)

`Label`控制項支援以不同的字型設定每個平台或自訂的字型包含在您的應用程式上使用內建的字型。 請參閱[字型](fonts.md)文件如需詳細資訊。

<a name="Styles" />

## <a name="stylesstylesmd"></a>[樣式](styles.md)

請參閱[使用樣式](~/xamarin-forms/user-interface/styles/index.md)以了解如何設定字型，[色彩](~/xamarin-forms/user-interface/colors.md)，以及其他適用於跨多個控制項的顯示屬性。



## <a name="related-links"></a>相關連結

- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
