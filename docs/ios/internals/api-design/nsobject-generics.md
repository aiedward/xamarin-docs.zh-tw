---
title: 在 Xamarin 中 NSObject 的一般子類別
description: 本檔說明如何建立 NSObject 的建立一般子類別。 它會檢查可以和無法完成的作業、討論靜態註冊機構, 並查看效能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 2e84ec85fac933f579f961300d242bafa1f0b838
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226043"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>在 Xamarin 中 NSObject 的一般子類別

## <a name="using-generics-with-nsobjects"></a>使用泛型搭配 NSObjects

您可以在的`NSObject`子類別中使用泛型, 例如[UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

因為子類別`NSObject`已向目標-C 執行時間註冊的物件, 所以有一些對類型的`NSObject`泛型子類別可能會有一些限制。

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 一般子類別的考慮

本檔詳細說明一般子類別`NSObjects`的有限支援中的限制。

### <a name="generic-type-arguments-in-member-signatures"></a>成員簽章中的泛型型別引數

成員簽章中對目標-C 公開的所有泛型型別引數都`NSObject`必須有條件約束。

**好**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**:泛型型別參數是`NSObject`, 因此的選擇器`myMethod:`簽章可以安全地公開給目標-C (它一定會是`NSObject`或它的子類別)。

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

**原因**: 無法針對目標 c 程式碼可呼叫的匯出成員建立目標-c 簽章, 因為簽章會根據泛型型別`T`的確切類型而有所不同。

**好**:

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

**原因**: 可能會有不受限制的泛型型別引數, 但前提是它們不會包含匯出成員簽章的一部分。

**好**:

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

**原因**: 在`T`目標-C 中匯出`MyMethod`的參數會限制為`NSObject`, 不受限制的類型`U`不是簽章的一部分。

### <a name="instantiations-of-generic-types-from-objective-c"></a>從目標-C 的泛型型別具現化

不允許從目標-C 具現化泛型型別。 這通常會在 xib 或分鏡腳本中使用 managed 類型時發生。

請考慮這個類別定義, 它會公開採用 (從`IntPtr`原生目標-C 實例來建立C#物件的 Xamarin. iOS 方法) 的函式:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

雖然上述的結構正常, 但在執行時間, 這會擲回例外狀況 (如果目標為 C) 嘗試建立它的實例。

發生這種情況的原因是目標-C 沒有泛型型別的概念, 而且無法指定要建立的確切泛型型別。

藉由建立泛型型別的特殊子類別, 可以解決這個問題。 例如：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

現在沒有任何明確之處, 類別`GenericUIView`可以用於 xib 或分鏡腳本。

## <a name="no-support-for-generic-methods"></a>不支援泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允許泛型方法。

將不會編譯下列程式碼:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**:這是不允許的, 因為當從目標 C 叫用方法時, Xamarin 不知道`T`要在型別引數使用哪種型別。

另一個替代方式是建立特製化方法, 並改為匯出:

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

### <a name="no-exported-static-members-allowed"></a>不允許任何匯出的靜態成員

如果靜態成員裝載于的泛型子類別`NSObject`中, 您就不能將它公開至目標-C。

不支援的案例範例:

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

**原因**就像一般方法一樣, Xamarin iOS 執行時間必須能夠知道要用於泛型型別引數`T`的型別。

若是實例成員, 則會使用實例本身 (因為不會有實例`Generic<T>`, 它一律會是`Generic<SomeSpecificClass>`), 但對於靜態成員, 此資訊不存在。

請注意, 即使有問題的成員不會以任何方式使用型別`T`引數, 這也適用。

在此情況下, 替代方法是建立特製化子類別:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
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

## <a name="performance"></a>效能

靜態註冊機構無法在建立時解析泛型型別中的匯出成員, 因為它通常是在執行時間進行查閱。 這表示從目標-C 叫用這類方法, 會比叫用非泛型類別的成員稍微慢一點。

