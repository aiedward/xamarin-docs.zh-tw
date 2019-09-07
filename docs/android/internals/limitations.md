---
title: Xamarin. Android 與Mono 執行時間中的桌面差異
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 7f98f2f75a106ad3a9f62256a7145ac746c4b1c8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757777"
---
# <a name="limitations"></a>限制

因為 Android 上的應用程式需要在建立過程中產生 JAVA proxy 類型，所以無法在執行時間產生所有程式碼。

以下是相較于桌面 Mono 的 Xamarin Android 限制：

## <a name="limited-dynamic-language-support"></a>有限的動態語言支援

 當 Android 執行時間需要叫用 managed 程式碼時，就需要 Android 可呼叫[包裝](~/android/platform/java-integration/android-callable-wrappers.md)函式。 Android 可呼叫包裝函式會在編譯時期根據 IL 的靜態分析來產生。 結果的最後一項：您*無法*在需要 JAVA 類型子類別化的任何情況下（包括間接子類別化）使用動態語言（IronPython、IronRuby 等），因為在編譯時期無法將這些動態類型解壓縮到產生必要的 Android 可呼叫包裝函式。

## <a name="limited-java-generation-support"></a>有限的 JAVA 產生支援

您必須產生 Android 可呼叫[包裝](~/android/platform/java-integration/android-callable-wrappers.md)函式，JAVA 程式碼才能呼叫 managed 程式碼。 *根據預設*，Android 可呼叫包裝函式只會包含（特定）宣告的函式和方法，以覆寫虛擬 JAVA [`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)方法（也就是它具有）或實作為`Attribute`JAVA 介面方法（介面同樣具有）。
  
在4.1 版之前，不會宣告任何其他方法。 使用4.1 版本時， [ `Export` `ExportField`和自訂屬性可以用來宣告 Android 可呼叫包裝函式中的 JAVA 方法和欄位](~/android/platform/java-integration/working-with-jni.md)。

### <a name="missing-constructors"></a>遺漏的析構函式

除非使用，否則[`ExportAttribute`](xref:Java.Interop.ExportAttribute) ，處理函式會保持不變。 產生 Android 可呼叫包裝函式的演算法，是在下列情況發出 JAVA 的程式：

1. 所有參數類型都有 JAVA 對應
2. 基類宣告相同&ndash;的函式，這是必要的，因為 Android 可呼叫包裝函式*必須*叫用對應的基類檢查程式; 不能使用任何預設引數（因為沒有簡單的方法可以判斷哪些值應該在 JAVA 中使用）。

例如，請參考下列類別：

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

雖然這看起來完全合乎邏輯，在*發行組建中*產生的 Android 可呼叫包裝函式將不會包含預設的函式。 因此，如果您嘗試啟動此服務（例如[`Context.StartService`](xref:Android.Content.Context.StartService*)，它將會失敗：

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

因應措施是宣告預設的處理函式，並使用`ExportAttribute`來裝飾它， [`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString)然後設定： 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```

### <a name="generic-c-classes"></a>泛型C#類別

只有C#部分支援泛型類別。 有下列限制：

- 泛型型別可能不會`[Export]`使用`[ExportField`或]。 嘗試這麼做會產生`XA4207`錯誤。

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

- 泛型方法可能不會`[Export]`使用`[ExportField]`或：

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

- `[ExportField]`不能用於傳回的`void`方法：

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

- 泛型型別的實例_不得_從 JAVA 程式碼建立。
    只能從 managed 程式碼安全地建立它們：

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```

## <a name="partial-java-generics-support"></a>部分 JAVA 泛型支援

JAVA 泛型系結支援有限。 特別是，從另一個泛型（非具現化）類別衍生的泛型實例類別中的成員，會被遺留成以 JAVA. Lang.ini 物件的形式公開。 例如， [GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*)方法會傳回 JAVA. lang.ini 物件。 這是因為已清除的 JAVA 泛型。
我們有一些類別不會套用這項限制，但會以手動方式調整。

## <a name="related-links"></a>相關連結

- [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [SuperString](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [RegisterAttribute](xref:Android.Runtime.RegisterAttribute)
