---
title: Android 上的回呼
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: f23f155a02422a3d04a0b14b282929ea63d60765
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007296"
---
# <a name="callbacks-on-android"></a>Android 上的回呼

從C#呼叫 JAVA 是有點危險的*業務*。 也就是說，有一種*模式*可從C#到 JAVA 的回呼;不過，它比我們想要的更為複雜。

我們將涵蓋三個選項，可讓您針對 JAVA 進行最合理的回呼：

- 抽象類別
- 介面
- 虛擬方法

## <a name="abstract-classes"></a>抽象類別

這是回呼的最簡單路由，因此，如果您只想要以最簡單的形式來取得回呼，建議使用 `abstract`。

讓我們從想要C#執行 JAVA 的類別開始：

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

以下是完成此工作的詳細資料：

- `[Register]` 會在 JAVA 中產生不錯的套件名稱--您會在沒有它的情況下取得自動產生的套件名稱。
- 子類別化 `Java.Lang.Object` 會向 .NET 內嵌發出信號，以透過 Xamarin. Android 的 JAVA 產生器來執行類別。
- 空白的函式：這是您想要從 JAVA 程式碼使用的功能。
- `(IntPtr, JniHandleOwnership)` 的函C#式：這是 Xamarin Android 將用來建立對等的 JAVA 物件。
- `[Export]` 會通知 Xamarin. Android 將方法公開至 JAVA。 我們也可以變更方法名稱，因為 JAVA world 喜歡使用小寫方法。

接下來，我們要C#做一個方法來測試案例：

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

`JavaCallbacks` 可以是任何要測試的類別，只要它是 `Java.Lang.Object`就可以了。

現在，請在您的 .NET 元件上執行 .NET 內嵌，以產生 AAR。 如需詳細資訊，請參閱[消費者入門指南](~/tools/dotnet-embedding/get-started/java/android.md)。

將 AAR 檔案匯入 Android Studio 之後，讓我們撰寫單元測試：

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

所以我們：

- 以匿名型別在 JAVA 中執行 `AbstractClass`
- 確定我們的實例會從 JAVA 傳回 `"Java"`
- 確定我們的實例會從傳回 `"Java"`C#
- 已新增 `throws Throwable`， C#因為目前已將函式標示為 `throws`

如果我們依原本執行此單元測試，則會失敗並出現如下的錯誤：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

此處遺漏的是 `Invoker` 類型。 這是將呼叫轉送C#至 JAVA 的 `AbstractClass` 子類別。 如果 JAVA C#物件進入世界，而對等C#類型是 abstract，則 Xamarin 會自動尋找具有後置字元C#的類型，`Invoker`在程式碼中C#使用。

Xamarin 會針對 JAVA 系結專案使用這種 `Invoker` 模式。

以下是我們的 `AbstractClassInvoker`的執行：

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

這裡有很多的進展，我們：

- 已新增類別，其後綴 `Invoker` 子類別 `AbstractClass`
- 已新增 `class_ref`，以保留子類別的 JAVA 類別C#的 JNI 參考
- 已新增 `id_gettext` 以保存 JAVA `getText` 方法的 JNI 參考
- 包含 `(IntPtr, JniHandleOwnership)` 的構造函式
- 實作為 Xamarin 的需求，`ThresholdType` 和 `ThresholdClass` 瞭解 `Invoker` 的詳細資料
- `GetText` 需要使用適當的 JNI 簽章查閱 JAVA `getText` 方法並加以呼叫
- `Dispose` 只需要清除 `class_ref` 的參考

加入這個類別並產生新的 AAR 之後，我們的單元測試就會通過。 如您所見，這種回呼模式並不*理想*，但雖可行。

如需有關 JAVA interop 的詳細資訊，請參閱此主題的絕佳[Xamarin. Android 檔](~/android/platform/java-integration/working-with-jni.md)。

## <a name="interfaces"></a>介面

介面與抽象類別別非常類似，但有一個詳細資料： Xamarin。 Android 不會為它們產生 JAVA。 這是因為在 .NET 內嵌之前，JAVA 會執行C#介面的情況並不多。

假設我們有下列C#介面：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` 通知 .NET 內嵌，這是一種 Xamarin 的 Android 介面，否則這與 `abstract` 類別完全相同。

因為 Xamarin 目前不會產生此介面的 JAVA 程式碼，請將下列 JAVA 新增至您C#的專案：

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

您可以將檔案放在任何位置，但請務必將其組建動作設定為 `AndroidJavaSource`。 這會指示 .NET 內嵌將它複製到適當的目錄，以編譯成您的 AAR 檔案。

接下來，`Invoker` 的執行會相當相同：

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

產生 AAR 檔案之後，在 Android Studio 我們可以撰寫下列通過單元測試：

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

在 JAVA 中覆寫 `virtual` 是可行的，但並不是絕佳的體驗。

假設您有下列C#類別：

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

如果您遵循上述的 `abstract` 類別範例，則除了一個詳細資料之外，它仍可正常執行： _Xamarin 不會查閱 `Invoker`_ 。

若要修正此問題， C#請將類別修改為`abstract`：

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

這不是理想的做法，但可讓此案例運作。 Xamarin 會挑選 `VirtualClassInvoker`，而 JAVA 可以在方法上使用 `@Override`。

## <a name="callbacks-in-the-future"></a>未來的回呼

我們可以做幾件事來改善這些案例：

1. 在此C# [PR](https://github.com/xamarin/java.interop/pull/170)上修正了對函式的 `throws Throwable`。
1. 將 JAVA 產生器設為 Xamarin. Android 支援介面。
    - 這不需要新增具有 `AndroidJavaSource`之組建動作的 JAVA 原始程式檔。
1. 建立 Xamarin 的方法，以載入虛擬類別的 `Invoker`。
    - 這不需要在我們的 `virtual` 範例 `abstract`中標記類別。
1. 自動產生適用于 .NET 內嵌的 `Invoker` 類別
    - 這會變得很複雜，但雖可行。 Xamarin 已針對 JAVA 系結專案執行類似于這項操作。

這裡有很多工作可以完成，但是 .NET 內嵌的增強功能也是可行的。

## <a name="further-reading"></a>進一步閱讀

- [Android 上的消費者入門](~/tools/dotnet-embedding/get-started/java/android.md)
- [初稿 Android Research](~/tools/dotnet-embedding/android/index.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
