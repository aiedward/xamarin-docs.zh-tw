---
title: 適用於 Objective-C 開發人員的 C# Primer
description: 本文件描述適合 Objective-C 開發人員使用的 C#。 它會透過檢查通訊協定和介面、類別和擴充方法、架構和組件、選取器和具名參數等等，來比較並對照兩種語言。
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 514841bb18ebed72c07377ff95127dff247f0d71
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786209"
---
# <a name="c-primer-for-objective-c-developers"></a>適用於 Objective-C 開發人員的 C# Primer

_Xamarin.iOS 可讓您跨平台分享以 C# 撰寫的平台獨立程式碼。不過，現有的 iOS 應用程式可能想要利用已建立的 Objective-C 程式碼。此文章可做為想要移至 Xamarin 和 C# 語言之 Objective-C 開發人員的入門。_

以 Objective-C 開發的 iOS 與 OS X 應用程式只要在不需要平台特定程式碼的情況下利用 C#，就能獲得 Xamarin 的優點，進而使得此類程式碼能用於非 Apple 裝置。 例如 Web 服務、JSON 與 XML 剖析，以及自訂演算法等接著可以用於跨平台行為。

為在維護現有 Objective-C 資產的情況化發揮 Xamarin 的優點，可以使用使用稱為繫結的 Xamarin 技術將 Xamarin 公開給 C#，這樣可讓 Objective-C 程式碼提供給受控 C# 環境。 此外，您也可以視需要將程式碼逐行移植為 C#。 不過，不論採用繫結或移植方法，必須同時熟悉 Objective-C 與 C#，才能有效地利用現有的 Objective-C 程式碼來搭配 Xamarin.iOS。

## <a name="objective-c-interop"></a>Objective-C 交互操作性

目前沒有任何支援的機制可以用來在 C# 中使用 Xamarin.iOS 來建立可從 Objective-C 呼叫的程式庫。 主要原因是除了繫結之外，也需要 Mono 執行階段。 不過，您仍然可以以 Objective-C 建立大部分的邏輯，包括使用者介面。 若要這樣做，請將 Objective-C 程式法封裝在程式庫中，並建立與該程式庫的繫結。 需要 Xamarin.iOS 才能啟動應用程式 (表示它必須建立 `Main` 進入點)。 在那之後，任何其他邏輯可以置於 Objective-C 中，並透過繫結 (或透過 P/Invoke) 公開給 C#。 透過這種方式，您可以將平台特定邏輯保留在 Objective-C 中，並在 C# 中開發與平台無關的部分。

此文章重點說明兩種語言的一些重要相同點，以及數個差異的對比，作為移轉到使用 C# 搭配 Xamarin.iOS 之環境的入門基本知識，不論是繫結到現有的 Objective-C 程式碼或將它移植到 C#。

如需有關建立繫結的詳細資料，請參閱[繫結 Objective-C](~/ios/platform/binding-objective-c/index.md) 中的其他文件。

## <a name="language-comparison"></a>語言比較

不論是從語法的觀點或執行階段的觀點來看，Objective-C 與 C# 都是差異非常大的語言。 Objective-C 是動態語言，並使用訊息傳遞配置；C# 則是以靜態方式指定型別。 具語法智能特色的 Objective-C 就像是 Smalltalk 一樣，而 C# 則從 Java 衍生許多它的基本語法，雖然近年來它已經非常成熟並包含 Java 沒有的許多功能。

雖然如此，Objective-C 與 C# 有數種語言特色在功能方面都是相同的。 建立從 C# 到 Objective-C 程式碼的繫結或將 Objective-C 移植到 C# 時，了解這些相似性非常實用。

### <a name="protocols-vs-interfaces"></a>通訊協定與介面

Objective-C 與 C# 都是單一繼承語言。 不過，這兩種語言都支援在給定類別中實作多個介面。 在 Objective-C 中，這些邏輯介面稱為「通訊協定」在 C# 中，它們稱為「介面」。 關於在實作方面的智能，C# 介面與 Objective-C 通訊協定之間的主要差異是後者可以有選擇性方法。 如需詳細資訊，請參閱[事件、委派與通訊協定](~/ios/app-fundamentals/delegates-protocols-and-events.md)一文。

### <a name="categories-vs-extension-methods"></a>分類與擴充方法

Objective-C 可讓您將方法新增到那些您未使用「類別」(Category)建立之實作程式碼的類別。 在 C# 中，可以透過「擴充方法」了解相同的概念。

擴充方法可讓您將靜態方法新增到類別，其中 C# 中的靜態方法類似 Objective-C 中的類別方法。 例如，下列程式碼會將名為 `ScrollToBottom` 的方法新增到 `UITextView` 類別，接著是一個從 UIKit 繫結到 Objective-C `UITextView` 類別的受控方法：

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

然後，當 `UITextView` 的執行個體在程式碼中建立時，您將可以在自動完成清單中看到該方法，如下所示：

 ![](primer-images/01-extensionmethodintellisense.png "可以在自動完成可以在自動完成清單中看到該方法")

