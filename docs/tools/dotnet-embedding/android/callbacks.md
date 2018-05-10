---
title: 在 Android 上回呼
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3f18516643c00dc67fe533ecab00e1f415eb5c46
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="callbacks-on-android"></a>在 Android 上回呼

從 C# 呼叫 Java 有點*風險商務*。 也就是說沒有*模式*回呼從 C# java; 但是，所以比，我們都希望更為複雜。

我們會進行回呼，讓 Java 最適合的三個選項：

- 抽象類別
- 介面
- 虛擬方法

## <a name="abstract-classes"></a>抽象類別

這是最簡單的路由回呼，因此也建議您使用`abstract`如果只想要使用的最簡單的表單的回呼。

讓我們開始，我們都希望 Java 實作的 C# 類別：

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

以下是詳細資料以進行這項工作：

- `[Register]` 會產生好的封裝名稱 java-就會自動產生的封裝名稱，則沒有。
- 子類別化`Java.Lang.Object`訊號，以.NET 內嵌進行預演 Xamarin.Android 的 Java 產生器的類別。
- 空的建構函式： 您會想要從 Java 程式碼使用。
- `(IntPtr, JniHandleOwnership)` 建構函式： Xamarin.Android 將使用如 C# 中建立的對等的 Java 物件。
- `[Export]` 若要將方法公開至 Java Xamarin.Android 會發出信號。 由於 Java 世界喜歡使用大小寫的方法，我們也可以變更在方法名稱。

下一步我們把 C# 方法來測試案例：

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
`JavaCallbacks` 可能是任何類別，以測試這個，只要它是`Java.Lang.Object`。

現在，執行您的.NET 組件產生 AAR 的.NET 內嵌。 請參閱[入門指南](~/tools/dotnet-embedding/get-started/java/android.md)如需詳細資訊。

匯入之後 AAR 檔案至 Android Studio 中，我們要撰寫單元測試：

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
因此我們：

- 實作`AbstractClass`在 Java 中使用的匿名型別
- 確定我們的執行個體傳回`"Java"`從 Java
- 確定我們的執行個體傳回`"Java"`從 C#
- 加入`throws Throwable`，因為目前在使用會標示為 C# 建構函式 `throws`

如果我們以執行此單元測試做為是，它會這類失敗，發生錯誤：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

遺漏了什麼如下`Invoker`型別。 這是子類別的`AbstractClass`，轉送 Java 的 C# 呼叫。 如果 Java 物件輸入 C# 世界，而且相等的 C# 類型是抽象的則會自動尋找 C# 類型的後置詞 Xamarin.Android `Invoker` C# 程式碼中使用。

使用這項 Xamarin.Android `Invoker` Java 繫結專案，以及其他項目模式。

以下是我們實作`AbstractClassInvoker`:
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

沒有很多這裡，我們：

- 加入具有後置詞的類別`Invoker`該子類別 `AbstractClass`
- 加入`class_ref`來保存 JNI 參考 Java 類別的子類別 C# 類別
- 加入`id_gettext`來保存 java JNI`getText`方法
- 包含`(IntPtr, JniHandleOwnership)`建構函式
- 實作`ThresholdType`和`ThresholdClass`Xamarin.Android 了解詳細資料的需求 `Invoker`
- `GetText` 需要查閱 Java`getText`適當的 JNI 簽章的方法，並為它
- `Dispose` 只需要清除的參考 `class_ref`

加入這個類別，並產生新的 AAR 之後, 我們的單元測試階段。 您可以看到這個回呼的模式並不*理想*，但可行。

如需 Java interop 的詳細資訊，請參閱令人驚訝[Xamarin.Android 文件](~/android/platform/java-integration/working-with-jni.md)有關這個主題。

## <a name="interfaces"></a>介面

介面是抽象類別，除了一個詳細資料與大致相同： Xamarin.Android 不會將 Java 產生它們。 這是因為.NET 內嵌之前, 有案例不多 Java 就會在其中實作的 C# 介面。

例如，假設我們有下列 C# 介面：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` 對.NET 內嵌發出訊號，這是 Xamarin.Android 介面，但否則這是完全相同`abstract`類別。

因為 Xamarin.Android 將目前產生此介面的 Java 程式碼，加入下列 Java 加入 C# 專案：

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

您可以將檔案放在任何地方，但請務必將其建置動作設定為`AndroidJavaSource`。 這會將其複製到適當的目錄取得編譯至 AAR 檔案內嵌.NET 發出信號。

下一步`Invoker`實作將會完全相同：

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

在產生 AAR 檔案之後, 在 Android Studio 我們可以撰寫下列傳遞單元測試：

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

覆寫`virtual`在 Java 中是，但不是好的體驗。

例如，假設您有下列 C# 類別：

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

如果您遵循`abstract`類別上述範例中，它能除了一個詳細資料： _Xamarin.Android 不查閱`Invoker`_ 。

若要修正此問題，修改 C# 類別是`abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

這不是理想的做法，但是它會取得此案例中運作。 Xamarin.Android 拾取`VirtualClassInvoker`Java 並用`@Override`方法上。

## <a name="callbacks-in-the-future"></a>在未來的回呼

有幾件事我們無法執行改善這些案例：

1. `throws Throwable` 在此建構函式固定在 C# [PR](https://github.com/xamarin/java.interop/pull/170)。
1. 請支援的介面中 Xamarin.Android Java 產生器。
    - 這會移除新增的建置動作的 Java 原始程式檔的需求`AndroidJavaSource`。
1. 請讓 Xamarin.Android 載入`Invoker`虛擬類別。
    - 這會移除標記中的類別需要我們`virtual`範例`abstract`。
1. 產生`Invoker`自動內嵌.NET 類別
    - 這將會很複雜，但可行。 Xamarin.Android 已正在進行類似的 Java 繫結專案。

大量的工作完成，但可能會有這些.NET 內嵌的增強功能。

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上開始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
