---
title: 使用 JNI
description: Xamarin.Android 允許撰寫 C# 而不是 Java 的 Android 應用程式。 數個組件會提供 Xamarin.Android 這兩個提供的 Java 程式庫，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 繫結。 不過，每個可能的 Java 程式庫，如未提供繫結，而且每個 Java 類型及成員所提供的繫結可能不會將繫結。 若要使用未繫結的 Java 類型和成員，可能會使用 Java 原生介面 (JNI)。 本文說明如何使用 JNI Java 類型及成員從 Xamarin.Android 應用程式互動。
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 4b5874a0f0e4289201f68299e2e37660cabc9ecf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-jni"></a>使用 JNI

_Xamarin.Android 允許撰寫 C# 而不是 Java 的 Android 應用程式。數個組件會提供 Xamarin.Android 這兩個提供的 Java 程式庫，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 繫結。不過，每個可能的 Java 程式庫，如未提供繫結，而且每個 Java 類型及成員所提供的繫結可能不會將繫結。若要使用未繫結的 Java 類型和成員，可能會使用 Java 原生介面 (JNI)。本文說明如何使用 JNI Java 類型及成員從 Xamarin.Android 應用程式互動。_


## <a name="overview"></a>總覽

它不一定需要或不可能建立受管理可呼叫包裝函式 (MCW) 來叫用的 Java 程式碼。 在許多情況下，「 內嵌 」 JNI 是完全可接受並適用於一次性使用未繫結的 Java 成員。 通常是容易使用 JNI 叫用比若要產生整個 d 繫結的 Java 類別上的單一方法。

Xamarin.Android 提供`Mono.Android.dll`組件，提供的繫結，適用於 Android 的`android.jar`程式庫。 型別和成員不存在內`Mono.Android.dll`內未出現型別和`android.jar`可能由手動將它們繫結。 若要繫結 Java 型別和成員時，您使用**Java 原生介面**(**JNI**) 查閱類型、 讀取和寫入欄位，並叫用方法。

Xamarin.Android JNI API 在概念上非常類似`System.Reflection`.NET 中的應用程式開發介面： 可讓您查詢型別和成員名稱，讀取和寫入欄位的值，叫用方法和更多。 您可以使用 JNI 和`Android.Runtime.RegisterAttribute`宣告繫結支援覆寫虛擬方法的自訂屬性。 您可以繫結介面，以便它們可以在 C# 中實作。

本文件說明：

-  如何 JNI 參考類型。
-  如何以查閱、 讀取及寫入的欄位。
-  如何以查閱及叫用方法。
-  如何公開虛擬方法，以允許從 managed 程式碼覆寫。
-  如何公開的介面。



## <a name="requirements"></a>需求

