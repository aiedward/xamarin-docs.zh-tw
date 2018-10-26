---
title: 可延伸應用程式標記語言 (XAML)
description: XAML 是一種宣告式標記的語言，可用來定義使用者介面。 使用 XAML 語法，雖然執行階段行為，被定義在個別的程式碼後置檔案中 XML 檔案中定義的使用者介面。
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/18/2018
ms.openlocfilehash: 4045f3a1d31b1c0c8b69e840d3943b6ce258b894
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113582"
---
# <a name="extensible-application-markup-language-xaml"></a>可延伸應用程式標記語言 (XAML)

_XAML 是一種宣告式標記的語言，可用來定義使用者介面。使用 XAML 語法，雖然執行階段行為，被定義在個別的程式碼後置檔案中 XML 檔案中定義的使用者介面。_

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolve 2016： 成為 XAML Master**

> [!NOTE]
> 試試看[XAML Standard 預覽版](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[XAML 基本知識](xaml-basics/index.md)

XAML 可讓開發人員在 Xamarin.Forms 應用程式中使用標記，而不是程式碼定義使用者介面。 Xamarin.Forms 程式永遠不會需要 XAML，但它是靈活，並經常會以視覺化方式一致且更簡潔，比對等的程式碼。 XAML 是非常適合用於搭配熱門的 Model View ViewModel (MVVM) 應用程式架構： XAML 定義的檢視，透過以 XAML 為基礎的資料繫結連結到 ViewModel 的程式碼。

## <a name="xaml-compilationxamlcmd"></a>[XAML 編譯](xamlc.md)

XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。 此文章說明如何使用 XAMLC 和它的優點。

## <a name="xaml-previewerxaml-previewermd"></a>[XAML 預覽程式](xaml-previewer.md)

[XAML 預覽程式](~/xamarin-forms/xaml/xaml-previewer.md)呈現頁面的並行以 XAML 標記，可讓您查看您呈現您所輸入的使用者介面的即時預覽。

## <a name="xaml-namespacesnamespacesmd"></a>[XAML 命名空間](namespaces.md)

XAML 使用`xmlns`命名空間宣告的 XML 屬性。 本文介紹 XAML 命名空間語法，並示範如何宣告 XAML 命名空間，以存取的型別。

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[XAML 標記延伸](markup-extensions/index.md)

XAML 包含標記延伸模組，將屬性設定為 「 值 」 或 「 超出能使用簡單的字串表示的物件。 這些包括常數、 靜態屬性和欄位、 資源字典，以及資料繫結參考。

## <a name="field-modifiersfield-modifiersmd"></a>[欄位修飾詞](field-modifiers.md)

`x:FieldModifier`命名空間屬性指定具名 XAML 項目產生欄位的存取層級。

## <a name="passing-argumentspassing-argumentsmd"></a>[傳遞引數](passing-arguments.md)

XAML 可以用來將引數傳遞至非預設建構函式或 factory 方法中。 這篇文章示範如何使用可用來將引數傳遞至建構函式，呼叫 factory 方法，並指定的泛型引數類型的 XAML 屬性。

## <a name="bindable-propertiesbindable-propertiesmd"></a>[可繫結的屬性](bindable-properties.md)

在 Xamarin.Forms 中，通用語言執行平台 (CLR) 屬性的功能會擴充可繫結的屬性。 可繫結的屬性是屬性的特殊類型，其中 Xamarin.Forms 屬性系統會追蹤屬性的值。 這篇文章介紹可繫結的屬性，並示範如何建立和使用它們。

## <a name="attached-propertiesattached-propertiesmd"></a>[附加屬性](attached-properties.md)

附加的屬性是特殊類型的可繫結的屬性，定義一個類別中，但連接至其他物件，並可辨識在 XAML 做為屬性中包含的類別，以句號分隔屬性名稱。 本文章提供附加屬性的簡介，並示範如何建立和使用它們。

## <a name="resource-dictionariesresource-dictionariesmd"></a>[資源字典](resource-dictionaries.md)

XAML 資源都可以使用一次以上的物件的定義。 A [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)允許在單一位置中，定義及重複使用在 Xamarin.Forms 應用程式的資源。 這篇文章示範如何建立及取用`ResourceDictionary`，以及如何合併其中`ResourceDictionary`到另一個。
