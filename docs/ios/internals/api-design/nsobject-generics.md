---
title: Xamarin.iOS 中 NSObject 的通用子類別
description: 本文件介紹如何創建 NSObject 的泛型子類。 它檢查可以做什麼和不能做什麼,討論靜態註冊器,並查看性能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022369"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Xamarin.iOS 中 NSObject 的通用子類別

## <a name="using-generics-with-nsobjects"></a>將泛型與 NS 物件一起使用

可以在的子類別使用泛型`NSObject`,例如[UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

由於子類`NSObject`的物件在Objective-C運行時註冊,因此類型泛`NSObject`型子類的可能內容存在一些限制。

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 的泛型子類別的注意事項

本文檔詳細介紹了 對的通用子類的有限`NSObjects`支援中 的限制。

### <a name="generic-type-arguments-in-member-signatures"></a>成員簽章的一般型態參數

向 Objective-C 公開的成員簽名中的所有泛型類型參數都`NSObject`必須具有約束。

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

**原因**:泛型類型參數`NSObject`是 ,`myMethod:`因此 選擇 者簽名可以安全地公開到 Objective-C(它永遠是`NSObject`或其子類)。

**壞**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**:無法為匯出的成員創建 Objective-C 簽名,而目標 C 代碼可以調用該簽名,因為簽名`T`會因泛型類型 的確切類型而異。

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

**原因**:只要不參與導出的成員簽名,就可以具有無約束泛型類型參數。

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

**原因**:匯`T`出 的 Objective-C`MyMethod`中的`NSObject`參數 被限制`U`為 ,無約束類型 不是簽名的一部分。

### <a name="instantiations-of-generic-types-from-objective-c"></a>從目標C的泛型類型的實體化

不允許從目標 C 實例化泛型類型。 當在 xib 或情節提要中使用託管類型時,通常會發生這種情況。

請考慮此類定義,該定義公開採用`IntPtr`的建構函數(Xamarin.iOS 方式從本機 Objective-C 實例建構 C# 物件):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

雖然上述構造正常,但運行時,如果Objective-C嘗試創建它的實例,這將引發異常。

這是因為 Objective-C 沒有泛型類型的概念,並且無法指定要創建的確切泛型類型。

此問題可以通過創建泛型類型的專用子類來解決此問題。 例如：

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

現在不再有歧義,類`GenericUIView`可以在 xibs 或情節提要中使用。

## <a name="no-support-for-generic-methods"></a>不支援泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允許使用通用方法。

以下代碼不會編譯:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**:這是不允許的,因為當從 Objective-C 調用方法時,Xamarin.iOS`T`不知道要使用哪種類型用於類型參數。

另一種方法是創建專用方法並匯出,而不是:

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

### <a name="no-exported-static-members-allowed"></a>不要匯出的靜態成員

如果靜態成員託管在 的`NSObject`泛型子類中,則無法將其公開給 Objective-C。

不支援的方案範例:

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

**原因:** 與泛型方法一樣,Xamarin.iOS 運行時需要能夠知道泛型類型`T`參數 使用哪種類型。

對於實例成員,實例本身被使用(因為永遠不會有實例`Generic<T>`,它將永遠`Generic<SomeSpecificClass>`是 ),但對於靜態成員,此資訊不存在。

請注意,即使相關成員不以任何方式使用類型參數`T`,這也會適用。

在這種情況下,另一種方法是創建專用子類:

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

靜態註冊器無法像通常那樣在生成時解析泛型類型的匯出成員,因此必須在運行時將其向上查看。 這意味著從 Objective-C 調用此類方法比從非泛型類調用成員稍微慢一些。
