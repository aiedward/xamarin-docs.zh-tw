---
title: 在 Xamarin.iOS 中 NSObject 的泛型子
description: 本文件說明如何建立建立 NSObject 的泛型子。 它會檢查項目可以和無法完成，討論靜態的註冊機構，並探討效能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 512280e9c298cfbcea6f693b0691236fd1cf5a5f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036469"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>在 Xamarin.iOS 中 NSObject 的泛型子

## <a name="using-generics-with-nsobjects"></a>使用泛型搭配 NSObjects

從開始，Xamarin.iOS 7.2.1 您可以使用泛型中的子`NSObject`(例如[UIView](xref:UIKit.UIView)。

您現在可以建立泛型類別會與下列類似：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

因為物件該子`NSObject`註冊與 OBJECTIVE-C 執行階段中，有一些限制，什麼是可行的泛型子`NSObject`類型。
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 的泛型子類別的考量

本文件詳細說明中的泛型子類別的有限支援限制`NSObjects`搭配 Xamarin.iOS 7.2.1 導入。
    
### <a name="generic-type-arguments-in-member-signatures"></a>成員簽章中的泛型類型引數

公開給 Objective C 的成員簽章中的所有泛用型別引數必須具有`NSObject`條件約束。

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

**原因**:泛型類型參數是`NSObject`，因此選取器簽章`myMethod:`可以安全地公開以 OBJECTIVE-C (它一律是`NSObject`或它的子類別)。

**不正確**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 不可能的 OBJECTIVE-C 程式碼可以呼叫，因為簽章會有所不同的泛型類型的確切型別匯出的成員建立的 OBJECTIVE-C 簽章`T`。

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

**原因**： 您可不受限制地泛型類型引數，只要它們不會匯出的成員簽章的一部分。

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

**原因**: `T` Objective c 參數匯出`MyMethod`限制為`NSObject`，不受限制的型別`U`不是簽章的一部分。
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>從 OBJECTIVE-C 的泛型類型的具現化

不允許從 OBJECTIVE-C 的泛型類型具現化。 Managed 型別用於 xib 時，就會發生這種發生。

此類別定義中，公開 （expose） 的建構函式，請考慮`IntPtr`(建構的 Xamarin.iOS 方法C#物件的原生 OBJECTIVE-C 執行個體):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

上述建構沒問題，在執行階段時，這會擲回例外狀況在 OBJECTIVE-C 嘗試建立它的執行個體。

這是因為 Objective C 並沒有泛型型別的概念，而且不能指定確切的泛型型別來建立。

這個問題可以解決藉由建立泛型類型的特定子類別。   例如: 
    
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

現在有任何模稜兩可，類別`GenericUIView`可以用於 xib。

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

**原因**:這不允許因為 Xamarin.iOS 不知道哪種類型的類型引數使用`T`時從 OBJECTIVE-C 叫用方法。

替代方式是建立特製化的方法，並將其改為匯出：

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

如果裝載的泛型子類別內，您可以不公開以 OBJECTIVE-C 靜態成員`NSObject`。

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

**原因：** 就像一般方法一樣，Xamarin.iOS 執行階段必須能知道類型用於泛型類型引數 t。

比方說使用本身的執行個體的成員 (因為絕對不會執行個體的泛型<T>，它一律是泛型<SomeSpecificClass>)，但是對靜態成員這項資訊不存在。

請注意，這適用於即使有問題的成員不會以任何方式使用 T 型別引數。

在此情況下，替代方法是建立的特定子類別：

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

需要新的泛型支援[註冊系統](~/ios/internals/registrar.md)。

如果您嘗試使用舊遇到 （此外若要產生正確的程式碼，導致未定義的行為） 的泛型型別時，舊版的註冊系統會顯示警告。
    
## <a name="performance"></a>效能

靜態註冊機構無法解析在泛型型別匯出的成員在建置階段通常會，它必須在執行階段查閱。 這表示叫用這種方法從 OBJECTIVE-C 會稍微慢一點比叫用非泛型類別的成員。

