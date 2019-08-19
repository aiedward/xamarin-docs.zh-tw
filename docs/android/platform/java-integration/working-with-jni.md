---
title: 使用 JNI 和 Xamarin
description: Xamarin 允許使用C# (而不是 JAVA) 來撰寫 android 應用程式。 Xamarin 提供了數個元件, 提供 JAVA 程式庫的系結, 包括 Mono. GoogleMaps 和 Mono。 不過, 不會為每個可能的 JAVA 程式庫提供系結, 而且所提供的系結可能不會系結每個 JAVA 類型和成員。 若要使用未系結的 JAVA 類型和成員, 可以使用 JAVA 原生介面 (JNI)。 本文說明如何使用 JNI, 與 Xamarin. Android 應用程式中的 JAVA 類型和成員互動。
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 2acc57a65f9a9dfb49088c17b5b4cfac7e9d2ab6
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680872"
---
# <a name="working-with-jni-and-xamarinandroid"></a>使用 JNI 和 Xamarin

_Xamarin 允許使用C# (而不是 JAVA) 來撰寫 android 應用程式。Xamarin 提供了數個元件, 提供 JAVA 程式庫的系結, 包括 Mono. GoogleMaps 和 Mono。不過, 不會為每個可能的 JAVA 程式庫提供系結, 而且所提供的系結可能不會系結每個 JAVA 類型和成員。若要使用未系結的 JAVA 類型和成員, 可以使用 JAVA 原生介面 (JNI)。本文說明如何使用 JNI, 與 Xamarin. Android 應用程式中的 JAVA 類型和成員互動。_

## <a name="overview"></a>總覽

您不一定要建立受控可呼叫包裝函式 (MCW) 來叫用 JAVA 程式碼。 在許多情況下, 「內嵌」 JNI 是完全可接受的, 並且適用于一次使用未系結的 JAVA 成員。 使用 JNI 來叫用 JAVA 類別上的單一方法, 通常會比產生整個 .jar 系結來得簡單。

Xamarin 提供的`Mono.Android.dll`元件會提供`android.jar` Android 程式庫的系結。 不存在於和類型`Mono.Android.dll` `android.jar`中的類型和成員, 可能會透過手動系結來使用。 若要系結 JAVA 類型和成員, 您可以使用**JAVA 原生介面**(**JNI**) 來查閱類型、讀取和寫入欄位, 以及叫用方法。

Xamarin 中的 JNI API 概念上與 .net 中的`System.Reflection` api 非常類似: 它可讓您依名稱、讀取和寫入域值、叫用方法等等來查詢類型和成員。 您可以使用 JNI 和`Android.Runtime.RegisterAttribute`自訂屬性來宣告可系結以支援覆寫的虛擬方法。 您可以系結介面, 讓它們可以在中C#執行。

本檔說明:

- JNI 如何參考類型。
- 如何查閱、讀取和寫入欄位。
- 如何查閱和叫用方法。
- 如何公開虛擬方法, 以允許從 managed 程式碼覆寫。
- 如何公開介面。

## <a name="requirements"></a>需求

JNI (如[JNIEnv 命名空間](xref:Android.Runtime.JNIEnv)所公開) 可用於每個 Xamarin. Android 版本。
若要系結 JAVA 類型和介面, 您必須使用 Xamarin. Android 4.0 或更新版本。

## <a name="managed-callable-wrappers"></a>受控可呼叫包裝函式

受控可呼叫**包裝**函式 (**MCW**) 是 JAVA 類別或介面的系結, 它會包裝所有 JNI 的機制, 讓C#用戶端程式代碼不需要擔心 JNI 的基礎複雜度。 大部分是`Mono.Android.dll`由 managed 可呼叫包裝函式所組成。

受控可呼叫包裝函式有兩個用途:

1.  封裝 JNI 使用, 讓用戶端程式代碼不需要知道基礎複雜度。
1.  讓子類別的 JAVA 類型得以執行, 並實作為 JAVA 介面。

第一個目的純粹是為了方便和封裝複雜度, 讓取用者擁有一組簡單、受管理的類別來使用。 這需要使用各種[JNIEnv](xref:Android.Runtime.JNIEnv)成員, 如本文稍後所述。 請記住, 受控可呼叫包裝函式並&ndash;不是絕對必要的「內嵌」 JNI 用法, 非常適合用來一次使用未系結的 JAVA 成員。 子 classing 和介面的執行需要使用受控可呼叫包裝函式。

## <a name="android-callable-wrappers"></a>Android 可呼叫包裝函式

