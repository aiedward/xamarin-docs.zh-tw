---
title: Xamarin.Android vs。桌面： 在 Mono 執行階段中的差異
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 115d715214d7af3174c41d9d82e894ce429dab42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953340"
---
# <a name="limitations"></a>限制

在 Android 上的應用程式需要在建置流程期間產生 Java proxy 型別，因為它不可能產生在執行階段的所有程式碼。

相較於 Mono 桌面的 Xamarin.Android 限制如下：


## <a name="limited-dynamic-language-support"></a>有限的動態語言支援

 [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)所需的任何 Android 執行階段需要叫用 managed 程式碼的時間。 Android 可呼叫包裝函式會在編譯時期，根據產生的 IL 的靜態分析。 這個最後的結果： 您*無法*做為動態語言 （IronPython、 IronRuby 等） 在任何情況下 （包括間接子類別化），需要子類別化的 Java 類型的位置沒有擷取這些動態類型的方法在編譯時期產生必要 Android 可呼叫包裝函式。


## <a name="limited-java-generation-support"></a>產生的有限的 Java 支援

[Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)需要 Java 程式碼來呼叫 managed 程式碼的順序來產生。 *依預設*，Android 可呼叫包裝函式只會包含 （特定） 的宣告建構函式和方法會覆寫虛擬的 Java 方法 (也就是它有[ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) 或實作的 Java 介面方法 （介面同樣具有`Attribute`)。
  
4.1 發行之前，可以不宣告任何額外的方法。 4.1 版本中， [`Export`並`ExportField`自訂屬性可以用來宣告 Java 方法和 Android 的可呼叫包裝函式內的欄位](~/android/platform/java-integration/working-with-jni.md)。

### <a name="missing-constructors"></a>遺漏的建構函式

建構函式仍需要一些技巧，除非[ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute)用。 產生 Android 可呼叫包裝函式建構函式的演算法是如果將會發出 Java 建構函式：

1. 所有參數類型的 Java 都對應
2. 基底類別宣告相同的建構函式&ndash;這是必要的因為 Android 可呼叫包裝函式*必須*叫用對應的基底類別建構函式; （因為不容易，就可以使用任何預設引數判斷值應該是在 Java 中使用)。

例如，請參考下列類別：

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

雖然這看起來完全邏輯，產生 Android 可呼叫包裝函式*發行組建中*將不會包含預設建構函式。 因此，如果您嘗試啟動此服務 (例如[ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/))，它將會失敗：

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

因應措施是宣告預設建構函式，它與裝飾`ExportAttribute`，並設定[ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

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

泛型C#類別僅提供部分支援。 有下列限制：


-   不得使用泛型型別`[Export]`或`[ExportField`]。 嘗試執行此作業會產生`XA4207`時發生錯誤。

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

-   泛型方法不能使用`[Export]`或`[ExportField]`:

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

-   `[ExportField]` 傳回方法不會用於`void`:

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

-   泛型型別的執行個體_不得_從 Java 程式碼建立。
    它們只安全地建立從 managed 程式碼：

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


## <a name="partial-java-generics-support"></a>泛型的部分的 Java 支援

Java 繫結的泛型支援僅限於。 特別是，一般的執行個體類別衍生自另一個的泛型 （非具現化） 類別中的成員則會保留公開為 Java.Lang.Object。 例如， [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/)方法會傳回 Java.Lang.Object。 這是因為它們的 Java 泛型。
我們有一些類別，不會套用這項限制，但它們會以手動方式進行調整。


## <a name="related-links"></a>相關連結

- [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
