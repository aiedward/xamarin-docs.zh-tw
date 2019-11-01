---
title: 架構
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: fe0903eca5c907fc104728ca0ad7c676a45a5180
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027916"
---
# <a name="architecture"></a>架構

[Xamarin]： Android 應用程式會在 Mono 執行環境中執行。
這個執行環境會與 Android 執行時間（美工）虛擬機器並存執行。 這兩個執行時間環境會在 Linux 核心之上執行，並向使用者程式碼公開各種 Api，讓開發人員能夠存取基礎系統。 Mono 執行時間是以 C 語言撰寫。

您可以使用[System](xref:System)、 [System.IO](xref:System.IO)、 [System.Net](xref:System.Net)和其他 .net 類別庫，來存取基礎 Linux 作業系統設施。

在 Android 上, 大部分的系統裝置 (例如音訊、圖形、OpenGL 和電話語音) 都無法直接提供給原生應用程式使用, 只會透過位於其中一個[JAVA](xref:Java.Lang) * 命名空間或 [Android](xref:Android) * 命名空間的 android 執行時間 JAVA api 來公開。 架構大致如下：

[核心上方和 .NET/JAVA + 系結之下的 Mono 和美工 ![圖](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin。 Android 開發人員可存取作業系統中的各種功能，方法是呼叫他們知道的 .NET Api （適用于低層級存取），或使用 Android 命名空間中公開的類別，以提供橋接器給所公開的 JAVA ApiAndroid 執行時間。

如需 Android 類別如何與 Android 執行時間類別通訊的詳細資訊，請參閱[API 設計](~/android/internals/api-design.md)檔。

## <a name="application-packages"></a>應用程式套件

Android 應用程式套件是具有副檔名*apk*的 ZIP 容器。 Xamarin Android 應用程式套件與一般 Android 套件具有相同的結構和配置，並新增下列專案：

- 應用程式元件（包含 IL）會以未壓縮的形式*儲存*在 [*元件*] 資料夾內。 在發行組建中的進程啟動期間， *apk*會在進程中*mmap （）* ed，而且會從記憶體載入元件。 這允許更快速的應用程式啟動，因為元件不需要在執行之前先解壓縮。  
- *注意：* 元件位置資訊，例如[元件。位置](xref:System.Reflection.Assembly.Location)和[元件。程式碼基](xref:System.Reflection.Assembly.CodeBase)底*無法依賴*發行組建。 它們不會以不同的檔案系統專案的形式存在，而且沒有可用的位置。

- 包含 Mono 執行時間的原生程式庫會出現在*apk*中。 Xamarin Android 應用程式必須包含所需/目標 Android 架構的原生程式庫，例如*armeabi* 、 *armeabi-armeabi-v7a* 、 *x86* 。 Xamarin Android 應用程式無法在平臺上執行，除非它包含適當的執行時間程式庫。

Xamarin Android 應用程式也包含*android*可呼叫包裝函式，可讓 android 呼叫 managed 程式碼。

## <a name="android-callable-wrappers"></a>Android 可呼叫包裝函式

- **Android**可呼叫包裝函式是一種[JNI](https://en.wikipedia.org/wiki/Java_Native_Interface)橋接器，會在 Android 執行時間需要叫用 managed 程式碼時使用。 Android 可呼叫包裝函式是如何覆寫虛擬方法，以及如何實作為 JAVA 介面。 如需詳細資訊，請參閱[JAVA 整合總覽](~/android/platform/java-integration/index.md)檔。

<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>受控可呼叫包裝函式

受控可呼叫包裝函式是一種 JNI 橋接器，會在受控碼需要叫用 Android 程式碼，並提供覆寫虛擬方法和執行 JAVA 介面的支援時使用。 整個[Android](xref:Android). * 和相關的命名空間是透過[.jar](~/android/platform/binding-java-library/index.md)系結所產生的受控可呼叫包裝函式。
Managed 可呼叫包裝函式會負責在 managed 和 Android 類型之間進行轉換，以及透過 JNI 叫用基礎 Android 平臺方法。

每個建立的受控可呼叫包裝函式都會保存 JAVA 全域參考，這可透過[IJAVAObject](xref:Android.Runtime.IJavaObject.Handle)屬性來存取。 全域參考是用來提供 JAVA 實例和受控實例之間的對應。 全域參考是有限的資源：模擬器一次只允許有2000個全域參考，而大部分的硬體允許一次有超過52000個全域參考存在。

若要追蹤全域參考的建立和終結時間，您可以將 [ [debug .log](~/android/troubleshooting/index.md)系統] 屬性設定為包含[grf](~/android/troubleshooting/index.md)。

藉由呼叫 managed 可呼叫包裝函式上的[JAVA. lang.ini （）](xref:Java.Lang.Object.Dispose) ，即可明確釋放全域參考。 這會移除 JAVA 實例和受控實例之間的對應，並允許收集 JAVA 實例。 如果從 managed 程式碼重新存取 JAVA 實例，將會為它建立新的受控可呼叫包裝函式。

處置 Managed 可呼叫包裝函式時，如果實例可以不小心線上程之間共用，則必須特別小心，因為處置實例會影響其他執行緒的參考。 為了達到最高的安全性，只有透過 `new` 或您知道一律配置新實例的方法，*或*從您*已知*會造成執行緒之間意外實例共用的方法所 `Dispose()` 的實例。

## <a name="managed-callable-wrapper-subclasses"></a>Managed 可呼叫包裝函式子類別

Managed 可呼叫包裝函式子類別是指所有「有趣」的應用程式特定邏輯可能會存留的地方。 其中包括自訂的[Android 應用程式](xref:Android.App.Activity)子類別（例如預設專案範本中的[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)類型）。 （具體而言，這些是*不*包含[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自訂屬性或[RegisterAttribute](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)的任何*JAVA lang.ini 物件*子類別。 DoNotGenerateAcw 為*false*，這是預設值）。

就像受控可呼叫包裝函式一樣，managed 可呼叫包裝函式子類別也包含全域參考，可以透過- [lang.ini](xref:Java.Lang.Object.Handle)屬性來存取。 就像受控可呼叫包裝函式一樣，全域參考可以藉由呼叫[JAVA. lang.ini （）](xref:Java.Lang.Object.Dispose)明確釋放。
與受控可呼叫包裝函式不同的是，在處置這類實例之前應該*特別小心*，因為*Dispose （）* -實例的執行會中斷 JAVA 實例（Android 可呼叫包裝函式的實例）和 managed 的對應示例.

### <a name="java-activation"></a>JAVA 啟用

當 Android 可呼叫[包裝](~/android/platform/java-integration/android-callable-wrappers.md)函式（ACW）是從 JAVA 建立時，ACW 的函數將C#會叫用對應的函式。 例如， *MainActivity*的 ACW 會包含預設的函式，此函式將會叫用*MainActivity*的預設函式。 （這是透過 ACW 處理函式內的*TypeManager （）* 呼叫來完成）。

另一個是結果的其他函式簽章： *（IntPtr，JniHandleOwnership）* 的函數。 每當 JAVA 物件公開給 managed 程式碼，而且需要結構化 Managed 可呼叫包裝函式來管理 JNI 控制碼時，就會叫用 *（IntPtr，JniHandleOwnership）* 函數。 這通常會自動完成。

在兩種情況下，必須以手動方式在 Managed 可呼叫包裝函式子類別上提供 *（IntPtr，JniHandleOwnership）* 函數：

1. [Android. 應用程式](xref:Android.App.Application)已子類別化。 *應用程式*是特別的;預設的*應用程式*函式將*永遠不*會叫用，而且[必須改為提供（IntPtr，JniHandleOwnership）的函數](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105)。

2. 來自基類的虛擬方法調用。

請注意，（2）是 leaky 的抽象概念。 在 JAVA 中，就C#像在中一樣，從函式呼叫虛擬方法一律會叫用最衍生的方法。 例如， [TextView （CoNtext，AttributeSet，int）](xref:Android.Widget.TextView#ctor*)函式會叫用虛擬方法[TextView getDefaultMovementMethod （）](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())，它會系結為[TextView 屬性](xref:Android.Widget.TextView.DefaultMovementMethod)。
因此，如果型別[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs)是（1）子[類別 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)，（2）覆[寫 TextView DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，而（3）透過[XML 啟動該類別的實例](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)，則覆寫的*DefaultMovementMethod*屬性會在 ACW 的處理函式有機會執行之前叫用，而且在此C#函式有機會執行之前發生。

當 ACW LogTextBox 實例第一次進入 managed 程式碼，然後叫用 LogTextBox （CoNtext，IAttributeSet，）時，透過[LogTextView （IntPtr，JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)的處理常式具現化實例 LogTextBox，即可支援此情況。 [int）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)在 ACW 的函式執行時 *，在同一個實例上的*函數。

事件的順序：

1. 版面配置 XML 會載入至[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)。

2. Android 會將版面設定物件圖形具現化，並具現化 monodroid 的實例*apidemo. LogTextBox* ，ACW for *LogTextBox* 。

3. *Monodroid. apidemo. LogTextBox*函式會執行[TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)的函式。

4. *TextView*的函式呼叫會叫用*monodroid （）* 。

5. *monodroid. apidemo. getDefaultMovementMethod （* ）會叫用 LogTextBox *. n_getDefaultMovementMethod* （），它會叫用 *（）* ，它會叫用（），它會叫用[。 GetObject&lt;TextView&gt; （handle，JniHandleOwnership. DoNotTransfer）](xref:Java.Lang.Object.GetObject*) 。

6. *&lt;TextView&gt;（）* ，會檢查是否已經有對應C#的實例可供*處理*。 如果有，則會傳回它。 在此案例中，沒有，所以*Object。 GetObject&lt;t&gt;（）* 必須建立一個。

7. *物件。 GetObject&lt;t&gt;（）* 會尋找*LogTextBox （IntPtr，JniHandleOwneship）* 的函式、叫用它、建立*控制碼*與建立的實例之間的對應，然後傳回建立的實例。

8. *TextView. n_GetDefaultMovementMethod （）* 會叫用*LogTextBox. DefaultMovementMethod*屬性 getter。

9. 控制項會回到*TextView*的函式，它會完成執行。

10. *Monodroid. apidemo. LogTextBox*函式會執行，叫用*TypeManager。 Activate （）* 。

11. *LogTextBox （CoNtext，IAttributeSet，int）* 的函式會*在（7）中建立的相同實例上*執行。

12. 如果找不到（IntPtr，JniHandleOwnership）構造函式，則會擲回 MissingMethodException] （x： MissingMethodException）。

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>過早處置（）呼叫

JNI 控制碼與相對應的實例之間有一個C#對應。 . Lang. Dispose （）會中斷這個對應。 如果 JNI 控制碼在對應中斷之後進入 managed 程式碼，它看起來就像是 JAVA 啟動，而且會檢查 *（IntPtr，JniHandleOwnership）* 的函式，並加以叫用。 如果不存在此函式，則會擲回例外狀況。

例如，假設有下列受控可呼叫 Wraper 子類別：

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

如果我們建立實例，處置（），並導致重新建立受控可呼叫包裝函式：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

此程式將會骰子：

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

如果子類別包含 *（IntPtr，JniHandleOwnership）* 的函式，則會建立類型的*新*實例。 因此，實例會顯示為「遺失」所有實例資料，因為它是新的實例。 （請注意，此值為 null）。

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

只有當您知道 JAVA 物件不再使用時，或子類別未包含任何實例資料，且已提供 *（IntPtr，JniHandleOwnership）* 的函式時，才會有 managed 可呼叫包裝函式子類別的*Dispose （）* 。

## <a name="application-startup"></a>應用程式啟動

當活動、服務等啟動時，Android 會先檢查是否已有執行中的進程來裝載活動/服務/等等。如果沒有這樣的進程，則會建立新的進程、讀取[androidmanifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html) ，而且會載入並具現化[/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm)屬性中指定的類型。 接下來， [/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm)屬性值所指定的所有類型都會具現化，並叫用其[ContentProvider. attachInfo %28）](xref:Android.Content.ContentProvider.AttachInfo*)方法。 [Xamarin] 會藉由新增 mono 來攔截 *。* 在建立程式期間，請尋找 mono.monoruntimeprovider *ContentProvider*至 androidmanifest.xml。 *Mono。請尋找 mono.monoruntimeprovider. attachInfo （）* 方法負責將 Mono 執行時間載入進程中。
在此點之前使用 Mono 的任何嘗試都會失敗。 （*注意*：這就是為什麼當應用程式實例在 Mono 可以初始化之前建立時, 應用程式所需提供[(IntPtr, JniHandleOwnership)函式](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)的類別[Android.App.Application](xref:Android.App.Application)。

完成進程初始化之後，請 `AndroidManifest.xml`，以尋找要啟動之活動/服務/等等的類別名稱。 例如， [/manifest/application/activity/@android:name 屬性](https://developer.android.com/guide/topics/manifest/activity-element.html#nm)是用來決定要載入的活動名稱。 若為活動，此類型必須繼承[android. app. 活動](xref:Android.App.Activity)。
指定的型別是透過[class.forname （）](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String))載入，它會要求型別必須是 JAVA 型別，也就是 Android 可呼叫包裝函式，然後再具現化。 建立 Android 可呼叫包裝函式實例，將會觸發建立對應C#類型的實例。 然後，Android 會叫用[onCreate （配套）](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，這將會叫用對應的[onCreate （配套）](xref:Android.App.Activity.OnCreate*) ，而您也不會遇到競爭情形。
