---
title: eXtensible Application Markup Language (XAML)
description: XAML 是可以用來定義使用者介面的宣告式標記語言。 使用者介面被定義在執行階段行為，被定義在個別的程式碼後置檔案中，使用 XAML 語法中，XML 檔案。
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/18/2018
ms.openlocfilehash: c040c12829708418d0a705b8e9f930989900c678
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209423"
---
# <a name="extensible-application-markup-language-xaml"></a>eXtensible Application Markup Language (XAML)

_XAML 是可以用來定義使用者介面的宣告式標記語言。使用者介面被定義在執行階段行為，被定義在個別的程式碼後置檔案中，使用 XAML 語法中，XML 檔案。_

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**發展 2016年： 成為 XAML Master**

> [!NOTE]
> 試試看[XAML 標準預覽](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[XAML 基本知識](xaml-basics/index.md)

XAML 可讓開發人員定義的使用者介面，在 Xamarin.Forms 應用程式中使用標記，而不是程式碼。 XAML 永遠不需要在 Xamarin.Forms 程式中，但是 toolable，而且通常會較以視覺化方式一致且更簡潔比對等程式碼。 XAML 是特別適合搭配受歡迎的模型-檢視-ViewModel (MVVM) 應用程式架構： XAML 定義的檢視，透過以 XAML 為基礎的資料繫結連結到 ViewModel 程式碼。

## <a name="xaml-compilationxamlcmd"></a>[XAML 編譯](xamlc.md)

XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。 此文章說明如何使用 XAMLC 和它的優點。

## <a name="xaml-previewerxaml-previewermd"></a>[XAML 預覽程式](xaml-previewer.md)

[XAML 預覽程式](~/xamarin-forms/xaml/xaml-previewer.md)在宣佈 Xamarin 發展 2016年適用於測試的 Alpha 色板。

## <a name="xaml-namespacesnamespacesmd"></a>[XAML 命名空間](namespaces.md)

XAML 使用`xmlns`命名空間宣告的 XML 屬性。 本文章導入 XAML 命名空間的語法，並示範如何宣告 XAML 命名空間，以存取的類型。

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[XAML 標記延伸](markup-extensions/index.md)

XAML 包含標記延伸模組，將屬性設定為值或物件不能使用簡單的字串表示。 這些包括常數、 靜態屬性和欄位、 資源字典和資料繫結的參考。

## <a name="field-modifiersfield-modifiersmd"></a>[欄位修飾詞](field-modifiers.md)

`x:FieldModifier`命名空間屬性會指定產生的具名 XAML 項目欄位的存取層級。

## <a name="passing-argumentspassing-argumentsmd"></a>[傳遞引數](passing-arguments.md)

XAML 可以用來將引數傳遞至非預設建構函式或 factory 方法。 本文將示範使用 XAML 屬性，可用來將引數傳遞給建構函式，呼叫 factory 方法，並指定為泛型引數的類型。

## <a name="bindable-propertiesbindable-propertiesmd"></a>[可繫結的屬性](bindable-properties.md)

透過 Xamarin.Forms，在 common language runtime (CLR) 屬性的功能被擴充可繫結屬性。 可繫結屬性是屬性的特殊類型，其中會追蹤 Xamarin.Forms 屬性系統屬性的值。 本文介紹可繫結的屬性，並示範如何建立及使用它們。

## <a name="attached-propertiesattached-propertiesmd"></a>[附加屬性](attached-properties.md)

附加的屬性是一種特殊的某一個類別中定義，但連接到其他物件，可繫結屬性可辨識在 XAML 中做為屬性包含的類別和屬性名稱以逗號分隔。 本文章提供附加屬性的簡介，並示範如何建立及使用它們。

## <a name="resource-dictionariesresource-dictionariesmd"></a>[資源字典](resource-dictionaries.md)

XAML 資源都可以使用一次以上的物件定義。 A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)讓定義在單一位置，並在整個 Xamarin.Forms 應用程式重複使用的資源。 本文示範如何建立和使用`ResourceDictionary`，以及如何合併一`ResourceDictionary`到另一個。
