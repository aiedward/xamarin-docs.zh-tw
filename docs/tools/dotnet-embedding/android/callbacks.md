---
title: Android 上的回呼
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8f32da34c82e46fa4afd69ae420b314eab18b235
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889350"
---
# <a name="callbacks-on-android"></a>Android 上的回呼

從C#呼叫 JAVA 是有點危險的*業務*。 也就是說, 有一種*模式*可從C#到 JAVA 的回呼;不過, 它比我們想要的更為複雜。

我們將涵蓋三個選項, 可讓您針對 JAVA 進行最合理的回呼:

- 抽象類別
- 介面
- 虛擬方法

## <a name="abstract-classes"></a>抽象類別

這是回呼的最簡單路由, 因此, 如果您只`abstract`是嘗試讓回呼以最簡單的形式運作, 建議使用。

讓我們從想要C#執行 JAVA 的類別開始:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

以下是完成此工作的詳細資料:

- `[Register]`在 JAVA 中產生不錯的套件名稱--您會在沒有它的情況下取得自動產生的套件名稱。
- 子類別化指示 .net 內嵌, 以透過 Xamarin. Android 的 JAVA 產生器來執行類別。 `Java.Lang.Object`
- 空白的函式: 這是您想要從 JAVA 程式碼使用的功能。
- `(IntPtr, JniHandleOwnership)`函C#式: 這是 Xamarin Android 將用來建立對等的 JAVA 物件。
- `[Export]`通知 Xamarin. Android 將方法公開至 JAVA。 我們也可以變更方法名稱, 因為 JAVA world 喜歡使用小寫方法。

接下來, 我們要C#做一個方法來測試案例:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```

`JavaCallbacks`可以是任何要測試的`Java.Lang.Object`類別, 只要它是。

現在, 請在您的 .NET 元件上執行 .NET 內嵌, 以產生 AAR。 如需詳細資訊, 請參閱[消費者入門指南](~/tools/dotnet-embedding/get-started/java/android.md)。

將 AAR 檔案匯入 Android Studio 之後, 讓我們撰寫單元測試:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```

所以我們:

- `AbstractClass`以匿名型別在 JAVA 中執行
- 確定我們的實例從`"Java"` JAVA 返回
- 確定我們`"Java"`的實例會從C#
- 已`throws Throwable`新增, C#因為目前已將函式標記為`throws`

如果我們依原本執行此單元測試, 則會失敗並出現如下的錯誤:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

此處遺漏的是一`Invoker`種類型。 這是將呼叫轉送`AbstractClass` C#至 JAVA 的子類別。 如果 JAVA C#物件進入世界, 而對等C#類型是 abstract, 則 Xamarin 會自動尋找具有C# `Invoker`後置字元的類型, 以便在程式碼中C#使用。

Xamarin 會針對 JAVA 系`Invoker`結專案使用此模式, 以及其他事項。

以下是我們的`AbstractClassInvoker`執行方式:

```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

這裡有很多的進展, 我們:

- 新增類別, 其中包含子`Invoker`類別的尾碼`AbstractClass`
- 已`class_ref`新增, 以保存類別的子類別的 JNI 參考C#
- 已`id_gettext`新增以保存 JAVA `getText`方法的 JNI 參考
- 包含一個`(IntPtr, JniHandleOwnership)`函式
- 已實`ThresholdClass`作為 Xamarin 的需求,以瞭解其相關詳細資料`ThresholdType``Invoker`
- `GetText`需要使用適當的 JNI `getText`簽章查閱 JAVA 方法並呼叫它
- `Dispose`只需要清除的參考`class_ref`

加入這個類別並產生新的 AAR 之後, 我們的單元測試就會通過。 如您所見, 這種回呼模式並不*理想*, 但雖可行。

如需有關 JAVA interop 的詳細資訊, 請參閱此主題的絕佳[Xamarin. Android 檔](~/android/platform/java-integration/working-with-jni.md)。

## <a name="interfaces"></a>介面

介面與抽象類別別非常類似, 但有一個詳細資料:Xamarin 不會為它們產生 JAVA。 這是因為在 .NET 內嵌之前, JAVA 會執行C#介面的情況並不多。

假設我們有下列C#介面:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject`向 .net 內嵌發出信號, 表示這是 Xamarin 的 Android 介面, 否則這與`abstract`類別完全相同。

因為 Xamarin 目前不會產生此介面的 JAVA 程式碼, 請將下列 JAVA 新增至您C#的專案:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

您可以將檔案放在任何位置, 但請務必將其組建動作`AndroidJavaSource`設為。 這會指示 .NET 內嵌將它複製到適當的目錄, 以編譯成您的 AAR 檔案。

接下來, `Invoker`這會是完全相同的執行方式:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

產生 AAR 檔案之後, 在 Android Studio 我們可以撰寫下列通過單元測試:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>虛擬方法

`virtual`在 JAVA 中覆寫是可行的, 但不是絕佳的體驗。

假設您有下列C#類別:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

如果您遵循上述`abstract`的類別範例, 則除了一個詳細資料之外, 它還可以使用:_Xamarin 不會查閱`Invoker`_ 。

若要修正此問題, C#請將`abstract`類別修改為:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

這不是理想的做法, 但可讓此案例運作。 Xamarin 會挑選`VirtualClassInvoker` , 而 JAVA 可以在方法上使用`@Override` 。

## <a name="callbacks-in-the-future"></a>未來的回呼

我們可以做幾件事來改善這些案例:

1. `throws Throwable`在C#此[PR](https://github.com/xamarin/java.interop/pull/170)上已修正 on 的函式。
1. 將 JAVA 產生器設為 Xamarin. Android 支援介面。
    - 這不需要新增具有 build 動作的`AndroidJavaSource`JAVA 原始程式檔。
1. 提供一個方法, 讓 Xamarin Android 載入`Invoker`虛擬類別的。
    - 這不需要在我們`virtual`的範例`abstract`中標記類別。
1. 自動`Invoker`產生 .net 內嵌的類別
    - 這會變得很複雜, 但雖可行。 Xamarin 已針對 JAVA 系結專案執行類似于這項操作。

這裡有很多工作可以完成, 但是 .NET 內嵌的增強功能也是可行的。

## <a name="further-reading"></a>進一步閱讀

* [Android 上的消費者入門](~/tools/dotnet-embedding/get-started/java/android.md)
* [初稿 Android Research](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