JNI，公開透過[Android.Runtime.JNIEnv 命名空間](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，位於 Xamarin.Android 的每個版本。
若要繫結 Java 類型和介面，您必須使用 Xamarin.Android 4.0 或更新版本。


## <a name="managed-callable-wrappers"></a>受管理的可呼叫包裝函式

A**管理可呼叫包裝函式**(**MCW**) 是*繫結*Java 類別或介面的所有 JNI 機制，讓用戶端 C# 程式碼不需要擔心會結束JNI 基礎的複雜度。 大部分的`Mono.Android.dll`managed 可呼叫包裝函式所組成。

受管理的可呼叫包裝函式有兩個用途：

1.  封裝 JNI 使用，讓用戶端程式碼不需要了解基礎的複雜性。
1.  可讓 Java 類型具有子類別並實作的 Java 介面。

第一個用途是複雜性的單純的方便以及可以封裝，讓取用者有一組簡單、 managed 類別來使用。 這需要使用各種[JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)本文稍後所述的成員。 請記住，管理可呼叫包裝函式時並非絕對必要&ndash;JNI 使用 「 內嵌 」 是完全可接受和適用於一次性使用未繫結的 Java 成員。 分為子類別和介面的實作需要受管理的可呼叫包裝函式的使用。



## <a name="android-callable-wrappers"></a>Android 的可呼叫包裝函式

要叫用 managed 程式碼; 需要 Android 的執行階段 （圖案） 時，所需 android 可呼叫包裝函式 (ACW)需要這些包裝函式，因為沒有任何方法，以向圖案中的類別，在執行階段。
(具體而言， [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) Android 的執行階段不支援 JNI 函式。 Android 的可呼叫包裝函式因此便會產生執行階段類型註冊支援缺乏的。）

Android 程式碼需要執行的虛擬或介面方法，會覆寫或實作在 managed 程式碼，每當 Xamarin.Android 必須提供 Java proxy，使這個方法取得分派至適當的 managed 型別。 這些 Java proxy 類型是具有 「 相同 」 的基底類別和 Java 介面清單，做為受管理的類型，實作相同的建構函式和宣告的任何覆寫基底類別和介面方法的 Java 程式碼。

Android 的可呼叫包裝函式所產生**monodroid.exe**程式期間[建置程序](~/android/deploy-test/building-apps/build-process.md)，而且會產生 （直接或間接） 繼承的所有類型[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。



### <a name="implementing-interfaces"></a>實作介面

有時候您可能需要實作 Android 的介面 (例如[Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/))。

所有 Android 類別和介面，以擴充[Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)介面; 因此，所有的 Android 類型必須實作`IJavaObject`。
Xamarin.Android 會利用這項事實&ndash;它會使用`IJavaObject`來提供 Android Java proxy （Android 可呼叫包裝函式），以指定的 managed 型別。 因為**monodroid.exe**只會尋找`Java.Lang.Object`子類別 (必須實作`IJavaObject`)、 subclassing`Java.Lang.Object`我們提供 managed 程式碼中實作介面的方法。 例如: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>實作詳細資料

*這篇文章的其餘部分提供實作詳細資料，恕不另行通知*（並只是因為開發人員可能會好奇背後原理運作這裡呈現）。

例如，假設下列 C# 原始程式檔：

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

**Mandroid.exe**程式將會產生下列 Android 可呼叫包裝函式：

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

請注意，保留了基底類別在 managed 程式碼會覆寫每個方法可使用原生方法宣告。



### <a name="exportattribute-and-exportfieldattribute"></a>Exportattribute 標記的型和 ExportFieldAttribute

一般而言，Xamarin.Android 會自動產生包含 ACW; 的 Java 程式碼類別衍生自的 Java 類別並覆寫現有的 Java 方法時，此層代為基礎的類別和方法名稱。 不過，在某些情況下，程式碼產生不正確的如下所述：

-   Android 支援的動作名稱在配置的 XML 屬性，例如[android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) XML 屬性。 當指定時，擴大的檢視執行個體就會嘗試查閱 Java 方法。

-   [Java.io.Serializable](http://developer.android.com/reference/java/io/Serializable.html)介面需要`readObject`和`writeObject`方法。 沒有這個介面的成員，因為我們對應的 managed 的實作不會公開這些方法來使用 Java 程式碼。

-   [Android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/)介面必須有實作類別必須有靜態欄位`CREATOR`型別的`Parcelable.Creator`。 產生的 Java 程式碼會需要某些明確欄位。 一般案例中，沒有任何方法可以在 Java 程式碼中的 [輸出] 欄位從 managed 程式碼。


產生程式碼不提供解決方案，以產生具有任意名稱的任意 Java 方法，因為開頭 Xamarin.Android 4.2 [exportattribute 標記的型](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)和[ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/)了在引進提供上述狀況的解決方案。 這兩個屬性位於`Java.Interop`命名空間：

-   `ExportAttribute` &ndash; 指定方法名稱和其預期的例外狀況類型 （若要提供明確 」 就會擲回 「 java）。 當它在方法上使用時，方法會 「 匯出 」 產生分派代碼對應 JNI 引動過程，給 managed 方法的 Java 方法。 這可以搭配`android:onClick`和`java.io.Serializable`。

-   `ExportFieldAttribute` &ndash; 指定的欄位名稱。 它位於可做為欄位初始設定式的方法。 這可以搭配`android.os.Parcelable`。

[Exportattribute 標記的型](https://developer.xamarin.com/samples/monodroid/ExportAttribute/)範例專案會說明如何使用這些屬性。


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>疑難排解 exportattribute 標記的型和 ExportFieldAttribute

-   封裝會失敗，因為遺漏**Mono.Android.Export.dll** &ndash;如果您使用`ExportAttribute`或`ExportFieldAttribute`上程式碼或相依程式庫中的某些方法，您必須加入**Mono.Android.Export.dll**。 這是組件隔離，以支援回呼的程式碼將來自 Java。 它是分開**Mono.Android.dll**在應用程式中加入額外的大小。

-   在發行組建`MissingMethodException`匯出方法，就會發生&ndash;中發行的組建，`MissingMethodException`匯出方法，就會發生。 （Xamarin.Android 的最新版本中已修正此問題）。



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` 和`ExportFieldAttribute`該 Java 執行階段程式碼可以使用提供的功能。 此執行階段程式碼會透過這些屬性由所產生的 JNI 方法存取 managed 程式碼。 如此一來，沒有任何受管理的方法繫結; 的現有 Java 方法因此，會從 managed 的方法簽章產生 Java 方法。

不過，此情況下不完全的行列式。 最值得注意的是，這是在 managed 的類型和 Java 類型，例如之間的某些進階對應，則為 true:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

匯出的方法，需要這類的類型時`ExportParameterAttribute`必須明確地命名對應的參數或傳回值型別使用。



### <a name="annotation-attribute"></a>註解屬性

Xamarin.Android 4.2 轉換`IAnnotation`將屬性 (System.Attribute)，並加入的支援 Java 的包裝函式中的註解產生的實作類型。

這表示下列的方向變更：

-   繫結產生器會產生`Java.Lang.DeprecatedAttribute`從`java.Lang.Deprecated`(雖然它應該是`[Obsolete]`managed 程式碼中)。

-   這不表示現有的`Java.Lang.Deprecated`類別也將消失。 這些 Java 為基礎的物件仍可做為慣用的 Java 物件 （如果這類使用的話）。 會有`Deprecated`和`DeprecatedAttribute`類別。

-   `Java.Lang.DeprecatedAttribute`類別標示為`[Annotation]`。 繼承自這個自訂屬性時`[Annotation]`屬性，msbuild 工作將會產生該自訂屬性的 Java 註釋 (@Deprecated) 中 Android 可呼叫包裝函式 (ACW)。

-   註解拖曳至類別，方法會產生及匯出 （這是一種方法在 managed 程式碼） 的欄位。

如果未註冊包含的類別 （註解的類別本身或包含註解式的成員的類別），整個 Java 類別來源不會產生，包括註解。 若是方法，您可以指定`ExportAttribute`取得明確產生並註解的方法。 此外，它不會 」 產生 「 Java 註解的類別定義的功能。 換句話說，如果您定義特定註解的自訂 managed 的屬性，您必須加入另一個包含對應的 Java 註解類別 d 程式庫。 將 Java 原始程式檔會定義註釋之類型是不夠的。 Java 編譯器無法運作方式與**apt**。

此外會適用下列限制：

-   此轉換程序不會考慮`@Target`上註解的註解型別為止。

-   屬性的屬性無法運作。 請改用屬性 getter 或 setter 的屬性。



## <a name="class-binding"></a>類別繫結

繫結類別，表示撰寫以簡化基礎 Java 類型的引動過程的 managed 可呼叫包裝函式。

繫結虛擬和抽象方法，以允許覆寫從 C# 需要 Xamarin.Android 4.0。 不過，任何版本的 Xamarin.Android 可以繫結非虛擬方法、 靜態方法或虛擬方法不支援覆寫。

繫結通常會包含下列項目：

-  A [JNI 控制代碼繫結的 Java 類型](#_Looking_up_Java_Types)。

-  [JNI 欄位 Id 和每個繫結的欄位屬性](#_Instance_Fields)。

-  [JNI 方法 Id，而且每個方法繫結方法](#_Instance_Methods)。

-  如果需要分為子類別，型別都必須有[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自訂屬性型別宣告與[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)設`true`。



### <a name="declaring-type-handle"></a>宣告的類型控制代碼

欄位和方法查閱方法都需要其宣告的型別參考的物件參考。 根據慣例，這保留在`class_ref`欄位：

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

請參閱[JNI 型別參考](#_JNI_Type_References)區段如需詳細資訊，關於`CLASS`語彙基元。


### <a name="binding-fields"></a>繫結欄位

Java 欄位會公開為 C# 屬性，例如 Java 欄位[java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in)會繫結 C# 屬性為[Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/)。
此外，由於 JNI 區分靜態欄位和執行個體欄位，不同的方法時，使用實作的屬性。

欄位繫結牽涉到三組方法：

1.  *取得欄位 id*方法。 *取得欄位 id*方法負責傳回處理這種欄位*取得欄位值*和*設定欄位值*方法會使用。 取得欄位 id，則必須了解宣告的類型，欄位的名稱和[JNI 類型簽章](#_JNI_Type_Signatures)的欄位。

1.  *取得欄位值*方法。 這些方法需要的欄位控制代碼，而且會負責從 Java 讀取欄位值。
    要使用的方法需視欄位的類型而定。

1.  *設定欄位值*方法。 這些方法需要的欄位控制代碼，而且會負責寫在 Java 中欄位的值。 要使用的方法需視欄位的類型而定。


 [靜態欄位](#_Static_Fields)使用[JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)， `JNIEnv.GetStatic*Field`，和[JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/)方法。

 [執行個體欄位](#_Instance_Fields)使用[JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)， `JNIEnv.Get*Field`，和[JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/)方法。

例如，靜態屬性`JavaSystem.In`可以實作為：

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

注意： 我們會使用[InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))要轉換成 JNI 參考`System.IO.Stream`使用執行個體，而且我們`JniHandleOwnership.TransferLocalRef`因為[JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)傳回本機參考。

許多[Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)類型有`FromJniHandle`方法會將它轉換 JNI 參考到所需的類型。



### <a name="method-binding"></a>方法繫結

Java 方法會公開為 C# 方法，以及 C# 屬性。 比方說，Java 方法[java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean))方法會繫結為[Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/)方法，而[java.lang.Object.getClass](http://developer.android.com/reference/java/lang/Object.html#getClass)方法會繫結為[Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/)屬性。

方法引動過程是兩個步驟的程序：

1.  *取得方法 id*叫用方法。 *取得方法 id*方法負責傳回方法引動過程方法會使用的方法控制代碼。 取得方法 id，則必須了解宣告的類型，方法的名稱和[JNI 類型簽章](#_JNI_Type_Signatures)的方法。

1.  叫用方法。

如同欄位，以取得方法 id，並叫用方法所使用的方法不同的靜態方法和執行個體方法。

[靜態方法](#_Static_Methods_1)使用[JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)查閱方法 id，並使用`JNIEnv.CallStatic*Method`系列方法引動過程。

[執行個體方法](#_Instance_Methods)使用[JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)查閱方法 id，並使用`JNIEnv.Call*Method`和`JNIEnv.CallNonvirtual*Method`系列的方法引動過程。

方法繫結是可能不只是方法引動過程。 方法繫結也包括允許的方法覆寫 （適用於抽象和非 final 方法），或者 （適用於介面的方法） 實作。 [支援繼承，介面](#_Supporting_Inheritance,_Interfaces_1)章節將涵蓋支援的虛擬方法和介面方法的複雜性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>靜態方法

繫結的靜態方法牽涉到使用`JNIEnv.GetStaticMethodID`以取得方法的控制代碼，然後使用適當`JNIEnv.CallStatic*Method`方法，會根據方法的傳回型別。 以下是範例的繫結[Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法：

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

我們將方法控制代碼儲存在靜態欄位，注意`id_getRuntime`。 這是效能最佳化，以便查閱在每次叫用不需要的方法控制代碼。 您不需要快取方法控制代碼，以這種方式。 取得方法的控制代碼時，一旦[JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用來叫用方法。 `JNIEnv.CallStaticObjectMethod` 傳回`IntPtr`其中包含傳回 Java 執行個體的控制代碼。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr、 JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用來將 Java 控制代碼轉換成強類型的物件的執行個體。



#### <a name="non-virtual-instance-method-binding"></a>非虛擬執行個體方法繫結

繫結`final`執行個體方法或執行個體方法，這個方法不需要覆寫時，會牽涉到使用`JNIEnv.GetMethodID`以取得方法的控制代碼，然後使用適當`JNIEnv.Call*Method`方法，會根據方法的傳回型別。 以下是範例的繫結`Object.Class`屬性：

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

我們將方法控制代碼儲存在靜態欄位，注意`id_getClass`。
這是效能最佳化，以便查閱在每次叫用不需要的方法控制代碼。 您不需要快取方法控制代碼，以這種方式。 取得方法的控制代碼時，一旦[JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用來叫用方法。 `JNIEnv.CallStaticObjectMethod` 傳回`IntPtr`其中包含傳回 Java 執行個體的控制代碼。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr、 JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用來將 Java 控制代碼轉換成強類型的物件的執行個體。


### <a name="binding-constructors"></a>繫結的建構函式

建構函式都具有名稱的 Java 方法`"<init>"`。 如同 Java 執行個體方法，`JNIEnv.GetMethodID`用來查閱建構函式控制代碼。 Java 與方法不同， [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/)方法可用於叫用建構函式方法控制代碼。 傳回值`JNIEnv.NewObject`JNI 本機參考：


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

一般類別繫結會將子類別化[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。
當子類別化`Java.Lang.Object`、 其他語意派上用場：`Java.Lang.Object`執行個體維護透過 Java 執行個體的全域參考`Java.Lang.Object.Handle`屬性。

1.  `Java.Lang.Object`預設建構函式會配置的 Java 執行個體。

1.  如果類型具有`RegisterAttribute`，和`RegisterAttribute.DoNotGenerateAcw`是`true`，執行個體`RegisterAttribute.Name`透過其預設建構函式建立型別。

1.  否則， [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 對應至`this.GetType`會透過其預設建構函式的具現化。 封裝建立期間所產生的 android 可呼叫包裝函式每個`Java.Lang.Object`子類別的`RegisterAttribute.DoNotGenerateAcw`未設定為`true`。

型別不類別繫結，這是預期的語意： 具現化`Mono.Samples.HelloWorld.HelloAndroid`C# 執行個體應該建構 Java`mono.samples.helloworld.HelloAndroid`即產生 Android 可呼叫包裝函式的執行個體。

對於類別繫結，這可能是正確的行為，如果 Java 類型包含預設建構函式和/或沒有其他建構函式需要叫用。 否則，建構函式，必須提供它會執行下列動作：

1.  叫用[Java.Lang.Object （IntPtr、 JniHandleOwnership）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)而不使用預設`Java.Lang.Object`建構函式。 需要這個動作，以避免建立新的 Java 執行個體。

1.  檢查值[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)再建立任何 Java 執行個體。 `Object.Handle`屬性有值的以外`IntPtr.Zero`如果 Android 可呼叫包裝函式建構在 Java 程式碼中，要建構類別繫結來包含建立的 Android 可呼叫包裝函式執行個體。 比方說，當 Android 建立`mono.samples.helloworld.HelloAndroid`執行個體，將建立 Android 的可呼叫包裝函式，第一個和 Java`HelloAndroid`建構函式會建立對應的執行個體`Mono.Samples.HelloWorld.HelloAndroid`型別，與`Object.Handle`屬性設定建構函式執行前的 Java 執行個體。

1.  如果目前的執行階段類型不是相同的宣告類型，則對應 Android 可呼叫包裝函式的執行個體必須建立並使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))來儲存所傳回的控制代碼[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)。

1.  如果目前的執行階段類型的宣告類型相同，然後叫用的 Java 建構函式，並使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))來儲存所傳回的控制代碼`JNIEnv.NewInstance`。


例如，請考慮[java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int))建構函式。 這會繫結為：

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)方法是 helper 來執行`JNIEnv.FindClass`， `JNIEnv.GetMethodID`， `JNIEnv.NewObject`，和`JNIEnv.DeleteGlobalReference`從傳回的值上`JNIEnv.FindClass`。 如需詳細資訊，請參閱下一節。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支援繼承，介面

子類別化 Java 類型或實作 Java 介面所必須產生[Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)(ACWs) 產生的每個`Java.Lang.Object`封裝程序期間的子類別。 透過控制 ACW 產生[Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自訂屬性。

針對 C# 類型，`[Register]`自訂屬性建構函式需要一個引數： [JNI 簡化型別參考](#_Simplified_Type_References_1)對應的 java 類型。 這可讓提供 Java 和 C# 之間的不同名稱。

Xamarin.Android 4.0 之前`[Register]`自訂屬性無法使用為"alias"現有 Java 類型。 這是因為 ACW 產生程序會產生 ACWs 的每個`Java.Lang.Object`遇到子類別。

Xamarin.Android 4.0 引進了[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)屬性。 此屬性會指示 ACW 產生程序*略過*註解式的型別，允許的新管理可呼叫包裝函式，不會導致在封裝建立期間所產生的 ACWs 宣告。 這可讓繫結現有的 Java 類型。 例如，請考慮下列簡單的 Java 類別， `Adder`，其中包含一種方法， `add`，它加入至整數，並傳回結果：

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder`無法繫結類型為：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

在這裡， `Adder` C# 類型*別名* `Adder` Java 類型。 `[Register]`屬性用來指定 JNI 名稱`mono.android.test.Adder`Java 類型及`DoNotGenerateAcw`屬性用來禁止 ACW 產生。 這會導致產生的 ACW`ManagedAdder`輸入，其中正確子類別`mono.android.test.Adder`型別。 如果`RegisterAttribute.DoNotGenerateAcw`屬性還沒有用，則 Xamarin.Android 建置程序將會產生新`mono.android.test.Adder`Java 類型。 這會導致編譯錯誤，做為`mono.android.test.Adder`型別會存在於兩次，在兩個不同的檔案。



### <a name="binding-virtual-methods"></a>繫結的虛擬方法

`ManagedAdder` 子類別 Java`Adder`類型，但它不是特別有趣： C#`Adder`類型未定義任何虛擬方法，因此`ManagedAdder`無法覆寫任何項目。

繫結`virtual`設為允許覆寫子類別的方法需要幾項工作必須先完成其可分成下列兩個類別：

1.  **方法繫結**

1.  **註冊方法**


#### <a name="method-binding"></a>方法繫結

方法繫結需要的 C# 的兩個支援成員的加入`Adder`定義： `ThresholdType`，和`ThresholdClass`。

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType`屬性會傳回目前繫結的類型：

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` 是方法的繫結中用來判斷何時應該執行虛擬與非虛擬方法分派。 它應該會一律傳回`System.Type`其對應到宣告的 C# 類型的執行個體。

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass`屬性會傳回 JNI 類別參考繫結的類型：

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` 叫用非虛擬方法時，會使用方法繫結中。

#### <a name="binding-implementation"></a>繫結的實作

方法繫結實作負責的 Java 方法的執行階段引動過程。 它也包含`[Register]`自訂屬性宣告，屬於方法註冊，並將方法註冊一節中討論：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

`id_add`欄位包含方法的 Java 方法叫用的識別碼。 `id_add`值取自`JNIEnv.GetMethodID`，這需要宣告類別 (`class_ref`)，Java 方法名稱 (`"add"`)，和 JNI 簽章的方法 (`"(II)I"`)。

取得方法 ID 時，一旦`GetType`相較於`ThresholdType`來判斷是否需要虛擬或非虛擬的分派。 虛擬分派時為必要`GetType`符合`ThresholdType`，做為`Handle`Java 配置子類別覆寫此方法可能會參考。

當`GetType`不符`ThresholdType`，`Adder`已子類別化 (例如透過`ManagedAdder`)，而`Adder.Add`實作將只會叫用子類別叫用`base.Add`。 這種非虛擬的分派，這是 where`ThresholdClass`傳入。 `ThresholdClass` 指定哪一個 Java 類別會提供要叫用方法的實作。



#### <a name="method-registration"></a>註冊方法

假設我們有更新`ManagedAdder`定義會覆寫`Adder.Add`方法：

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

請記得，`Adder.Add`有`[Register]`自訂屬性：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]`自訂屬性建構函式接受三個值：

1.  Java、 方法名稱`"add"`在此情況下。

1.  JNI 類型簽章的方法，`"(II)I"`在此情況下。

1.  *連接器方法*，`GetAddHandler`在此情況下。
    連接器方法將在稍後討論。


前兩個參數允許 ACW 產生程序，以產生在方法宣告覆寫的方法。 產生 ACW 就會包含下列程式碼：

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

請注意，`@Override`方法宣告，它會委派至`n_`-前置詞的相同名稱的方法。 這樣可確保，Java 程式碼會叫用時`ManagedAdder.add`，`ManagedAdder.n_add`會叫用，以允許覆寫 C#`ManagedAdder.Add`来執行的方法。

因此，最重要的問題： 如何為`ManagedAdder.n_add`攔截最多`ManagedAdder.Add`嗎？

Java `native` Java （Android 的執行階段） 執行階段透過向註冊方法[JNI RegisterNatives 函式](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)。
`RegisterNatives` 會遵循結構包含 Java 方法名稱、 JNI 類型簽章，以及叫用的函式指標的陣列[JNI 呼叫慣例](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)。
函式指標必須是後面接著方法參數的兩個指標引數的函式。 使用 Java`ManagedAdder.n_add`必須實作方法，透過具有下列 C 原型的函式：

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

不會公開 Xamarin.Android`RegisterNatives`方法。 相反地，ACW 和 MCW 一起提供叫用所需的資訊`RegisterNatives`: ACW 包含方法名稱和 JNI 類型簽章，唯一的項目遺漏是連結的函式指標。

這是 where*連接器方法*傳入。 第三個`[Register]`自訂屬性的參數是已註冊型別或不接受任何參數，並傳回註冊類型的基底類別中定義的方法名稱`System.Delegate`。 傳回`System.Delegate`依次指的是正確的 JNI 函式簽章的方法。 最後，連接器方法會傳回委派*必須*根目錄，因此 GC，不會收集因為 java 提供委派。

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

`GetAddHandler`方法會建立`Func<IntPtr, IntPtr, int, int,
int>`委派指`n_Add`方法，再叫用[JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/)。
`JNINativeWrapper.CreateDelegate` 包裝在 try/catch 區塊中，所提供的方法，使任何未處理的例外狀況處理，而且將會導致引發[AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/)事件。 產生的委派會儲存在靜態`cb_add`變數，以便在 GC 不會釋放委派。

最後，`n_Add`方法是負責封送處理 JNI 參數，為對應的 managed 型別，則委派的方法呼叫。

注意： 一律使用`JniHandleOwnership.DoNotTransfer`取得 MCW 透過 Java 執行個體時。 視為本機參考 (然後因此呼叫`JNIEnv.DeleteLocalRef`) 將會中斷管理-&gt; Java-&gt;管理堆疊轉換。



### <a name="complete-adder-binding"></a>完成 Adder 繫結

完整的受管理的繫結`mono.android.tests.Adder`類型是：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>限制

在撰寫類型符合下列準則：

1.  子類別 `Java.Lang.Object`

1.  具有`[Register]`自訂屬性

1.  `RegisterAttribute.DoNotGenerateAcw` 是 `true`


然後針對 GC 互動類型*必須不*有任何欄位，這可能會參考`Java.Lang.Object`或`Java.Lang.Object`子類別，在執行階段。 例如，類型的欄位`System.Object`及不允許使用任何介面類型。 型別不能參考`Java.Lang.Object`執行個體萬用字元，例如`System.String`和`List<int>`。 這項限制是為了防止 gc 的分批物件集合。

如果類型必須包含可以參考執行個體欄位`Java.Lang.Object`執行個體，則欄位類型必須是`System.WeakReference`或`GCHandle`。



## <a name="binding-abstract-methods"></a>繫結抽象方法

繫結`abstract`是大致相同繫結的虛擬方法的方法。 有兩個差異：

1.  抽象方法是抽象的。 它仍會保留`[Register]`屬性和相關聯的方法註冊，方法繫結只移至`Invoker`型別。

1.  非`abstract``Invoker`建立哪個子類別類型是抽象型別。 `Invoker`型別必須覆寫基底類別中宣告的所有抽象方法，並覆寫的實作是繫結的方法實作，還是可以忽略非虛擬的分派大小寫。


例如，假設上述`mono.android.test.Adder.add`方法`abstract`。 C# 繫結將會變更以便`Adder.Add`抽象，且新`AdderInvoker`類型將會定義其中實作`Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

`Invoker`取得 JNI Java 建立執行個體的參考時，才需要型別。


## <a name="binding-interfaces"></a>繫結介面

繫結介面是在概念上類似於繫結類別，其中包含虛擬方法，但許多細節不同難以察覺 （或不太微妙） 的方式。 請考慮下列[Java 介面宣告](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

介面的繫結包含兩個部分： C# 介面定義和啟動程式定義的介面。



### <a name="interface-definition"></a>介面定義

C# 介面定義必須滿足下列需求：

-   介面定義必須有`[Register]`自訂屬性。

-   介面定義必須擴充`IJavaObject interface`。
    這樣會防止 ACWs 從 Java 介面繼承。

-   每個介面方法必須包含`[Register]`指定對應的 Java 方法名稱、 JNI 簽章，以及連接器方法的屬性。

-   連接器方法也必須指定可位於連接器方法的型別。

繫結時`abstract`和`virtual`連接器方法的方法會搜尋所註冊的類型的繼承階層架構內。 介面可以有沒有包含內文，所以此作業無效，因此需求的方法，指出連接器方法所在的位置指定的類型。 連接器方法在字串內，指定型別冒號之後`':'`，而且必須包含啟動程式的類型的組件限定的類型名稱。

介面方法宣告都是對應的 Java 方法使用的翻譯*相容*型別。 針對 Java 內建類型，相容的型別會對應 C# 的型別，例如 Java`int`是 C# `int`。 參考類型相容的型別會是可提供適當的 Java 類型 JNI 控制代碼的類型。

介面成員，就不會直接叫用 Java&ndash;引動過程將會在啟動程式型別傳送&ndash;以便允許一定程度的彈性。

Java 進度介面可以是[中 C# 做為宣告](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

請注意，在上述我們對應 Java`int[]`參數[JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/)。
這並非必要： 我們無法繫結至以 C# `int[]`，或`IList<int>`，或是其他項目完全。 選擇任何類型，`Invoker`必須能將它轉譯成 Java`int[]`引動過程類型。


### <a name="invoker-definition"></a>啟動程式定義

`Invoker`一定會繼承型別定義`Java.Lang.Object`，實作適當的介面，並提供介面定義中參考的所有連線方法。 沒有一個不同於類別繫結的多個建議：`class_ref`欄位和方法的 Id 應該是執行個體成員，並非供靜態成員。

偏好的執行個體成員的原因有與`JNIEnv.GetMethodID`Android 的執行階段中的行為。 （這可能是 Java 行為以及; 尚未經過測試）。`JNIEnv.GetMethodID`時查閱來自於實作的介面以及不宣告的介面的方法會傳回 null。 請考慮[java.util.SortedMap&lt;K，V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) Java 介面，實作[java.util.Map&lt;K，V&gt; ](http://developer.android.com/reference/java/util/Map.html)介面。 地圖會提供[清除](http://developer.android.com/reference/java/util/Map.html#clear())方法，因此看似合理`Invoker`SortedMap 定義會是：

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

上述會失敗，因為`JNIEnv.GetMethodID`會傳回`null`時查閱`Map.clear`方法，透過`SortedMap`類別執行個體。

有兩個解決方案： 追蹤來自於每個方法，哪個介面，而且有`class_ref`每個介面，或保留做為執行個體成員的所有項目並執行於最具衍生性的類別類型，而不是介面類型方法查詢。 後者是在**Mono.Android.dll**。

啟動程式定義有六個部分： 建構函式，`Dispose`方法，`ThresholdType`和`ThresholdClass`成員`GetObject`方法、 介面方法實作中，以及連接器方法實作。



#### <a name="constructor"></a>建構函式

建構函式需要查閱所叫用執行個體的執行階段類別和執行個體中儲存的執行階段類別`class_ref`欄位：

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

注意：`Handle`屬性必須使用建構函式主體中，而非`handle`參數，為 Android v4.0 上`handle`基底建構函式完成執行之後，參數可能不正確。


#### <a name="dispose-method"></a>Dispose 方法

`Dispose`方法需要釋放配置於建構函式的通用參考：

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>色黑墨色和 ThresholdClass

`ThresholdType`和`ThresholdClass`成員都是相同類別繫結中找到：

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>GetObject 方法

靜態`GetObject`方法才能支援[Extensions.JavaCast&lt;T&gt;（)](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>介面方法

介面的所有方法都需要有實作，會叫用 JNI 透過對應的 Java 方法：

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>連接器方法

連接器方法和支援的基礎結構會負責封送處理成適當的 C# 類型的 JNI 參數。 使用 Java`int[]`參數會當做 JNI 傳遞給`jintArray`，也就是`IntPtr`C# 中。 `IntPtr`必須封送處理至`JavaArray<int>`為了支援叫用 C# 介面：

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

如果`int[]`會透過慣用`JavaList<int>`，然後[JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type))可改用：

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

不過請注意，`JNIEnv.GetArray`複製 Vm，因此對於大型陣列這可能導致大量加入 GC 壓力之間的整個陣列。


### <a name="complete-invoker-definition"></a>完成啟動程式定義

[完成 IAdderProgressInvoker 定義](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>JNI 物件參考

有許多 JNIEnv 方法傳回*JNI* *物件參考*，類似於`GCHandle`s。 JNI 提供的物件參考的三種不同類型： 本機參考、 通用參考，以及全域的弱式參考。 這三個以`System.IntPtr`，*但*（根據 JNI 函式類型 區段中） 不是所有`IntPtr`傳回從`JNIEnv`方法為參考。 例如， [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)傳回`IntPtr`，但它不會傳回的物件參考，它會傳回`jmethodID`。 請參閱[JNI 函式文件](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)如需詳細資訊。

建立的本機參考*大部分*參考建立方法。
Android 只允許限定的數量的存在任何給定時間，通常是 512 的本機參考。 您可以透過刪除本機參考[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)。
不同於 JNI，並非所有參考 JNIEnv 方法，它會傳回物件參考傳回本機參考。[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)傳回*全域*參考。 強烈建議您盡可以可能是透過建構刪除本機參考[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)物件周圍，並指定`JniHandleOwnership.TransferLocalRef`至[Java.Lang.Object (IntPtr處理，JniHandleOwnership 傳輸）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)建構函式。

建立全域參考[JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/)和[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)。
可透過終結[JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/)。
模擬器已限制為 2000 未處理的全域參考，雖然硬體裝置都有限制為大約 52,000 全域的參考。

在 Android v2.2 (Froyo) 及更新版本才有全域的弱式參考。 可以刪除全域的弱式參考與[JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr))。


### <a name="dealing-with-jni-local-references"></a>處理 JNI 本機參考

[JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/)， [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)， [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/)， [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)和[JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)方法會傳回`IntPtr`包含 JNI 本機 Java 物件參考，或`IntPtr.Zero`如果 Java 傳回`null`。 因為有限的一旦 （512 個項目），最好確認參考可以是在未處理的本機參考數目會及時刪除。 有三種方式可以以處理本機參考： 明確加以刪除，建立`Java.Lang.Object`執行個體，保留，以及使用`Java.Lang.Object.GetObject<T>()`建立周圍的 managed 可呼叫包裝函式。



### <a name="explicitly-deleting-local-references"></a>明確刪除其中的本機參考

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)可用來刪除本機參考。 一旦已刪除的本機參考，它無法再使用，所以必須小心，確保`JNIEnv.DeleteLocalRef`會最後完成的本機參考。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>包裝與 Java.Lang.Object

`Java.Lang.Object` 提供[Java.Lang.Object （IntPtr 控制代碼，JniHandleOwnership 傳輸）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)建構函式可以用來包裝現有的 JNI 參考它。 [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/)參數會決定如何`IntPtr`參數應該被視為：

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash;建立`Java.Lang.Object`執行個體將會建立新的全域參考從`handle`參數，以及`handle`保持不變。
    呼叫端負責釋放`handle`，必要時。

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;建立`Java.Lang.Object`執行個體將會建立新的全域參考從`handle`參數，以及`handle`會隨之刪除[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . 呼叫端必須不會釋放`handle`，而且絕不能使用`handle`建構函式完成執行之後。

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;建立`Java.Lang.Object`執行個體將會接管的擁有權`handle`參數。 呼叫端必須不會釋放`handle`。


因為 JNI 方法引動過程方法會傳回本機 refs，`JniHandleOwnership.TransferLocalRef`一般使用：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

建立全域參考之前，將不會釋放`Java.Lang.Object`執行個體是記憶體回收。 如果您能夠，處置的執行個體就會釋出全域的參考，以加速記憶體回收：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` 提供[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr 控制代碼，JniHandleOwnership 傳輸）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)可以用來建立指定類型的 managed 可呼叫包裝函式的方法。

型別`T`必須滿足下列需求：

1.  `T` 必須是參考類型。

1.  `T` 必須實作`IJavaObject`介面。

1.  如果`T`並不是抽象類別或介面，然後`T`必須提供給建構函式的參數型別`(IntPtr,
    JniHandleOwnership)`。

1.  如果`T`是抽象類別或介面，有*必須*是*invoker*供`T`。 啟動程式會繼承的非抽象類型`T`或實作`T`，而且相同的名稱與`T`Invoker 尾碼。 例如，如果 T 是介面`Java.Lang.IRunnable`，型別`Java.Lang.IRunnableInvoker`必須存在，且必須包含所需`(IntPtr,
    JniHandleOwnership)`建構函式。


因為 JNI 方法引動過程方法會傳回本機 refs，`JniHandleOwnership.TransferLocalRef`一般使用：

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>查閱 Java 類型

查閱欄位或方法中 JNI，欄位或方法的宣告型別必須查閱第一個。 [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))方法用來查閱 Java 類型。 字串參數的*簡化型別參考*或*完整型別參考*Java 類型。 請參閱[JNI 類型參考 > 一節](#_JNI_Type_References)簡化和完整的型別參考的詳細資料。

注意： 不同於每個其他`JNIEnv`方法會傳回物件執行個體，`FindClass`傳回全域參考，而不是本機的參考。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>執行個體欄位

欄位透過管理*欄位 Id*。 欄位識別碼取得透過[JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)，這需要類別欄位定義中欄位的名稱和[JNI 類型簽章](#JNI_Type_Signatures)的欄位。

欄位識別碼不需要釋出，且有效，只要載入對應的 Java 類型。 （android 目前不支援卸載的類別。）

有兩組方法，以管理的執行個體欄位： 一個用於讀取執行個體欄位，另一個用於寫入的執行個體欄位。 方法的所有集合，都需要讀取或寫入的欄位值的欄位 ID。


### <a name="reading-instance-field-values"></a>讀取執行個體欄位值

一組讀取執行個體欄位值的方法遵循的命名模式：

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
其中`*`是欄位的類型：

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash;讀取的值不是內建類型，例如任何執行個體欄位`java.lang.Object`，陣列和介面型別。 傳回的值為 JNI 本機參考。

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash;讀取的值`bool`執行個體欄位。

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash;讀取的值`sbyte`執行個體欄位。

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash;讀取的值`char`執行個體欄位。

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash;讀取的值`short`執行個體欄位。

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash;讀取的值`int`執行個體欄位。

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash;讀取的值`long`執行個體欄位。

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash;讀取的值`float`執行個體欄位。

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash;讀取的值`double`執行個體欄位。





### <a name="writing-instance-field-values"></a>寫入的執行個體欄位值

寫入的執行個體欄位值的方法集合會遵循的命名模式：

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

其中*類型*是欄位的類型：

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;寫入的任何欄位，不是內建類型，例如值`java.lang.Object`，陣列和介面型別。 `IntPtr`值可能是 JNI 本機參考、 JNI 全域參考、 JNI 弱式全域參考，或`IntPtr.Zero`(如`null`)。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;寫入的值`bool`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;寫入的值`sbyte`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;寫入的值`char`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;寫入的值`short`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;寫入的值`int`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;寫入的值`long`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;寫入的值`float`執行個體欄位。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;寫入的值`double`執行個體欄位。


<a name="_Static_Fields" />

## <a name="static-fields"></a>靜態欄位

靜態欄位透過管理*欄位 Id*。 欄位識別碼取得透過[JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/)，這需要類別欄位定義中欄位的名稱和[JNI 類型簽章](#JNI_Type_Signatures)的欄位。

欄位識別碼不需要釋出，且有效，只要載入對應的 Java 類型。 （android 目前不支援卸載的類別。）

系統提供的方法來操作的靜態欄位的兩組： 一個用於讀取執行個體欄位，另一個用於寫入的執行個體欄位。 方法的所有集合，都需要讀取或寫入的欄位值的欄位 ID。


### <a name="reading-static-field-values"></a>讀取靜態欄位值

一組讀取靜態欄位值的方法遵循的命名模式：

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

其中`*`是欄位的類型：

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash;讀取的任何不是內建類型，例如的靜態欄位值`java.lang.Object`，陣列和介面型別。 傳回的值為 JNI 本機參考。

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash;讀取的值`bool`靜態欄位。

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash;讀取的值`sbyte`靜態欄位。

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash;讀取的值`char`靜態欄位。

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash;讀取的值`short`靜態欄位。

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash;讀取的值`long`靜態欄位。

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash;讀取的值`float`靜態欄位。

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash;讀取的值`double`靜態欄位。



### <a name="writing-static-field-values"></a>寫入靜態欄位值

寫入靜態欄位值的方法集合會遵循的命名模式：

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

其中*類型*是欄位的類型：

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;不是內建類型，例如任何靜態欄位的值寫入`java.lang.Object`，陣列和介面型別。 `IntPtr`值可能是 JNI 本機參考、 JNI 全域參考、 JNI 弱式全域參考，或`IntPtr.Zero`(如`null`)。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;寫入的值`bool`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;寫入的值`sbyte`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;寫入的值`char`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;寫入的值`short`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;寫入的值`int`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;寫入的值`long`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;寫入的值`float`靜態欄位。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;寫入的值`double`靜態欄位。


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>執行個體方法

執行個體方法透過叫用*方法 Id*。 方法 Id 取得透過[JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)，其需要型別、 方法名稱中所定義之方法的和[JNI 類型簽章](#JNI_Type_Signatures)的方法。

方法的識別碼不需要釋出，且有效，只要載入對應的 Java 類型。 （android 目前不支援卸載的類別。）

有兩組的叫用方法的方法： 一個用於幾乎，叫用方法，一個用於叫用非虛擬方法。 這兩組方法，需要叫用方法的方法 ID 和非虛擬引動過程也會要求您指定應叫用類別實作。

介面方法僅查閱內宣告的型別。無法查閱來自擴充/繼承的介面的方法。 請參閱後續的繫結介面啟動程式實作 /section 如需詳細資訊。

在類別中宣告的任何方法或任何基底類別或實作的介面可以查閱。


### <a name="virtual-method-invocation"></a>虛擬方法引動過程

叫用方法的方法集合幾乎會遵循的命名模式：

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

其中`*`之方法的傳回型別。

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash;叫用方法，這個方法會傳回非內建型別，例如`java.lang.Object`、 陣列和介面。 傳回的值為 JNI 本機參考。

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash;叫用方法，這個方法會傳回`bool`值。

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash;叫用方法，這個方法會傳回`sbyte`值。

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash;叫用方法，這個方法會傳回`char`值。

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash;叫用方法，這個方法會傳回`short`值。

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;叫用方法，這個方法會傳回`long`值。

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash;叫用方法，這個方法會傳回`float`值。

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash;叫用方法，這個方法會傳回`double`值。



### <a name="non-virtual-method-invocation"></a>非虛擬方法引動過程

叫用方法的方法集合非幾乎會遵循的命名模式：

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`之方法的傳回型別。 非虛擬方法引動過程通常用來叫用虛擬方法的基底方法。

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash;非幾乎叫用方法，這個方法會傳回非內建型別，例如`java.lang.Object`、 陣列和介面。 傳回的值為 JNI 本機參考。

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`bool`值。

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`sbyte`值。

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`char`值。

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`short`值。

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`long`值。

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`float`值。

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash;非幾乎叫用方法，這個方法會傳回`double`值。


<a name="_Static_Methods" />

## <a name="static-methods"></a>靜態方法

靜態方法透過叫用*方法 Id*。 方法 Id 取得透過[JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)，其需要型別、 方法名稱中所定義之方法的和[JNI 類型簽章](#JNI_Type_Signatures)的方法。

方法的識別碼不需要釋出，且有效，只要載入對應的 Java 類型。 （android 目前不支援卸載的類別。）



### <a name="static-method-invocation"></a>靜態方法引動過程

叫用方法的方法集合幾乎會遵循的命名模式：

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`之方法的傳回型別。

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash;叫用靜態方法，這個方法會傳回非內建型別，例如`java.lang.Object`、 陣列和介面。 傳回的值為 JNI 本機參考。

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash;叫用靜態方法，這個方法會傳回`bool`值。

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash;叫用靜態方法，這個方法會傳回`sbyte`值。

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash;叫用靜態方法，這個方法會傳回`char`值。

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash;叫用靜態方法，這個方法會傳回`short`值。

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;叫用靜態方法，這個方法會傳回`long`值。

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash;叫用靜態方法，這個方法會傳回`float`值。

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash;叫用靜態方法，這個方法會傳回`double`值。


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 類型簽章

[JNI 類型簽章](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)是[JNI 型別參考](#_JNI_Type_References)（雖然不簡化的類型的參考） 除外的方法。 利用方法，JNI 類型簽章是一個左括號`'('`，後面接著型別參考的所有參數類型串連在一起 （具有不分隔的逗號或任何其他項目），後面接著右括號`')'`，後面接著方法的傳回型別 JNI 類型參照。

例如，給定的 Java 方法：

```java
long f(int n, String s, int[] array);
```

會 JNI 類型簽章：

```csharp
(ILjava/lang/String;[I)J
```

一般情況下，它是*強*建議使用`javap`命令來判斷 JNI 簽章。 比方說，JNI 類型簽章的[java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法 」 （Ljava/l a n g/字串;） Ljava/l a n g 執行緒 $狀態;"，而 JNI 類型的簽章[java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values)方法 」 （) [Ljava/l a n g 執行緒 $狀態;"。 請留意對尾端的分號。這些*是*JNI 類型簽章的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 型別參考

從 Java 類型參考 JNI 型別參考截然不同。 您無法使用完整的 Java 類型名稱，例如`java.lang.String`JNI，您必須改為使用 JNI 變化`"java/lang/String"`或`"Ljava/lang/String;"`，根據內容，請參閱下列詳細資料。
有四種類型的 JNI 類型參考：

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>內建型別參考

內建型別參考是單一字元，用來參考內建實值類型。 對應如下所示：

-  `"B"` 如`sbyte`。
-  `"S"` 如`short`。
-  `"I"` 如`int`。
-  `"J"` 如`long`。
-  `"F"` 如`float`。
-  `"D"` 如`double`。
-  `"C"` 如`char`。
-  `"Z"` 如`bool`。
-  `"V"` 如`void`方法的傳回型別。


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>簡化的型別參考

簡化的型別參考只可用在[JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))。
有兩種方式可衍生簡單的類型參考：

1.  從完整的 Java 名稱，取代每個`'.'`內封裝名稱和類型名稱，與之前`'/'`，和每`'.'`內包含的型別名稱`'$'`。

1.  讀取輸出的`'unzip -l android.jar | grep JavaName'`。


上述兩個會導致 Java 類型[java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html)對應的簡化型別參考到`java/lang/Thread$State`。


### <a name="type-references"></a>型別參考

型別參考是內建型別參考或使用簡化的型別參考`'L'`前置詞和`';'`後置詞。 Java 型別的[java.lang.String](http://developer.android.com/reference/java/lang/String.html)，簡化的型別參考`"java/lang/String"`，而型別參考`"Ljava/lang/String;"`。

陣列型別參考與 JNI 簽章，會使用型別參考。

所讀取的輸出是以其他方式來取得型別參考`'javap -s -classpath android.jar fully.qualified.Java.Name'`。
視類型而定，您可以使用建構函式宣告或方法傳回類型，以判斷 JNI 名稱。 例如: 

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` Java 列舉型別，所以我們可以使用的簽章`valueOf`方法來判斷型別參考是 Ljava/l a n g 執行緒 $狀態;。



### <a name="array-type-references"></a>陣列型別參考

陣列類型的參考是`'['`前置 JNI 類型參考。
指定陣列時，就無法使用簡化的型別參考。

例如，`int[]`是`"[I"`，`int[][]`是`"[[I"`，和`java.lang.Object[]`是`"[Ljava/lang/Object;"`。



## <a name="java-generics-and-type-erasure"></a>Java 泛型和類型清除

*大部分*的情況下，透過 JNI，Java 泛型所見*不存在於*。
某些"縐折的程度，"，但這些縐折的程度是在 Java 使用泛型時，不能搭配 JNI 查閱的方式，並叫用泛型成員的互動方式。

透過 JNI 互動時，沒有任何泛型類型或成員和非泛型類型或成員之間的差異。 例如，泛型型別[java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html)也是 「 原始 」 的泛型型別`java.lang.Class`，兩者都擁有相同的簡化型別參考， `"java/lang/Class"`。


## <a name="java-native-interface-support"></a>Java 原生介面支援

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)如 Jave 原生介面 (JNI) 是 managed 包裝函式。 JNI 函式中宣告了[Java 原生介面規格](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)，但是方法已經變更為移除明確`JNIEnv*`參數和`IntPtr`而非`jobject`， `jclass`，`jmethodID`等等。例如，請考慮[JNI NewObject 函式](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

這會公開為[JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)方法：

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

在兩次呼叫之間進行轉譯是相當簡單。 在 C 中，您就必須：

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

會使用 C# 對等項目：

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

一旦您擁有的 Java 物件執行個體保留為 IntPtr，可能會想要進行編輯。 您可以使用 JNIEnv 方法，例如[ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)若要這樣做，但如果已經有的類比 C# 的包裝函式，您會想要透過 JNI 參考建構的包裝函式。 您可以如此執行[Extensions.JavaCast <t>（)</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/)擴充方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

您也可以使用[Java.Lang.Object.GetObject <t>（)</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外，所有 JNI 函式已被修改移除`JNIEnv*`存在於每個 JNI 函式的參數。


## <a name="summary"></a>總結

直接處理 JNI 是可怕的體驗，您就應該避免代價。 不幸的是，您不一定屆時;希望本指南將提供一些協助，當您遇到未繫結的 Java 案例與 Mono for Android 時。


## <a name="related-links"></a>相關連結

- [SanityTests （範例）](https://developer.xamarin.com/samples/SanityTests/)
- [Java 原生介面規格](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java 原生介面函式](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