當擴充方法被呼叫時，該執行個體會被傳遞到引數，例如此範例中的 `textView`。

### <a name="frameworks-vs-assemblies"></a>架構與組件

Objective-C 會將相關類別封裝在稱為架構的特殊目錄中。 不過在 C# 與 .NET 中，組件是用來提供可重複使用的預先編譯程式碼。 在 iOS 外部的環境中，組件包含中繼語言程式碼 (IL)，這在執行階段會透過 Just-In-Time (JIT) 來編譯。 不過，Apple 不允許在 iOS 應用程式中使用 JIT。 因此，使用 Xamarin 開發且以 iOS 為目標的 C# 程式碼是預先編譯 (AOT)，這會產生單一 Unix 可執行檔，以及包含在應用程式套件組合中的中繼資料檔案。

### <a name="selectors-vs-named-parameters"></a>選取器與具名參數

Objective-C 方法本質上就自然在選取器中包含參數名稱。 例如，`AddCrayon:WithColor:` 之類的選取器在程式碼中使用時會清楚傳達每個參數的意義。 C# 也選擇性支援具名引數。

例如，C# 中使用具名引數的類似程式碼看起來會像這樣：

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

雖然 C# 在 4.0 版語言中新增此支援，實務上並不常使用。 不過，若要在您的程式碼中明確表示，確實支援它。

### <a name="headers-and-namespaces"></a>標頭與命名空間

做為 C 的超集，Objective-C 針對與實作檔案分離的公開宣告使用標頭。 C# 不使用標頭檔案。 不像 Objective-C，C# 程式碼是包含在名稱空間中。 若要將可用程式碼包含在某些命名空間中，您會新增 using 指示詞到實作檔案頂端，或用完整命名空間限定類型。

例如，下列程式碼包含 `UIKit` 命名空間，進而將該命名空間中的每個類別提供給實作：

```csharp
using UIKit
namespace MyAppNamespace
{
    // implementation of classes
}
```

此外，上物程式碼中的命名空間關鍵字會設定用於實作檔案本身的命名空間。 若多個實作檔案共用相同的命名空間，則您不需要一併將命名空間包含在 directive 指示詞中，因為那就有這樣的意涵。

### <a name="properties"></a>屬性

Objective-C 與 C# 都有屬性概念，屬性可提供存取子方法周圍的高階抽象。 在 Objective-C 中，@property 編譯器指示詞是用來有效地產生存取子方法。 相反地，C# 包含對語言本身內屬性的支援。 C# 屬性可使用會存取支援欄位的較長樣式來實作，或使用較短的自動屬性語法來實作，如下列範例所示：

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Static 關鍵字

*static* 關鍵字在 Objective-C 與 C# 中有不同的意義。 在 Objective-C 中，Satic 函式是用來將函式的範圍限制為目前的檔案。 不過在 C# 中，範圍是透過 *public*、*private* 與 *internal* 關鍵字來維持。

當 Satic 關鍵字被套用到 Objective-C 中的變數時，變數會在整個函式呼叫中維持其值。

C# 也有 Satic 關鍵字。 當被套用到方法時，它會有效地執行 `+` 修飾詞在 Objective-C 中執行的相同動作。 也就是說，它會建立類別方法。 同樣地，當被套用到其他建構 (例如欄位、屬性與事件) 時，它會讓那些成為它們被定義所在之類型的一部分，而不是具有該類型的任何執行個體。 您也可以設定為靜態類別，讓在該類別中的所有方法也必須是靜態的。

### <a name="nsarray-vs-list-initialization"></a>NSArray 與清單初始化

Objective-C 現在包含可搭配 `NSArray` 使用的常值語言，以便更簡單地初始化。 不過 C# 擁有稱為 `List` (它是「泛型」) 的更豐富類型，這表示清單所持有的類型可透過建立清單的程式碼來提供 (就像 C++ 中的範本)。 此外，清單也支援自動初始化語法，如下所示：

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>區塊與Lambda 運算式

Objective-C 使用「區塊」來建立終止 (Closure)，您可以在其中建立會使用其所封閉之狀態的內嵌函式。 C# 具有類似的概念，它會使用 Lambda 運算式。 在 C# 中，Lambda 運算式是使用 `=>` 運算子所建立，如下所示：

```csharp
(args) => {
    //  implementation code
};
```

如需有關 Lambda 運算式的詳細資訊，請參閱 Microsoft 的 [C# 程式設計手冊](http://msdn.microsoft.com/library/vstudio/bb397687.aspx).

## <a name="summary"></a>總結

在此文章中，我們說明了 Objective-C 與 C# 中相同與相異的語言功能。 在某些案例中，我們說明了兩種語言中的類似功能，例如區塊與 Lambda 運算式，以及延伸方法的類別。 此外，我們也說明了兩種語言的相異之處，例如 C# 中的命名空間與 Static 關鍵字的意義。
