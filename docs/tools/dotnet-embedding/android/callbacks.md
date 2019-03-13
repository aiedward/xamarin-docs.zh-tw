---
title: 在 Android 上的回撥
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117154"
---
# <a name="callbacks-on-android"></a>在 Android 上的回撥

呼叫以從 JavaC#稍微*具風險性的企業*。 也就是說沒有*圖樣*回呼，從C#java; 不過，它是超出我們想要更複雜。

我們將討論三個選項來執行適用於 Java 的最適合的回呼：

- 抽象類別
- 介面
- 虛擬方法

## <a name="abstract-classes"></a>抽象類別

這是最簡便的途徑，回呼，因此我會建議使用`abstract`如果只想要取得使用中的最簡單形式的回呼。

讓我們開始C#我們想要實作的 Java 類別：

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

以下是要進行這項工作的詳細資料：

- `[Register]` 產生不錯的封裝名稱在 Java 中，您會自動產生的封裝名稱，而不需要它。
- 子類別化`Java.Lang.Object`.NET 內嵌的訊號，來執行 Xamarin.Android 的 Java 產生器類別。
- 空的建構函式： 是您會想要使用 Java 程式碼。
- `(IntPtr, JniHandleOwnership)` 建構函式： Xamarin.Android 會使用建立C#-Java 物件相等。
- `[Export]` 表示方法公開至 Java 的 Xamarin.Android。 我們也可以變更方法名稱，因為 Java 世界喜歡使用大小寫的方法。

下一步 讓C#方法來測試案例：

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
`JavaCallbacks` 可能是任何類別，以進行測試，只要它是`Java.Lang.Object`。

現在，執行.NET 內嵌在您的.NET 組件，以產生 AAR 上。 請參閱[快速入門指南](~/tools/dotnet-embedding/get-started/java/android.md)如需詳細資訊。

AAR 檔案匯入 Android Studio 中之後, 我們要撰寫的單元測試：

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

- 實作`AbstractClass`在 Java 中使用匿名型別
- 確定我們的執行個體傳回`"Java"`從 Java
- 確定我們的執行個體傳回`"Java"`從C#
- 新增`throws Throwable`，因為C#建構函式目前標記為 `throws`

如果我們執行此單元測試做為是，它會這類失敗，發生錯誤：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

遺漏的項目如下`Invoker`型別。 這是子類別的`AbstractClass`，將轉送C#呼叫到 Java。 如果 Java 物件進入C#世界與對等C#型別是抽象的則會自動尋找 Xamarin.AndroidC#後置詞的型別`Invoker`以便使用於C#程式碼。

Xamarin.Android 會使用此`Invoker`Java 繫結專案，以及其他項目模式。

以下是我們的實作`AbstractClassInvoker`:
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

有一點相當在這裡，我們：

- 加入具有後置詞的類別`Invoker`子類別化 `AbstractClass`
- 新增`class_ref`來保存子類別化的 Java 類別的 JNI 參考我們C#類別
- 新增`id_gettext`來保存到 Java JNI`getText`方法
- 包含`(IntPtr, JniHandleOwnership)`建構函式
- 實作`ThresholdType`和`ThresholdClass`為 Xamarin.Android 來了解詳細資料的需求 `Invoker`
- `GetText` 需要查閱 Java`getText`具有適當的 JNI 簽章方法，並為它
- `Dispose` 只需要清除的參考 `class_ref`

新增此類別並產生新的 AAR 檔案之後, 我們的單元測試通過。 您可以看到此回呼的模式不是*理想*，但可行。

如需 Java interop 的詳細資訊，請參閱優越[Xamarin.Android 文件](~/android/platform/java-integration/working-with-jni.md)有關這個主題。

## <a name="interfaces"></a>介面

介面是抽象類別，除了一個詳細資料與大致相同： Xamarin.Android 不會為其產生 Java。 這是因為之前.NET 內嵌，案例並不多，Java 會實作C#介面。

假設我們有下列C#介面：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` 這是 Xamarin.Android 的介面，但否則這是完全相同，表示.NET 內嵌`abstract`類別。

因為 Xamarin.Android 將目前產生此介面的 Java 程式碼，加入下列 Java 程式C#專案：

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

您可以將檔案放在任何位置，但請務必將其建置動作設定為`AndroidJavaSource`。 .NET 內嵌，將它複製到適當的目錄，以取得編譯成 AAR 檔案，這將會發出信號。

接下來，`Invoker`實作會完全相同：

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

產生 AAR 檔案之後，在 Android Studio，我們可以撰寫下列傳遞單元測試：

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

覆寫`virtual`在 Java 中是的但不是很好的經驗。

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

如果您遵循`abstract`這個辦法行得通類別上述範例中，除了一個詳細資料： _Xamarin.Android 無法查閱`Invoker`_ 。

若要修正此問題，修改C#類別`abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

這不是理想的做法，但它會取得這個案例順利運作。 Xamarin.Android 會挑選`VirtualClassInvoker`，而且可以使用 Java`@Override`方法上。

## <a name="callbacks-in-the-future"></a>在未來的回呼

有幾件事我們可以進行改善這些案例：

1. `throws Throwable` 在C#建構函式會固定這[PR](https://github.com/xamarin/java.interop/pull/170)。
1. 請 Java 產生器在 Xamarin.Android 中支援的介面。
    - 這可免除新增 Java 原始程式檔的建置動作`AndroidJavaSource`。
1. 載入 Xamarin.Android 放置`Invoker`虛擬類別。
    - 這可免除將類別中的標示我們`virtual`範例`abstract`。
1. 產生`Invoker`自動類別的.NET 內嵌
    - 這將會很複雜，但可行。 Xamarin.Android 已經進行的類似 Java 繫結專案。

有很多，完成的工作，但可能會有.NET 內嵌這些增強功能。

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上開始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
