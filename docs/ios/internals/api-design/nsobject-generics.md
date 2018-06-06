---
title: 泛型的 NSObject Xamarin.iOS 中的子類別
description: 本文件說明如何建立建立的 NSObject 泛型子類別。 它會檢查功能可以和無法完成、 討論靜態註冊機構，並會查看效能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 9caad9d4990225a0468be8ee4987eaa9fea0c118
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786479"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>泛型的 NSObject Xamarin.iOS 中的子類別

## <a name="using-generics-with-nsobjects"></a>使用泛型搭配 NSObjects

從開始，Xamarin.iOS 7.2.1 您可以使用泛型中的子類別`NSObject`(例如[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/))。

您現在可以建立泛型類別，如下所示：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

因為物件的子類別`NSObject`註冊 Objective C 執行階段中，有一些限制，對於進行的一般子類別的`NSObject`型別。
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>考量 NSObject 泛型子類別

這份文件詳細資料中的一般子類別的有限支援限制`NSObjects`Xamarin.iOS 7.2.1 導入。
    
### <a name="generic-type-arguments-in-member-signatures"></a>成員簽章中的泛型型別引數

公開給 Objective C 的成員簽章中的所有泛型型別引數必須有`NSObject`條件約束。

**良好**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 泛型型別參數是`NSObject`，因此選取器簽章`myMethod:`可以安全地公開給 Objective C (它一律是`NSObject`或它的子類別)。

**錯誤**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 不可能建立 OBJECTIVE-C 簽章 Objective C 程式碼可以呼叫，因為簽章會有所不同的泛型型別精確的型別所匯出成員`T`。

**良好**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**原因**： 很可能不受限制地泛型型別引數，只要它們不採用匯出的成員簽章的一部分。

**良好**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**原因**: `T` Objective C 中的參數匯出`MyMethod`被限制為`NSObject`，受條件約束的型別`U`不是簽章的一部分。
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>從 Objective C 的泛型類型的具現化

不允許從 Objective C 的泛型類型的具現化。 這通常發生 xib 中使用 managed 型別時。

此類別定義中，公開 （expose） 的建構函式，請考慮`IntPtr`（Xamarin.iOS 方法建構的原生 Objective C 的執行個體中的 C# 物件）：
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

上述建構沒問題，在執行階段時，如果 Objective C 會嘗試建立它的執行個體這將會擲回的例外狀況。

這是發生問題的原因 OBJECTIVE-C 並沒有泛型類型的概念，而且不能指定建立的正確泛型型別。

這個問題可以克服藉由建立泛型類型的特定子類別。   例如: 
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

現在沒有模稜兩可，類別`GenericUIView`可用於 xibs。

## <a name="no-support-for-generic-methods"></a>不支援泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允許泛型方法。

將不會編譯下列程式碼：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**： 這不允許因為 Xamarin.iOS 不知道要使用的型別引數的類型`T`OBJECTIVE-C 從叫用方法的時機。

替代方法是建立特殊的方法和匯出，改為：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>不允許匯出的靜態成員

如果裝載內的一般子類別，您可以不公開至 Objective C 的靜態成員`NSObject`。

不支援的案例的範例：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**原因：** 一樣泛型方法，若要能夠知道要用於泛型型別引數 t 類型的 Xamarin.iOS 執行階段需求

執行個體使用本身的執行個體的成員 (由於永遠不會為執行個體的泛型<T>，它一律是泛型<SomeSpecificClass>)，但對靜態成員而言這項資訊不存在。

請注意，這適用於即使有問題的成員不會以任何方式使用 T 型別引數。

在此情況下替代方案是建立的特定子類別：

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

### <a name="requires-new-static-registrar"></a>需要新的靜態註冊機構

泛型支援需要新[註冊系統](~/ios/internals/registrar.md)。

如果您嘗試使用舊的舊版註冊系統將會顯示警告，當它遇到泛型類型 （除了不會產生正確的程式碼，因而導致未定義的行為）。
    
## <a name="performance"></a>效能

靜態註冊機構無法解析泛型類型中匯出的成員在建置階段通常會，它必須在執行階段查閱。 這表示叫用從 Objective C 這類方法會稍微慢一點比叫用非泛型類別的成員。