每當 Android 執行時間 (美工) 需要叫用 managed 程式碼時, 就需要 android 可呼叫包裝函式 (ACW);這些包裝函式是必要的, 因為在執行時間沒有任何方法可將類別註冊為美工圖案。
(具體而言, Android 執行時間不支援[DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI 函數。 因此, Android 可呼叫包裝函式會產生不足的執行時間類型註冊支援)。

每當 Android 程式碼需要執行在 managed 程式碼中覆寫或執行的虛擬或介面方法時, Xamarin。 Android 必須提供 JAVA proxy, 才能將此方法分派至適當的 managed 類型。 這些 JAVA proxy 類型是 JAVA 程式碼, 其具有「相同」基類和 JAVA 介面清單做為 managed 類型, 它會執行相同的函式, 並宣告任何覆寫的基類和介面方法。

Android 可呼叫包裝函式會在[建立程式](~/android/deploy-test/building-apps/build-process.md)期間由**monodroid**產生, 而且會針對 (直接或間接) 繼承 [Java.Lang.Object](xref:Java.Lang.Object) 的所有類型產生。

### <a name="implementing-interfaces"></a>實作介面

有時候, 您可能需要實作為 Android 介面 (例如[IComponentCallbacks](xref:Android.Content.IComponentCallbacks))。

所有 Android 類別和介面都會擴充[IJAVAObject](xref:Android.Runtime.IJavaObject)介面;因此, 所有 Android 類型都必須`IJavaObject`執行。
Xamarin 會利用這項事實&ndash; `IJavaObject` , 為指定的 managed 類型提供具有 JAVA proxy (android 可呼叫包裝函式) 的 android。 由於**monodroid**只會尋找`Java.Lang.Object`子類別 (必須`Java.Lang.Object`執行`IJavaObject`), 因此子類別化提供了在 managed 程式碼中執行介面的方法。 例如：

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

*本文的其餘部分提供可能變更的執行詳細資料, 恕不另行通知*(這只會在這裡提供, 因為開發人員可能會想知道幕後的功能)。

例如, 假設有下列C#來源:

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

**Mandroid**程式將會產生下列 Android 可呼叫包裝函式:

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

請注意, 會保留基類, 並針對在 managed 程式碼中覆寫的每個方法提供原生方法宣告。



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute

一般來說, Xamarin 會自動產生包含 ACW 的 JAVA 程式碼;當類別衍生自 JAVA 類別並覆寫現有的 JAVA 方法時, 此層代是以類別和方法名稱為基礎。 不過, 在某些情況下, 程式碼產生並不足夠, 如下所述:

-   Android 支援版面配置 XML 屬性中的動作名稱, 例如[android: onClick](xref:Android.Views.View.IOnClickListener.OnClick*) XML 屬性。 當指定時, 膨脹的 View 實例會嘗試查詢 JAVA 方法。

-   JAVA. 的[Serializable](https://developer.android.com/reference/java/io/Serializable.html)介面需要`readObject`和`writeObject`方法。 因為它們不是這個介面的成員, 所以對應的 managed 執行並不會將這些方法公開給 JAVA 程式碼。

-   [Parcelable](xref:Android.OS.Parcelable)介面預期執行類別必須有類型`CREATOR` `Parcelable.Creator`的靜態欄位。 產生的 JAVA 程式碼需要一些明確的欄位。 在我們的標準案例中, 沒有任何方法可從 managed 程式碼輸出 JAVA 程式碼中的欄位。

因為程式碼產生並未提供可產生任意名稱之任意 JAVA 方法的解決方案, 所以從 Xamarin. Android 4.2 開始, 引進了[ExportAttribute](xref:Java.Interop.ExportAttribute)和[ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute)來提供上述解決方案場景. 這兩個屬性都`Java.Interop`位於命名空間中:

-   `ExportAttribute`&ndash;指定方法名稱及其預期的例外狀況類型 (以 JAVA 提供明確的「擲回」)。 在方法上使用時, 此方法會「匯出」 JAVA 方法, 以產生分派程式碼給 managed 方法的對應 JNI 調用。 這可與`android:onClick`和`java.io.Serializable`搭配使用。

-   `ExportFieldAttribute`&ndash;指定功能變數名稱。 它位於當做欄位初始化運算式運作的方法上。 這可與`android.os.Parcelable`搭配使用。

[ExportAttribute](https://docs.microsoft.com/samples/xamarin/monodroid-samples/exportattribute)範例專案說明如何使用這些屬性。


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>疑難排解 ExportAttribute 和 ExportFieldAttribute

-   封裝 &ndash;失敗, 因為如果您在程式碼或相依程式庫的某些方法`ExportFieldAttribute`上使用`ExportAttribute`或, 就必須加入**mono. 匯出**.dll。 此元件已隔離, 以支援來自 JAVA 的回呼程式碼。 它與**Mono**不同, 因為它會將額外的大小新增至應用程式。

-   在發行組建中`MissingMethodException` , 匯出方法會&ndash;發生在發行組建`MissingMethodException`中的匯出方法。 (這個問題在最新版本的 Xamarin 中已修正)。



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute`和`ExportFieldAttribute`提供 JAVA 執行時間程式碼可以使用的功能。 這個執行時間程式碼會透過這些屬性所驅動的產生 JNI 方法, 來存取 managed 程式碼。 因此, managed 方法不會系結任何現有的 JAVA 方法;因此, JAVA 方法是從 managed 方法簽章產生。

不過, 這種情況並不完全行列式。 最值得注意的是, 在 managed 類型和 JAVA 類型之間的一些先進對應中, 這是如此, 例如:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

如果匯出的方法需要這些型別, 則`ExportParameterAttribute`必須用來明確地為對應的參數或傳回值提供類型。



### <a name="annotation-attribute"></a>Annotation 屬性

在 Xamarin 4.2 中, 我們將實`IAnnotation`作為型別轉換成屬性 (system.string), 並在 JAVA 包裝函式中新增注釋產生的支援。

這表示下列方向性變更:

-   系結產生器`Java.Lang.DeprecatedAttribute`會`java.Lang.Deprecated`從 (應該是`[Obsolete]`在 managed 程式碼中) 產生。

-   這並不表示現有`Java.Lang.Deprecated`的類別將會消失。 這些以 JAVA 為基礎的物件仍然可以當做平常的 JAVA 物件使用 (如果有這類用法的話)。 將`Deprecated`會有和`DeprecatedAttribute`類別。

-   類別會標示為`[Annotation]`。 `Java.Lang.DeprecatedAttribute` 當此`[Annotation]`屬性繼承自訂屬性時, msbuild 工作會在 Android 可呼叫包裝函式 (ACW) 中產生該@Deprecated自訂屬性 () 的 JAVA 注釋。

-   批註可以產生到類別、方法和匯出的欄位 (這是 managed 程式碼中的方法)。

如果包含的類別 (批註類別本身, 或包含批註成員的類別) 未註冊, 則完全不會產生整個 JAVA 類別來源, 包括批註。 針對方法, 您可以指定`ExportAttribute`來取得明確產生和標注的方法。 此外, 它並不是「產生」 JAVA 批註類別定義的功能。 換句話說, 如果您為某個注釋定義了自訂的 managed 屬性, 就必須加入另一個包含對應 JAVA 批註類別的 .jar 程式庫。 新增定義注釋類型的 JAVA 原始程式檔並不足夠。 JAVA 編譯器的運作方式與**apt**相同。

此外, 也適用下列限制:

-   此轉換程式在目前為止`@Target`不會考慮注釋類型的注釋。

-   屬性上的屬性無法運作。 請改用屬性 getter 或 setter 的屬性。



## <a name="class-binding"></a>類別系結

系結類別表示撰寫 managed 可呼叫包裝函式, 以簡化基礎 JAVA 類型的調用。

系結虛擬和抽象方法以允許從C#覆寫, 需要 Xamarin. Android 4.0。 不過, 任何版本的 Xamarin 都可以系結非虛擬方法、靜態方法或虛擬方法, 而不支援覆寫。

系結通常包含下列專案:

-  要系結之[JAVA 類型的 JNI 控制碼](#_Looking_up_Java_Types)。

-  [JNI 每個系結欄位的欄位識別碼和屬性](#_Instance_Fields)。

-  [JNI 每個系結方法的方法識別碼和方法](#_Instance_Methods)。

-  如果需要子 classing, 此類型在類型宣告上必須要有[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自訂屬性, 並將[RegisterAttribute](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)設定為`true`。



### <a name="declaring-type-handle"></a>宣告類型控制碼

欄位和方法查閱方法需要參考其宣告類型的物件參考。 依照慣例, 這會保留在`class_ref`欄位中:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

如需`CLASS`權杖的詳細資訊, 請參閱[JNI 類型參考](#_JNI_Type_References)一節。


### <a name="binding-fields"></a>系結欄位

JAVA 欄位會公開為C#屬性, 例如, java field [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in)會系結為C#屬性[JAVA.Lang.JAVASystem.In](xref:Java.Lang.JavaSystem.In)。
此外, 由於 JNI 會區分靜態欄位和實例欄位, 因此在執行屬性時, 會使用不同的方法。

欄位系結包含三組方法:

1.  *取得欄位識別碼*方法。 *取得欄位識別碼*方法會負責傳回「*取得域值*」和「*設定域值*」方法將使用的欄位控制碼。 取得欄位識別碼需要知道宣告類型、欄位的名稱, 以及欄位的[JNI 類型](#JNI_Type_Signatures)簽章。

1.  *取得域值*方法。 這些方法需要欄位控制碼, 而且必須負責從 JAVA 讀取欄位的值。
    要使用的方法取決於欄位的類型。

1.  *設定域值*方法。 這些方法需要欄位控制碼, 而且必須負責在 JAVA 中寫入欄位的值。 要使用的方法取決於欄位的類型。

[靜態欄位](#_Static_Fields)會使用[JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)、 `JNIEnv.GetStatic*Field`和[JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)方法。

 [實例欄位](#_Instance_Fields)會使用[JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*)、 `JNIEnv.Get*Field`和[JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)方法。

例如, 靜態屬性`JavaSystem.In`可以實作為:

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

注意:我們會使用[InputStreamInvoker](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*)將 JNI 參考轉換成`System.IO.Stream`實例, 而且我們正在使用`JniHandleOwnership.TransferLocalRef` , 因為[JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*)會傳回區域參考。

許多[Android. 運行](xref:Android.Runtime)時間類型都有`FromJniHandle`方法, 可將 JNI 參考轉換成所需的類型。

### <a name="method-binding"></a>方法系結

JAVA 方法會公開為C#方法和C#屬性。 例如, [runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean))方法的 java 方法會系結為[runFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*)方法, 而[getClass](https://developer.android.com/reference/java/lang/Object.html#getClass)方法則會系結為[.java. Class. 類別 (Class) 的物件 (Class)。](xref:Java.Lang.Object.Class)屬性。

方法調用是兩個步驟的程式:

1.  要叫用之方法的*get 方法識別碼*。 *Get 方法 id*方法會負責傳回方法調用方法將使用的方法控制碼。 取得方法識別碼需要知道宣告類型、方法的名稱, 以及方法的[JNI 類型](#JNI_Type_Signatures)簽章。

1.  叫用方法。

就像欄位一樣, 用來取得方法識別碼和叫用方法的方法, 在靜態方法和實例方法之間有所不同。

[靜態方法](#_Static_Methods_1)會使用[JNIEnv. GetStaticMethodID ()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)來查閱方法識別碼, `JNIEnv.CallStatic*Method`並使用系列的方法來進行調用。

[實例方法](#_Instance_Methods)會使用[JNIEnv](xref:Android.Runtime.JNIEnv.GetMethodID*)來查閱方法 id, `JNIEnv.Call*Method`並使用方法的和`JNIEnv.CallNonvirtual*Method`系列來進行調用。

方法系結可能不只是方法調用。 方法系結也包括允許覆寫方法 (適用于抽象和非最終方法) 或實作為 (針對介面方法)。 [支援的繼承, 介面](#_Supporting_Inheritance,_Interfaces_1)一節涵蓋支援虛擬方法和介面方法的複雜性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>靜態方法

系結靜態方法牽涉到`JNIEnv.GetStaticMethodID`使用來取得方法控制碼, 然後使用適當`JNIEnv.CallStatic*Method`的方法, 視方法的傳回型別而定。 以下是[getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法的系結範例:

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

請注意, `id_getRuntime`我們會將方法控制碼儲存在靜態欄位中。 這是效能優化, 因此不需要在每次叫用時查閱方法控制碼。 不需要以這種方式快取方法控制碼。 取得方法控制碼之後, 會使用[JNIEnv CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)來叫用方法。 `JNIEnv.CallStaticObjectMethod`傳回, `IntPtr`其中包含傳回之 JAVA 實例的控制碼。
將 java 控制碼轉換成強型[&lt;別&gt;](xref:Java.Lang.Object.GetObject*)物件實例時, 會使用 JniHandleOwnership 的物件。



#### <a name="non-virtual-instance-method-binding"></a>非虛擬實例方法系結

系結`JNIEnv.GetMethodID` `JNIEnv.Call*Method`實例方法或不需要覆寫的實例方法時, 需要使用來取得方法控制碼, 然後使用適當的方法 (視方法的傳回型別而定)。 `final` 以下是`Object.Class`屬性系結的範例:

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

請注意, `id_getClass`我們會將方法控制碼儲存在靜態欄位中。
這是效能優化, 因此不需要在每次叫用時查閱方法控制碼。 不需要以這種方式快取方法控制碼。 取得方法控制碼之後, 會使用[JNIEnv CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)來叫用方法。 `JNIEnv.CallStaticObjectMethod`傳回, `IntPtr`其中包含傳回之 JAVA 實例的控制碼。
將 java 控制碼轉換成強型[&lt;別&gt;](xref:Java.Lang.Object.GetObject*)物件實例時, 會使用 JniHandleOwnership 的物件。


### <a name="binding-constructors"></a>系結函式

「處理函式」是具有`"<init>"`名稱的 JAVA 方法。 就像使用 JAVA 實例方法一樣`JNIEnv.GetMethodID` , 是用來查閱函數控制碼。 不同于 JAVA 方法, [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法是用來叫用函式方法控制碼。 的傳回值`JNIEnv.NewObject`是 JNI 區域參考:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

一般來說, 類別系結將會子類別化[JAVA. Lang.ini 物件](xref:Java.Lang.Object)。
`Java.Lang.Object`子`Java.Lang.Object.Handle`類別化時, 會有額外的語義可供玩: 實例會透過屬性來維護 JAVA 實例的全域參考。 `Java.Lang.Object`

1.  預設`Java.Lang.Object`的函式會配置 JAVA 實例。

1.  如果類型具有`RegisterAttribute` , 且`RegisterAttribute.DoNotGenerateAcw`為`true` , 則會透過其預設的處理`RegisterAttribute.Name`程式建立類型的實例。

1.  否則, 對應至`this.GetType`的 [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) 會透過其預設的處理常式具現化。 在封裝建立期間, 會針對每個`Java.Lang.Object`未設定為的子類別`RegisterAttribute.DoNotGenerateAcw` , 產生`true`Android 可呼叫包裝函式。

對於不是類別系結的類型, 這是預期的語義: 具`Mono.Samples.HelloWorld.HelloAndroid`現化C#實例應該會`mono.samples.helloworld.HelloAndroid`建立 JAVA 實例, 這是產生的 Android 可呼叫包裝函式。

針對類別系結, 如果 JAVA 類型包含預設的函式, 以及 (或) 不需要叫用其他的函式, 這可能是正確的行為。 否則, 必須提供一個可執行下列動作的函式:

1.  叫用 JniHandleOwnership [,](xref:Java.Lang.Object#ctor*)而不是預設`Java.Lang.Object`的函式。 若要避免建立新的 JAVA 實例, 這是必要的。

1.  建立任何 JAVA 實例之前, 請先檢查 [Java.Lang.物件控點](xref:Java.Lang.Object.Handle)的值。 如果在 JAVA 程式碼中建立了 android 可呼叫包裝函式, 且類別系結正在進行結構化以包含已建立的 android 可呼叫包裝函式實例, 則`Object.Handle`屬性會有以外的值。 `IntPtr.Zero` 例如`mono.samples.helloworld.HelloAndroid` , 當 android 建立實例時, 會先建立 android 可呼叫包裝函式, 而 JAVA `HelloAndroid`處理常式會建立`Object.Handle`對應`Mono.Samples.HelloWorld.HelloAndroid`類型的實例, 並將屬性在執行函式之前, 將設定為 JAVA 實例。

1.  如果目前的執行時間類型與宣告類型不同, 則必須建立對應之 Android 可呼叫包裝函式的實例, 並使用[SetHandle](xref:Java.Lang.Object.SetHandle*)來儲存[JNIEnv](xref:Android.Runtime.JNIEnv.CreateInstance*)所傳回的控制碼。

1.  如果目前的執行時間型別與宣告型別相同, 則會叫用 JAVA 處理常式 `JNIEnv.NewInstance` , 並使用 [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) 來儲存所傳回的控制碼。

例如, 請考慮採用[.JAVA 整數 (int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int))的函式。 這會系結為:

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

[JNIEnv](xref:Android.Runtime.JNIEnv.CreateInstance*)方法`JNIEnv.FindClass`是 helper, 可在從`JNIEnv.NewObject` `JNIEnv.GetMethodID` `JNIEnv.DeleteGlobalReference` 傳回的`JNIEnv.FindClass`值上執行、、和。 如需詳細資訊，請參閱下一節。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支援繼承, 介面

將 java 類型子類別化或執行 java 介面時, 需要產生可在封裝程式期間針對每個`Java.Lang.Object`子類別產生的[ Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs)。 ACW 產生是透過[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自訂屬性來控制。

針對C#類型, 自`[Register]`定義屬性的函式需要一個引數: 對應 JAVA 類型的[JNI 簡化型別參考](#_Simplified_Type_References_1)。 這可讓您在 JAVA 和C#之間提供不同的名稱。

在`[Register]` Xamarin. Android 4.0 之前, 自訂屬性無法用於「別名」現有的 JAVA 類型。 這是因為 ACW 產生進程會針對每個`Java.Lang.Object`遇到的子類別產生 ACWs。

Xamarin. Android 4.0 引進了[RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)屬性。 這個屬性會指示 ACW 產生進程*略過*批註類型, 允許宣告新的 Managed 可呼叫包裝函式, 而不會導致在封裝建立期間產生 ACWs。 這可讓您系結現有的 JAVA 類型。 例如, 請考慮下列簡單的 JAVA 類別, `Adder`其中包含一個方法, `add`它會將新增至整數並傳回結果:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder`型別可以系結為:

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

`Adder` C#在這裡, 類型會將`Adder` JAVA 類型做為*別名*。 屬性是用來指定`mono.android.test.Adder` `DoNotGenerateAcw` JAVA 類型的 JNI 名稱, 而屬性則是用來抑制 ACW 的產生。 `[Register]` 這會產生`ManagedAdder`類型的 ACW, 並適當地將`mono.android.test.Adder`類型子類別化。 如果尚未使用`mono.android.test.Adder` 屬性,則Xamarin.Android組建程式會產生新`RegisterAttribute.DoNotGenerateAcw`的 JAVA 類型。 這會導致編譯錯誤, 因為在兩`mono.android.test.Adder`個不同的檔案中, 類型會出現兩次。



### <a name="binding-virtual-methods"></a>系結虛擬方法

`ManagedAdder`將 JAVA `Adder`類型子類別化, 但它並不特別C# `Adder`有趣: 類型不會定義任何虛擬`ManagedAdder`方法, 因此無法覆寫任何專案。

允許子類別覆寫的系結方法需要進行幾項需要完成的動作,而這兩個類別可分為下列兩種:`virtual`

1.  **方法系結**

1.  **方法註冊**


#### <a name="method-binding"></a>方法系結

方法系結需要在C# `Adder`定義中加上兩個支援成員: `ThresholdType`、和`ThresholdClass`。

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType`屬性會傳回目前的系結類型:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`會在方法系結中用來判斷它應該執行虛擬與非虛擬方法分派的時機。 它應該一律`System.Type`會傳回對應至宣告C#類型的實例。

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass`屬性會傳回系結類型的 JNI 類別參考:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`叫用非虛擬方法時, 會在方法系結中使用。

#### <a name="binding-implementation"></a>系結執行

方法系結程式會負責執行 JAVA 方法的執行時間調用。 它也包含`[Register]`屬於方法註冊一部分的自訂屬性宣告, 並會在方法註冊一節中討論:

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

`id_add`欄位包含要叫用的 JAVA 方法的方法識別碼。 `JNIEnv.GetMethodID` `class_ref` `"add"`值是從取得, 這需要宣告類別 ()、JAVA 方法名稱 () 和方法的 JNI 簽章 (`"(II)I"`)。 `id_add`

取得方法識別碼之後, 會`GetType` `ThresholdType`與進行比較, 以判斷是否需要虛擬或非虛擬分派。 當比對時`GetType` `ThresholdType`, 需要虛擬分派`Handle` , 因為它可能會參考 JAVA 配置的子類別, 這會覆寫方法。

當`GetType`不相符`ThresholdType`時`Adder` , 已子類別化 (例如`ManagedAdder`), `base.Add`而且只`Adder.Add`會在叫用子類別時叫用此實作為。 這是非虛擬分派案例, 也就是中的`ThresholdClass`位置。 `ThresholdClass`指定哪一個 JAVA 類別將提供要叫用之方法的執行。



#### <a name="method-registration"></a>方法註冊

假設我們有一個已`ManagedAdder`更新的`Adder.Add`定義, 它會覆寫方法:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

回想一下`Adder.Add` , `[Register]`有自訂屬性:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

自`[Register]`定義屬性的函數會接受三個值:

1.  JAVA 方法的名稱, `"add"`在此案例中為。

1.  方法的 JNI 類型簽章, `"(II)I"`在此案例中為。

1.  *連接器方法*, `GetAddHandler`在此案例中為。
    連接器方法將于稍後討論。


前兩個參數可讓 ACW 產生進程產生方法宣告, 以覆寫方法。 產生的 ACW 會包含下列一些程式碼:

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

請注意, 已宣告`n_`方法,其會委派至具有相同名稱的首碼方法。`@Override` 這可確保叫用 JAVA 程式`ManagedAdder.add`代碼`ManagedAdder.n_add`時, 將會叫用, 以允許C# `ManagedAdder.Add`執行覆寫方法。

因此, 最重要的問題: 如何`ManagedAdder.n_add`連接到？ `ManagedAdder.Add`

JAVA `native`方法會透過[JNI RegisterNatives 函數](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)向 java (Android 執行時間) 執行時間註冊。
`RegisterNatives`採用結構的陣列, 其中包含 JAVA 方法名稱、JNI 類型簽章, 以及會遵循[JNI 呼叫慣例叫](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)用的函式指標。
函式指標必須是接受兩個指標引數, 後面接著方法參數的函式。 JAVA `ManagedAdder.n_add`方法必須透過具有下列 C 原型的函式來執行:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin 不會公開`RegisterNatives`方法。 相反地, ACW 和 MCW 會提供叫用所需`RegisterNatives`的資訊: ACW 包含方法名稱和 JNI 類型簽章, 唯一缺少的就是要連結的函式指標。

這是*連接器方法*的來源位置。 第三`[Register]`個自訂屬性參數是已註冊型別中所定義的方法名稱, 或是不接受任何參數並`System.Delegate`傳回的註冊型別之基類。 而傳回`System.Delegate`的則是指具有正確 JNI 函數簽章的方法。 最後, 連接器方法所傳回的委派*必須*是 root, 如此一來, 當委派提供給 JAVA 時, GC 才不會收集它。

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

方法會建立參考`Func<IntPtr, IntPtr, int, int,
int>` `n_Add`方法的委派, 然後叫用[JNINativeWrapper system.delegate.createdelegate。](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*) `GetAddHandler`
`JNINativeWrapper.CreateDelegate`將提供的方法包裝在 try/catch 區塊中, 以處理任何未處理的例外狀況, 並導致引發[AndroidEvent. UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser)事件。 產生的委派會儲存在靜態`cb_add`變數中, 因此 GC 不會釋放委派。

最後, `n_Add`方法會負責將 JNI 參數封送處理至對應的 managed 類型, 然後委派方法呼叫。

注意:透過 JAVA `JniHandleOwnership.DoNotTransfer`實例取得 MCW 時, 請一律使用。 將它們視為本機參考 (因而呼叫`JNIEnv.DeleteLocalRef`) 將會中斷&gt; managed JAVA&gt;管理的堆疊轉換。

### <a name="complete-adder-binding"></a>完成的連結連結

`mono.android.tests.Adder`類型的完整 managed 系結為:

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

撰寫符合下列準則的類型時:

1.  超`Java.Lang.Object`

1.  `[Register]`具有自訂屬性

1.  `RegisterAttribute.DoNotGenerateAcw` 是 `true`


然後, 對於 GC 互動, 類型*不*能有任何可能在執行時間參考`Java.Lang.Object`或`Java.Lang.Object`子類別的欄位。 例如, 不允許類型`System.Object`的欄位和任何介面類別型。 不能參考`Java.Lang.Object`實例的類型 ( `System.String`例如和`List<int>`) 是允許的。 這項限制是為了防止 GC 過早的物件收集。

如果類型必須包含可以參考`Java.Lang.Object`實例的實例欄位, 則欄位類型必須是`System.WeakReference`或`GCHandle`。



## <a name="binding-abstract-methods"></a>系結抽象方法

系`abstract`結方法主要等同于系結虛擬方法。 只有兩個差異:

1.  Abstract 方法是抽象的。 它仍然會保留`[Register]`屬性和相關聯的方法註冊, 方法系結只會移`Invoker`至型別。

1.  建立的非`abstract` `Invoker`類型是子類別化抽象類別型。 `Invoker`類型必須覆寫在基類中宣告的所有抽象方法, 而覆寫的實作為方法系結執行, 不過可以忽略非虛擬分派案例。


例如, 假設上述`mono.android.test.Adder.add` `abstract`方法為。 C#系結會變更, 使`Adder.Add`成為抽象, 而且會定義`AdderInvoker` `Adder.Add`實作為新的型別:

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

只有`Invoker`在取得對 JAVA 所建立之實例的 JNI 參考時, 才需要此類型。


## <a name="binding-interfaces"></a>系結介面

系結介面在概念上類似于包含虛擬方法的系結類別, 但許多細節在微妙 (而不是微妙) 方面有所不同。 請考慮下列[JAVA 介面聲明](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

介面系結有兩個部分C# : 介面定義和介面的啟動程式定義。



### <a name="interface-definition"></a>介面定義

C#介面定義必須滿足下列需求:

-   介面定義必須有`[Register]`自訂屬性。

-   介面定義必須擴充`IJavaObject interface`。
    若未這麼做, 將會導致 ACWs 無法繼承自 JAVA 介面。

-   每個介面方法都必須`[Register]`包含屬性, 以指定對應的 JAVA 方法名稱、JNI 簽章和連接器方法。

-   連接器方法也必須指定連接器方法可以位於的類型。

當系結`virtual`和方法時, 連接器方法會在要註冊之型別的繼承階層架構中搜尋。 `abstract` 介面不能有任何包含內文的方法, 因此這不會有作用, 因此必須指定類型, 指示連接器方法所在的位置。 類型是在連接器方法字串內的冒號`':'`後面指定, 而且必須是包含啟動程式之類型的元件限定類型名稱。

介面方法宣告是使用*相容*的類型來轉譯對應的 JAVA 方法。 針對 java 內建類型, 相容的類型是對應C#的類型, 例如 JAVA `int`是C# `int`。 對於參考型別, 相容的類型是可以提供適當 JAVA 類型之 JNI 控制碼的類型。

JAVA &ndash;調用將不會直接叫用介面成員, 將會透過啟動程式類型&ndash;來 mediated, 因此允許某種程度的彈性。

JAVA 進度介面可以[在中C#宣告為](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

請注意, 在上述的中, 我們`int[]`會將 JAVA 參數對應至[JAVAArray&lt;int&gt; ](xref:Android.Runtime.JavaArray`1)。
這不是必要的C# `int[]`: 我們可以將它系結至、 `IList<int>`或, 或完全系結至其他專案。 無論選擇何種類型, `Invoker`都必須能夠將它轉譯成 JAVA `int[]`類型, 才能進行調用。

### <a name="invoker-definition"></a>啟動碼定義

型別定義必須繼承`Java.Lang.Object`、執行適當的介面, 並提供在介面定義中參考的所有連接方法。 `Invoker` 還有一個與類別系結不同的建議: `class_ref`欄位和方法識別碼應該是實例成員, 而不是靜態成員。

偏好實例成員的原因與`JNIEnv.GetMethodID` Android 執行時間中的行為有關。 (這也可能是 JAVA 行為, 尚未經過測試)。`JNIEnv.GetMethodID`查閱來自實介面的方法, 而非宣告的介面時, 會傳回 null。 請考慮[util&lt;. SortedMap k&gt; , v](https://developer.android.com/reference/java/util/SortedMap.html) java 介面, 它會執行[util. Map&lt;k, v&gt; ](https://developer.android.com/reference/java/util/Map.html)介面。 Map 提供[清楚](https://developer.android.com/reference/java/util/Map.html#clear())的方法, 因此 SortedMap 的看似`Invoker`合理定義如下:

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

上述動作會失敗, `JNIEnv.GetMethodID`因為`null`在透過`SortedMap`類別實例查閱`Map.clear`方法時, 會傳回。

這有兩個解決方案: 追蹤每個方法的來源介面, 並`class_ref`針對每個介面使用, 或將所有專案都保留為實例成員, 並對最多衍生的類別類型執行方法查閱, 而不是介面類別型。 後者是在 Mono. **.dll**中完成。

啟動程式定義有六個區段: 函式、 `Dispose`方法`ThresholdType` 、和`ThresholdClass`成員、 `GetObject`方法、介面方法執行, 以及連接器方法的執行。



#### <a name="constructor"></a>建構函式

此函式必須查閱所叫用之實例的執行時間類別, 並將執行時間類別儲存`class_ref`在實例欄位中:

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

注意:屬性必須在函式主體內使用, 而`handle`不是參數, 如同`handle`在 Android v4.0 上, 在基底函式完成執行之後, 參數可能無效。 `Handle`


#### <a name="dispose-method"></a>Dispose 方法

`Dispose`方法必須釋放在此函數中配置的全域參考:

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


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType 和 ThresholdClass

`ThresholdType` 和`ThresholdClass`成員與類別系結中找到的相同:

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

必須要`GetObject`有靜態方法, 才能支援[擴充功能&lt;。&gt;JAVACast T ()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>介面方法

介面的每個方法都必須有一個執行, 它會透過 JNI 叫用對應的 JAVA 方法:

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

連接器方法和支援的基礎結構會負責將 JNI 參數封送處理C#為適當的類型。 JAVA `int[]`參數將會以 JNI `jintArray`的形式傳遞, 這是內`IntPtr` C#的。 必須封送處理`JavaArray<int>`至, 才能支援叫用C#介面: `IntPtr`

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

如果`int[]`優先使用,則可以改用[JNIEnvGetArray()](xref:Android.Runtime.JNIEnv.GetArray*):`JavaList<int>`

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

不過, 請注意, `JNIEnv.GetArray`這會在 vm 之間複製整個陣列, 因此對於大型陣列, 這可能會導致許多增加的 GC 壓力。


### <a name="complete-invoker-definition"></a>完成啟動的定義

[完整的 IAdderProgressInvoker 定義](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

許多 JNIEnv 方法`GCHandle`會傳回*JNI* *物件參考*, 類似于 s。 JNI 提供三種不同類型的物件參考: 本機參考、全域參考和弱式全域參考。 這三個都是`System.IntPtr`以表示,*但*(根據 JNI 函式類型一節) `IntPtr`並非所有從`JNIEnv`方法傳回的都是參考。 例如, [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) `IntPtr`會傳回, 但不會傳回物件參考`jmethodID`, 它會傳回。 如需詳細資訊, 請參閱[JNI 函數檔](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)。

區域參考是由*大部分*的參考建立方法所建立。
Android 只允許在任何指定時間都有有限數目的本機參考, 通常是512。 本機參考可以透過 JNIEnv 來刪除[。 DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)。
不同于 JNI, 並非所有傳回物件參考的參考 JNIEnv 方法都會傳回本機參考;[JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*)會傳回*全域*參考。 強烈建議您儘快刪除本機參考, 可能的話, 您可以在物件周圍建立[java lang.ini 物件](xref:Java.Lang.Object), 並指定`JniHandleOwnership.TransferLocalRef`至[JniHandleOwnership (IntPtr 控制碼, 傳輸)](xref:Java.Lang.Object#ctor*)函數。

全域參考是由[JNIEnv. NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*)和[JNIEnv](xref:Android.Runtime.JNIEnv.FindClass*)所建立。
您可以使用[JNIEnv DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*)來終結它們。
模擬器的限制為2000個未處理的全域參考, 而硬體裝置的限制為大約52000個全域參考。

弱式全域參考僅適用于 Android 2.2 (Froyo) 和更新版本。 您可以使用[JNIEnv. DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*)刪除弱式全域參考。

### <a name="dealing-with-jni-local-references"></a>處理 JNI 區域參考

[JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*)[JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*)、[JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*)[JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) 和 [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) 會傳回，包含 java 物件的 JNI 區域參考的 `IntPtr`，獲如果 Java 傳回`null`，則為`IntPtr.Zero`。 因為一旦 （512 個項目），最好能確定參考可在未處理的本機參考的有限數目會刪除 neodpověděl včas。 有三種方式可以處理本機參考: 明確刪除它們、建立`Java.Lang.Object`實例來保存它們, 以及使用`Java.Lang.Object.GetObject<T>()`來建立其周圍的受控可呼叫包裝函式。



### <a name="explicitly-deleting-local-references"></a>明確刪除本機參考

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)用來刪除本機參考。 一旦刪除本機參考之後, 就無法再使用它, 因此必須小心, 以確保這`JNIEnv.DeleteLocalRef`是使用本機參考完成的最後一件事。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>以 .JAVA 包裝物件

`Java.Lang.Object`提供可以用來包裝現有 JNI 參考的[JAVA Lang.ini 物件 (IntPtr 控制碼, JniHandleOwnership transfer)](xref:Java.Lang.Object#ctor*) 。 [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership)參數會決定應該如何`IntPtr`處理參數:

-   [JniHandleOwnership。 DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash;建立`Java.Lang.Object`的實例將會從`handle`參數建立新的全域參考, 而且`handle`不會變更。
    呼叫端會負責釋放`handle` (如有需要)。

-   [JniHandleOwnership。 TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash;建立`Java.Lang.Object`的實例將會從`handle`參數建立新的全域參考, 並`handle`使用[JNIEnv](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)加以刪除。 呼叫端不能釋放`handle` , 而且在完成執行`handle`之後不能使用。

-   [JniHandleOwnership。 TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash;建立`Java.Lang.Object`的`handle`實例會接管參數的擁有權。 呼叫端不得釋放`handle` 。


由於 JNI 方法調用方法會傳回本機 refs, `JniHandleOwnership.TransferLocalRef`因此通常會使用:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

在`Java.Lang.Object`實例進行垃圾收集之前, 不會釋放所建立的全域參考。 如果您能夠, 處置實例將會釋出全域參考, 加速垃圾收集:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>使用. lang.ini&lt;T&gt;()

`Java.Lang.Object`提供可用於建立指定類型之 managed 可呼叫包裝函式的 JniHandleOwnership[物件&lt;。 GetObject T&gt;(IntPtr 控制碼, transfer)](xref:Java.Lang.Object.GetObject*)方法。

類型`T`必須滿足下列需求:

1.  `T`必須是參考型別。

1.  `T`必須執行`IJavaObject`介面。

1.  如果`T`不是抽象類別或介面, 則`T`必須提供具有參數類型`(IntPtr,
    JniHandleOwnership)`的函數。

1.  如果`T`是抽象類別或介面, 就*必須*有可供使用的`T`啟動程式。 啟動程式是繼承`T`或實行`T`的非抽象類別型, 而且`T`與啟動程式尾碼的名稱相同。 例如, 如果 T 是介面`Java.Lang.IRunnable` , 則類型`Java.Lang.IRunnableInvoker`必須存在, 而且必須包含必要`(IntPtr,
    JniHandleOwnership)`的函式。


由於 JNI 方法調用方法會傳回本機 refs, `JniHandleOwnership.TransferLocalRef`因此通常會使用:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>查閱 JAVA 類型

若要查閱 JNI 中的欄位或方法, 必須先查閱欄位或方法的宣告類型。 [JNIEnv. FindClass (string)](xref:Android.Runtime.JNIEnv.FindClass*)) 方法是用來查閱 JAVA 類型。 String 參數是 JAVA 類型的*簡化型別參考*或*完整型別參考*。 如需簡化和完整型別參考的詳細資訊, 請參閱[JNI 型別參考一節](#_JNI_Type_References)。

注意:不同于傳回`JNIEnv`物件實例的每個其他`FindClass`方法, 會傳回全域參考, 而不是區域參考。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>實例欄位

欄位是透過*欄位識別碼*來操作。 欄位識別碼是透過 JNIEnv 取得, 它需要欄位定義所在的類別、欄位的名稱, 以及欄位的[JNI 類型](#JNI_Type_Signatures)簽章[。](xref:Android.Runtime.JNIEnv.GetFieldID*)

欄位識別碼不需要釋放, 而且只要載入對應的 JAVA 類型, 就會是有效的。 (Android 目前不支援類別卸載)。

有兩組方法可以操控實例欄位: 一個用於讀取實例欄位, 另一個用於寫入實例欄位。 所有的方法集合都需要欄位識別碼, 才能讀取或寫入域值。

### <a name="reading-instance-field-values"></a>讀取實例域值

用來讀取實例域值的一組方法會遵循命名模式:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

其中`*`是欄位的類型:

-   [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash;讀取不是內建`java.lang.Object`類型的任何實例欄位的值, 例如、陣列和介面類別型。 傳回的值是 JNI 區域參考。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; GetBooleanField`bool`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; GetByteField`sbyte`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; GetCharField`char`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; GetShortField`short`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; GetIntField`int`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; GetLongField`long`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; GetFloatField`float`讀取實例欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; GetDoubleField`double`讀取實例欄位的值。

### <a name="writing-instance-field-values"></a>撰寫實例域值

用來撰寫實例域值的方法集合會遵循命名模式:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

其中*type*是欄位的類型:

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash;寫入不是內建`java.lang.Object`類型的任何欄位值, 例如、陣列和介面類別型。 此`IntPtr`值可以是 JNI 區域參考、JNI 全域參考、JNI 弱式全域參考, 或`IntPtr.Zero` (適用`null`于)。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `bool`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `sbyte`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `char`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `short`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `int`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `long`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `float`寫入實例欄位的值。

-   [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; `double`寫入實例欄位的值。

<a name="_Static_Fields" />

## <a name="static-fields"></a>靜態欄位

靜態欄位是透過*欄位識別碼*來操作。 欄位識別碼是透過 JNIEnv 取得, 它需要欄位定義所在的類別、欄位的名稱, 以及欄位的[JNI 類型](#JNI_Type_Signatures)簽章[。](xref:Android.Runtime.JNIEnv.GetStaticFieldID*)

欄位識別碼不需要釋放, 而且只要載入對應的 JAVA 類型, 就會是有效的。 (Android 目前不支援類別卸載)。

有兩組方法可以操控靜態欄位: 一個用於讀取實例欄位, 另一個用於寫入實例欄位。 所有的方法集合都需要欄位識別碼, 才能讀取或寫入域值。

### <a name="reading-static-field-values"></a>讀取靜態域值

用來讀取靜態域值的一組方法會遵循命名模式:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

其中`*`是欄位的類型:

-   [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash;讀取不是內建`java.lang.Object`類型的任何靜態欄位值, 例如、陣列和介面類別型。 傳回的值是 JNI 區域參考。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; GetStaticBooleanField`bool`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; GetStaticByteField`sbyte`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; GetStaticCharField`char`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; GetStaticShortField`short`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; GetStaticLongField`long`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; GetStaticFloatField`float`讀取靜態欄位的值。

-   [JNIEnv.](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; GetStaticDoubleField`double`讀取靜態欄位的值。

### <a name="writing-static-field-values"></a>寫入靜態域值

用來寫入靜態域值的方法集合會遵循命名模式:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

其中*type*是欄位的類型:

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash;寫入不是內建`java.lang.Object`類型的任何靜態欄位的值, 例如、陣列和介面類別型。 此`IntPtr`值可以是 JNI 區域參考、JNI 全域參考、JNI 弱式全域參考, 或`IntPtr.Zero` (適用`null`于)。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `bool`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `sbyte`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `char`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `short`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `int`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `long`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `float`寫入靜態欄位的值。

-   [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; `double`寫入靜態欄位的值。


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>實例方法

實例方法是透過*方法識別碼*叫用。 方法識別碼是透過[JNIEnv](xref:Android.Runtime.JNIEnv.GetMethodID*)取得, 它需要在中定義方法的類型、方法的名稱, 以及方法的[JNI 類型](#JNI_Type_Signatures)簽章。

方法識別碼不需要釋放, 而且只要載入對應的 JAVA 類型, 就會是有效的。 (Android 目前不支援類別卸載)。

有兩組方法可以叫用方法: 一個用來叫用方法, 另一個用於以非虛擬方式叫用方法。 這兩組方法都需要方法識別碼來叫用方法, 而非虛擬調用也需要您指定應該叫用哪一個類別的實作為。

介面方法只能在宣告類型內進行查閱;無法查閱來自擴充/繼承介面的方法。 如需詳細資訊, 請參閱稍後的系結介面/啟動程式執行一節。

在類別中宣告的任何方法或任何基類或實介面都可以進行查閱。

### <a name="virtual-method-invocation"></a>虛擬方法調用

用來叫用方法的一組方法, 幾乎會遵循命名模式:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

其中`*` , 是方法的傳回型別。

-   [JNIEnv。 CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash;會叫用傳回非內建`java.lang.Object`類型的方法, 例如、陣列和介面。 傳回的值是 JNI 區域參考。

-   [JNIEnv。 CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash;會叫用傳回`bool`值的方法。

-   [JNIEnv。 CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash;會叫用傳回`sbyte`值的方法。

-   [JNIEnv。 CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash;會叫用傳回`char`值的方法。

-   [JNIEnv。 CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash;會叫用傳回`short`值的方法。

-   [JNIEnv。 CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash;會叫用傳回`long`值的方法。

-   [JNIEnv。 CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash;會叫用傳回`float`值的方法。

-   [JNIEnv。 CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash;會叫用傳回`double`值的方法。

### <a name="non-virtual-method-invocation"></a>非虛擬方法調用

用來叫用方法的一組方法, 不是以虛擬方式遵循命名模式:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*` , 是方法的傳回型別。 非虛擬方法調用通常是用來叫用虛擬方法的基底方法。

-   [JNIEnv。 CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash;非虛擬叫用傳回非內建`java.lang.Object`類型的方法, 例如、陣列和介面。 傳回的值是 JNI 區域參考。

-   [JNIEnv。 CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash;非虛擬叫用傳回`bool`值的方法。

-   [JNIEnv。 CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash;非虛擬叫用傳回`sbyte`值的方法。

-   [JNIEnv。 CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash;非虛擬叫用傳回`char`值的方法。

-   [JNIEnv。 CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash;非虛擬叫用傳回`short`值的方法。

-   [JNIEnv。 CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash;非虛擬叫用傳回`long`值的方法。

-   [JNIEnv。 CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash;非虛擬叫用傳回`float`值的方法。

-   [JNIEnv。 CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash;非虛擬叫用傳回`double`值的方法。

<a name="_Static_Methods" />

## <a name="static-methods"></a>靜態方法

靜態方法是透過*方法識別碼*叫用。 方法識別碼是透過[JNIEnv](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)取得, 它需要在中定義方法的類型、方法的名稱, 以及方法的[JNI 類型](#JNI_Type_Signatures)簽章。

方法識別碼不需要釋放, 而且只要載入對應的 JAVA 類型, 就會是有效的。 (Android 目前不支援類別卸載)。

### <a name="static-method-invocation"></a>靜態方法調用

用來叫用方法的一組方法, 幾乎會遵循命名模式:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*` , 是方法的傳回型別。

-   [JNIEnv。 CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash;會叫用傳回非內建`java.lang.Object`類型的靜態方法, 例如、陣列和介面。 傳回的值是 JNI 區域參考。

-   [JNIEnv。 CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash;會叫用傳回`bool`值的靜態方法。

-   [JNIEnv。 CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash;會叫用傳回`sbyte`值的靜態方法。

-   [JNIEnv。 CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash;會叫用傳回`char`值的靜態方法。

-   [JNIEnv。 CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash;會叫用傳回`short`值的靜態方法。

-   [JNIEnv。 CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash;會叫用傳回`long`值的靜態方法。

-   [JNIEnv。 CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash;會叫用傳回`float`值的靜態方法。

-   [JNIEnv。 CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash;會叫用傳回`double`值的靜態方法。

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 類型簽章

[JNI 類型](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)簽章是[JNI 類型參考](#_JNI_Type_References)(雖然不是簡化的類型參考), 但方法除外。 使用方法, JNI 類型簽章是一個左括弧`'('`, 後面接著串連在一起的所有參數類型的類型參考 (沒有分隔逗號或任何其他專案), 後面接著右括弧, `')'`後面接著方法傳回類型的 JNI 類型參考。

例如, 假設有 JAVA 方法:

```java
long f(int n, String s, int[] array);
```

JNI 類型簽章會是:

```csharp
(ILjava/lang/String;[I)J
```

一般來說,*強烈*建議使用`javap`命令來判斷 JNI 簽章。 例如, [valueOf (String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法的 JNI 類型簽章是 "(Ljava/Lang/String;) Ljava/Lang/Thread $ State;", 而的 JNI 類型簽章則是 "() [Ljava/。[值](https://developer.android.com/reference/java/lang/Thread.State.html#values)] 方法。lang/Thread $ State; "。 請留意尾端的分號;這些*是*JNI 類型簽章的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 類型參考

JNI 類型參考與 JAVA 類型參考不同。 您不能使用完整的 JAVA 類型名稱 ( `java.lang.String`例如 with JNI), 您必須改為使用`"java/lang/String"` JNI `"Ljava/lang/String;"`變化或 (視內容而定), 請參閱下面的詳細資料。
JNI 類型參考有四種類型:

- **built-in**
- **simplified**
- **type**
- **array**

### <a name="built-in-type-references"></a>內建型別參考

內建類型參考是單一字元, 用來參考內建實數值型別。 對應如下所示:

- `"B"`適用`sbyte`于。
- `"S"`適用`short`于。
- `"I"`適用`int`于。
- `"J"`適用`long`于。
- `"F"`適用`float`于。
- `"D"`適用`double`于。
- `"C"`適用`char`于。
- `"Z"`適用`bool`于。
- `"V"`針對`void`方法傳回類型。

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>簡化型別參考

簡化型別參考只能用在[JNIEnv. FindClass (string)](xref:Android.Runtime.JNIEnv.FindClass*)) 中。
有兩種方式可以衍生簡化的型別參考:

1.  從完整的 JAVA 名稱, 以取代封裝名稱`'.'`中的每個, 並在類型名稱之前`'/'`加上, `'.'`並在類型名稱中`'$'`加上。

1.  讀取的輸出`'unzip -l android.jar | grep JavaName'` 。

這兩種方法的其中一種會產生 java 類型的 java [。狀態](https://developer.android.com/reference/java/lang/Thread.State.html)會對應到簡化型別參考`java/lang/Thread$State`。

### <a name="type-references"></a>型別參考

型別參考是內建型別參考, 或具有`'L'`前置詞`';'`和後置詞的簡化型別參考。 若為 java 類型[java.lang.String](https://developer.android.com/reference/java/lang/String.html), 則簡化型別參考是`"java/lang/String"`, 而型別參考是`"Ljava/lang/String;"`。

型別參考用於陣列型別參考和 JNI 簽章。

取得類型參考的另一種方式是藉由讀取的輸出`'javap -s -classpath android.jar fully.qualified.Java.Name'`。
視所涉及的類型而定, 您可以使用「函式宣告」或「方法傳回類型」來決定 JNI 名稱。 例如：

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

`Thread.State`是 JAVA 列舉型別, 所以我們可以使用`valueOf`方法的簽章來判斷型別參考是 Ljava/lang/Thread $ State;。

### <a name="array-type-references"></a>陣列型別參考

陣列型別參考`'['`的前面會加上 JNI 型別參考。
指定陣列時, 不能使用簡化的類型參考。

例如`int[]` , `"[I"`是,是`"[[I"`,而`java.lang.Object[]`是。`"[Ljava/lang/Object;"` `int[][]`

## <a name="java-generics-and-type-erasure"></a>JAVA 泛型和類型抹除

在*大部分*的情況下, 透過 JNI 所見, JAVA 泛型*並不存在*。
有些「wrinkles」, 但這些 wrinkles 是 JAVA 與泛型互動的方式, 而不是 JNI 查詢和叫用泛型成員的方式。

透過 JNI 進行互動時, 泛型型別或成員與非泛型型別或成員之間沒有任何差異。 例如, 泛型型別 ( [Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) ) 也是「原始」泛型型別`java.lang.Class`, `"java/lang/Class"`兩者都有相同的簡化型別參考。

## <a name="java-native-interface-support"></a>JAVA 原生介面支援

[JNIEnv](xref:Android.Runtime.JNIEnv)是 Jave Native INTERFACE (JNI) 的受控包裝函式。 JNI 函式會在[JAVA 原生介面規格](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) `JNIEnv*`中宣告, 不過方法已經變更為移除明確的參數, 並`jclass` `IntPtr`使用取代`jobject`、、 `jmethodID`、等.例如, 請考慮[JNI NewObject 函數](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

這會公開為[JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

兩個呼叫之間的轉譯相當簡單。 在 C 中, 您可以:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

對C#等的會是:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

在 IntPtr 中保存 JAVA 物件實例之後, 您可能會想要對它執行一些動作。 您可以使用 JNIEnv 方法 (例如[JNIEnv. CallVoidMethod ())](xref:Android.Runtime.JNIEnv.CallVoidMethod*)來執行此動作, 但如果已經有類比C#的包裝函式, 則您會想要透過 JNI 參考來建立包裝函數。 您可以透過[&lt;JAVACast T >](xref:Android.Runtime.Extensions.JavaCast*)擴充方法來執行此動作:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

您也可以使用. [lang.ini&lt;T >](xref:Java.Lang.Object.GetObject*)方法:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外, 所有的 JNI 函數都已藉由移除每個`JNIEnv*` JNI 函式中的參數來修改。

## <a name="summary"></a>總結

直接處理 JNI 是一種非常可怕的體驗, 應盡可能避免所有成本。 可惜的是, 它不一定是肇因;希望當您使用 Mono for Android 叫用未系結的 JAVA 案例時, 本指南會提供一些協助。

## <a name="related-links"></a>相關連結

- [JAVA 原生介面規格](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [JAVA 原生介面函式](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
