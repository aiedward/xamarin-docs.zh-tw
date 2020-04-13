---
title: 與 JNI 和 Xamarin.安卓合作
description: Xamarin.Android 允許使用 C# 而不是 Java 編寫 Android 應用程式。 Xamarin.Android 提供了多個程式集,為 Java 庫提供綁定,包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll。 但是,綁定並非針對每個可能的 Java 庫提供,並且提供的綁定可能不會綁定每個 JAVA 類型和成員。 要使用未綁定的 Java 類型和成員,可以使用 JAVA 本機介面 (JNI)。 本文演示如何使用 JNI 與 Xamarin.Android 應用程式中的 JAVA 類型和成員進行交互。
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291881"
---
# <a name="working-with-jni-and-xamarinandroid"></a>與 JNI 和 Xamarin.安卓合作

_Xamarin.Android 允許使用 C# 而不是 Java 編寫 Android 應用程式。Xamarin.Android 提供了多個程式集,為 Java 庫提供綁定,包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll。但是,綁定並非針對每個可能的 Java 庫提供,並且提供的綁定可能不會綁定每個 JAVA 類型和成員。要使用未綁定的 Java 類型和成員,可以使用 JAVA 本機介面 (JNI)。本文演示如何使用 JNI 與 Xamarin.Android 應用程式中的 JAVA 類型和成員進行交互。_

## <a name="overview"></a>概觀

創建託管可調用包裝器 (MCW) 來調用 Java 代碼並不總是必要或可能的。 在許多情況下,「內聯」JNI 是完全可以接受的,並且對於一次性使用未綁定的 JAVA 成員非常有用。 在 Java 類上使用 JNI 調用單個方法通常比生成整個 .jar 綁定更簡單。

Xamarin.Android`Mono.Android.dll`提供 程式集,它為`android.jar`Android 的 庫提供綁定。 手動繫結型類型和內部`Mono.Android.dll`不存在的類型和類型以及`android.jar`不存在的類型。 要綁定 Java 類型和成員,請使用**Java 本機介面****(JNI**) 來查找類型、讀取和寫入欄位以及調用方法。

Xamarin.Android 中的 JNI API`System.Reflection`在概念上 與 .NET 中的 API 非常相似:它使您能夠按名稱查找類型和成員、讀取和寫入欄位值、調用方法等。 您可以使用 JNI`Android.Runtime.RegisterAttribute`和自訂屬性聲明可以綁定支援重寫的虛擬方法。 您可以綁定介面,以便可以在 C# 中實現它們。

此文件解釋:

- JNI 如何引用類型。
- 如何查找、讀取和寫入欄位。
- 如何查找和調用方法。
- 如何公開虛擬方法以允許從託管代碼重寫。
- 如何公開介面。

## <a name="requirements"></a>需求

JNI,通過[Android.Runtime.JNIEnv命名空間](xref:Android.Runtime.JNIEnv)公開,在每一個版本的Xamarin.安卓中都有。
要綁定 Java 類型和介面,必須使用 Xamarin.Android 4.0 或更高版本。

## <a name="managed-callable-wrappers"></a>託管可呼叫包裝器

**託管可呼叫包裝器****(MCW**) 是 Java 類或介面的*綁定,* 它結束了所有 JNI 機制,以便用戶端 C# 程式碼無需擔心 JNI 的基本複雜性。 大部分`Mono.Android.dll`由託管可調用包裝器組成。

託管可呼叫包裝器有兩個用途:

1. 封裝 JNI 使用,以便用戶端代碼不需要瞭解底層複雜性。
1. 使子類 Java 類型和實現 JAva 介面成為可能。

第一個目的純粹是為了方便和封裝複雜性,以便消費者有一套簡單的、託管的類可供使用。 這需要使用本文後面所述的各種[JNIEnv](xref:Android.Runtime.JNIEnv)成員。 請記住,託管可調用包裝不是絕對必要的&ndash;「內聯」JNI 使用是完全可以接受的,並且對於一次性使用未綁定的 Java 成員非常有用。 子類和介面實現需要使用託管可調用包裝器。

## <a name="android-callable-wrappers"></a>Android 可呼叫包裝函式

每當 Android 執行時 (ART) 需要呼叫託管代碼時,都需要 Android 可呼叫包裝器 (ACW);這些包裝器是必需的,因為無法在運行時向 ART 註冊類。
(具體來說,Android 運行時不支援[DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI 函數。 因此,Android 可調用包裝器可以彌補運行時類型註冊支援的不足。

每當 Android 代碼需要執行在託管代碼中重寫或實現的虛擬或介面方法時,Xamarin.Android 必須提供 Java 代理,以便此方法被發送到相應的託管類型。 這些 Java 代理類型是 JAVA 代碼,它們具有與託管類型相同的「相同」基類和 JAVA 介面清單,實現相同的構造函數並聲明任何重寫的基類和介面方法。

Android 可呼叫包裝器由**單體.exe**程式[在生成過程中](~/android/deploy-test/building-apps/build-process.md)生成,並為繼承[Java.Lang.Object .](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>實作介面

有時您可能需要實現 Android 介面(如[Android.Content.I 元件回撥](xref:Android.Content.IComponentCallbacks))。

所有Android類和介面都擴展了[Android.Runtime.IJAvaObject](xref:Android.Runtime.IJavaObject)介面;因此,所有 Android`IJavaObject`類型 都必須實現 。
Xamarin.Android 利用了這一&ndash;事實`IJavaObject`,它 用於為給定的託管類型為 Android 提供 Java 代理(Android 可調用包裝器)。 由於**monodroid.exe**僅`Java.Lang.Object`查找子類(`IJavaObject`必須實現 ),`Java.Lang.Object`因此子類 為我們提供了在託管代碼中實現介面的方法。 例如：

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

*本文的其餘部分提供了可能更改的實現詳細資訊*(此處僅介紹,因為開發人員可能對引擎蓋下發生的事情感到好奇)。

例如,給定以下 C# 源:

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

**mandroid.exe**程式將生成以下 Android 可呼叫包裝器:

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

請注意,保留基類,併為託管代碼中重寫的每個方法提供本機方法聲明。

### <a name="exportattribute-and-exportfieldattribute"></a>匯出屬性與匯出欄位屬性

通常,Xamarin.Android 會自動生成包含 ACW 的 Java 代碼;當類派生自 JAVA 類並重寫現有的 Java 方法時,此生成基於類和方法名稱。 但是,在某些情況下,代碼生成不夠充分,如下所述:

- Android 支援佈局 XML 屬性中的操作名稱,例如[android:onClick](xref:Android.Views.View.IOnClickListener.OnClick*) XML 屬性。 指定時,膨脹的 View 實例將嘗試查找 JAva 方法。

- [java.io.可序列化](https://developer.android.com/reference/java/io/Serializable.html)介面`readObject`需要與`writeObject`方法 。 由於它們不是此介面的成員,因此我們相應的託管實現不會將這些方法公開給 JAva 代碼。

- [android.os.Parcel 可分發介面](xref:Android.OS.Parcelable)期望實現類必須具有`CREATOR``Parcelable.Creator`類型的 靜態欄位。 生成的 Java 代碼需要一些顯式欄位。 使用我們的標準方案,無法從託管代碼輸出 Java 代碼中的欄位。

由於代碼生成不提供使用任意名稱生成任意 Java 方法的解決方案,從 Xamarin.Android 4.2 開始,因此引入了[ExportAttribute](xref:Java.Interop.ExportAttribute)和[ExportFieldattribute,](xref:Java.Interop.ExportFieldAttribute)以便為上述方案提供解決方案。 這兩個屬性都`Java.Interop`駐留在命名空間中:

- `ExportAttribute`&ndash;指定方法名稱及其預期的異常類型(在 JAVA 中提供顯式"引發")。 當它用於方法時,該方法將「匯出」Java 方法,該方法將調度代碼生成到相應的 JNI 調用到託管方法。 這可以用於`android:onClick`與`java.io.Serializable`。

- `ExportFieldAttribute`&ndash;指定欄位名稱。 它駐留在用作欄位初始化程式的方法上。 這可以用於`android.os.Parcelable`。

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>容錯排除匯出屬性及匯出欄位屬性

- 由於缺少**Mono.Android.export.dll(**&ndash;`ExportAttribute`如果您使用 或`ExportFieldAttribute`在代碼或 從屬庫中的某些方法上)的打包失敗,您必須添加**Mono.Android.export.dll**。 此程式集隔離以支援來自 Java 的回調代碼。 它與**Mono.Android.dll**是分開的,因為它為應用程式添加了額外的大小。

- 在發佈生成中`MissingMethodException`,對於發佈&ndash;生成 中的匯出`MissingMethodException`方法, 發生匯出方法。 (此問題已修復在最新版本的 Xamarin.安卓中。

### <a name="exportparameterattribute"></a>匯出參數屬性

`ExportAttribute`並提供`ExportFieldAttribute`Java 運行時代碼可以使用的功能。 此執行時代碼通過由這些屬性驅動的生成的 JNI 方法訪問託管代碼。 因此,託管方法綁定的現有 JAva 方法;因此,沒有託管方法綁定的 Java 方法。因此,JAVA 方法是從託管方法簽名生成的。

然而,這種情況並不完全決定。 最值得注意的是,在託管類型和 JAVA 類型之間的某些高級映射中也是如此,例如:

- InputStream
- 輸出流
- XmlPullParser
- XmlResourceParser

當匯出方法需要此類類型時,`ExportParameterAttribute`必須使用 顯式為相應的參數或返回值類型。

### <a name="annotation-attribute"></a>註解屬性

在 Xamarin.Android 4.2`IAnnotation`中,我們將實現類型轉換為屬性(System.Attribute),並在 JAVA 包裝器中添加了對註釋生成的支援。

這意味著以下方向變更:

- 綁定產生器從`Java.Lang.DeprecatedAttribute``java.Lang.Deprecated`( 雖然`[Obsolete]`它應位於託管代碼中) 生成。

- 這並不意味著`Java.Lang.Deprecated`現有 類將消失。 這些基於 JAVA 的物件仍可像往常一樣使用 JAVA 物件(如果存在此類用法)。 會有`Deprecated`和`DeprecatedAttribute`類。

- 類別`Java.Lang.DeprecatedAttribute`標記為`[Annotation]`。 當從此屬性`[Annotation]`繼承的自定義屬性時,msbuild 任務將在 Android 可呼叫包裝器@Deprecated(ACW) 中為該自定義屬性 ( ) 生成 JAVA 註釋。

- 註釋可以生成到類、方法和導出的欄位(這是託管代碼中的方法)。

如果未註冊包含類(註釋的類本身或包含批註成員的類),則根本不生成整個 JAVA 類源,包括註釋。 對於方法,可以指定`ExportAttribute`獲取顯式生成和給給說明的方法。 此外,它不是"生成"Java註釋類定義的功能。 換句話說,如果為特定註釋定義自定義託管屬性,則必須添加另一個包含相應 JAVA 註釋類的 .jar 庫。 添加定義註釋類型的 Java 源檔是不夠的。 Java編譯器的工作方式與**apt**不同。

此外,以下限制還適用:

- 到目前為止,此轉換過程不考慮`@Target`註釋類型的註釋。

- 屬性上的屬性不起作用。 將屬性用於屬性獲取者或設置器。

## <a name="class-binding"></a>類綁定

綁定類意味著編寫託管可調用包裝器以簡化基礎 JAVA 類型的調用。

綁定虛擬和抽象方法以允許從 C# 重寫需要 Xamarin.Android 4.0。 但是,任何版本的 Xamarin.Android 都可以綁定非虛擬方法、靜態方法或虛擬方法,而無需支援重寫。

繫結通常包含以下項目:

- [繫結 Java 型態的 JNI 句柄](#_Looking_up_Java_Types)。

- [每個結合欄位的 JNI 欄位指示與屬性](#_Instance_Fields)。

- [JNI 方法的識別碼與每個綁定方法的方法](#_Instance_Methods)。

- 如果需要子類,則類型需要在具有 RegisterAttribute 的類型聲明中具有[註冊屬性](xref:Android.Runtime.RegisterAttribute)自定義[屬性。](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) `true`

### <a name="declaring-type-handle"></a>宣告型態

欄位和方法查找方法需要引用其聲明類型的物件引用。 按照慣例,這在一個`class_ref`欄位中舉行:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

有關權杖的詳細資訊,`CLASS`請參閱[JNI 類型引用](#_JNI_Type_References)部分。

### <a name="binding-fields"></a>繫結欄位

Java 欄位作為 C# 屬性公開,例如 java 欄位[java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in)綁定為 C# 屬性[Java.Lang.JavaSystem.In。](xref:Java.Lang.JavaSystem.In)
此外,由於 JNI 區分了靜態欄位和實例欄位,因此在實現屬性時使用不同的方法。

欄位結合的群組方法:

1. *獲取欄位 id*方法。 *get 欄位 ID*方法負責傳回*取得欄位值*和*設定欄位值*方法將使用的欄位句柄。 取得欄位代碼需要知道宣告型態、欄位的名稱和欄位的[JNI 類型簽署](#JNI_Type_Signatures)。

1. *獲取欄位值*方法。 這些方法需要欄位句柄,並負責從 JAVA 讀取欄位的值。
    要使用的方法取決於欄位的類型。

1. *設置欄位值*方法。 這些方法需要欄位句柄,並負責在 Java 中寫入欄位的值。 要使用的方法取決於欄位的類型。

[靜態欄位](#_Static_Fields)使用[JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) `JNIEnv.GetStatic*Field`,和[JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)方法。

 [實例欄位](#_Instance_Fields)使用[JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*) `JNIEnv.Get*Field`,和[JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*)方法。

例如,靜態屬性`JavaSystem.In`可以實現為:

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

注意:我們使用[InputStreamInvoker.FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*)將 JNI`System.IO.Stream`引用轉換為`JniHandleOwnership.TransferLocalRef`實例,我們使用的是因為[JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*)返回本地引用。

許多[Android.運行時](xref:Android.Runtime)`FromJniHandle`類型都有 將 JNI 引用轉換為所需類型的方法。

### <a name="method-binding"></a>方法繫結

Java 方法作為 C# 方法和 C# 屬性公開。 例如,JAVA 方法[java.lang.Runtime.runtimeonExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean))方法綁定為[JAva.lang.Runtime.Runtime.RunfinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*)方法[,java.lang.object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass)方法綁定為[JAva.lang.object.class](xref:Java.Lang.Object.Class)屬性。

方法呼叫是一個兩步過程:

1. 要呼叫的方法的*get 方法 ID。* *get 方法 id*方法負責返回方法調用方法將使用的方法句柄。 取得方法 ID 需要知道宣告類型、方法的名稱和方法的[JNI 類型簽署](#JNI_Type_Signatures)。

1. 叫用方法。

與欄位一樣,用於獲取方法 id 和調用方法的方法在靜態方法和實例方法之間有所不同。

[靜態方法](#_Static_Methods_1)使用[JNIEnv.GetStaticMethodID()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)查找方法 ID,並`JNIEnv.CallStatic*Method`使用調用方法 系列。

[實例方法](#_Instance_Methods)使用[JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*)查找方法 ID,`JNIEnv.Call*Method`並`JNIEnv.CallNonvirtual*Method`使用 調用 方法 的 和 族。

方法綁定可能不僅僅是方法調用。 方法綁定還包括允許重寫方法(對於抽象和非最終方法)或實現(對於介面方法)。 [支持繼承,介面](#_Supporting_Inheritance,_Interfaces_1)部分介紹支援虛擬方法和介面方法的複雜性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>靜態方法

綁定靜態方法涉及使用`JNIEnv.GetStaticMethodID`獲取方法句柄,然後使用適當`JNIEnv.CallStatic*Method`的 方法,具體取決於方法的返回類型。 以下是[Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法的綁定範例:

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

請注意,我們將方法句柄儲存在靜態欄位中`id_getRuntime`。 這是性能優化,因此不需要在每次調用時都抬頭查看方法句柄。 不必以這種方式緩存方法句柄。 獲取方法句柄后,使用[JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)調用該方法。 `JNIEnv.CallStaticObjectMethod`傳`IntPtr`回的 Java 實體的句柄的 。
[Java.Lang.object.getObject&lt;&gt;T(IntPtr,JniHandle擁有權)](xref:Java.Lang.Object.GetObject*)用於將JAVA句柄轉換為強類型的物件實例。

#### <a name="non-virtual-instance-method-binding"></a>非虛擬實體方法繫結

綁定`final`實例方法或不需要重寫的實例方法涉及`JNIEnv.GetMethodID`使用 獲取方法句柄,然後使用`JNIEnv.Call*Method`適當的 方法,具體取決於該方法的返回類型。 下面是`Object.Class`屬性繫結的範例:

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

請注意,我們將方法句柄儲存在靜態欄位中`id_getClass`。
這是性能優化,因此不需要在每次調用時都抬頭查看方法句柄。 不必以這種方式緩存方法句柄。 獲取方法句柄后,使用[JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)調用該方法。 `JNIEnv.CallStaticObjectMethod`傳`IntPtr`回的 Java 實體的句柄的 。
[Java.Lang.object.getObject&lt;&gt;T(IntPtr,JniHandle擁有權)](xref:Java.Lang.Object.GetObject*)用於將JAVA句柄轉換為強類型的物件實例。

### <a name="binding-constructors"></a>繫結建函數

建構函數是具有名稱`"<init>"`的 JAVA 方法。 與 JAVA 實例`JNIEnv.GetMethodID`方法一 樣,用於查找構造函數句柄。 與 JAVA 方法不同[,JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法用於調用構造函數方法句柄。 傳`JNIEnv.NewObject`回值是 JNI 本地端引用:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

通常,類綁定將子類[Java.Lang.object .object](xref:Java.Lang.Object).
下類時`Java.Lang.Object`,將發揮另一種語義:實例`Java.Lang.Object`通過 屬性維護對 JAVA`Java.Lang.Object.Handle`實例的 全域引用。

1. 默認`Java.Lang.Object`建構函數將分配 JAVA 實例。

1. 如果`RegisterAttribute`類型具有`RegisterAttribute.DoNotGenerateAcw``true`, 和`RegisterAttribute.Name`是 , 則透過該預設建構函數建立類型的實體。

1. 否則,對應`this.GetType`的[Android 可調用包裝器](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 將透過其預設構造函數實例化。 Android 可調用包裝器在創建過程中生成,用於`Java.Lang.Object`創建 未`true`設置`RegisterAttribute.DoNotGenerateAcw`為 的 每個子類。

對於不是類綁定的類型,這是預期的語義:實例化`Mono.Samples.HelloWorld.HelloAndroid`C# 實例應構造一個`mono.samples.helloworld.HelloAndroid`JAVA 實例,該實例是生成的 Android 可調用包裝器。

對於類綁定,如果 JAVA 類型包含預設構造函數和/或不需要調用其他構造函數,這可能是正確的行為。 否則,必須提供執行以下操作的建構函數:

1. 呼叫[Java.Lang.object(IntPtr,JniHandle 擁有權),](xref:Java.Lang.Object#ctor*)而不是預設`Java.Lang.Object`建構函數。 這是避免創建新的 JAVA 實例所必需的。

1. 在創建任何 JAVA 實例之前,請檢查[Java.Lang.object.handle](xref:Java.Lang.Object.Handle)的值。 該`Object.Handle`屬性將具有一個值`IntPtr.Zero`, 而不是在 Java 代碼中建構了 Android 可呼叫包裝器,並且正在建構類綁定以包含創建的 Android 可調用包裝器實例。 例如,當 Android`mono.samples.helloworld.HelloAndroid`創建 實例時,將首先創建 Android 可調用`HelloAndroid`包裝器,JAVA 構`Mono.Samples.HelloWorld.HelloAndroid`造函數將創建相應 類型的實例,在`Object.Handle`建構函數執行之前將 屬性設置為 JAVA 實例。

1. 如果當前運行時類型與聲明類型不同,則必須創建相應的 Android 可調用包裝器的實例,並使用[Object.SetHandle](xref:Java.Lang.Object.SetHandle*)來儲存[JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*)傳回的句柄。

1. 如果當前運行時類型與聲明類型相同,則調用 JAVA 構造函數並使用[Object.SetHandle](xref:Java.Lang.Object.SetHandle*)`JNIEnv.NewInstance`來存儲 返回的句柄。

例如,請考慮[java.lang.integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int))建構函數。 這繫結為:

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

[JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*)方法是幫助`JNIEnv.FindClass`執行`JNIEnv.GetMethodID``JNIEnv.NewObject``JNIEnv.DeleteGlobalReference`、`JNIEnv.FindClass`和 從返回的值。 如需詳細資料，請參閱下一節。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支援繼承、介面

對 JAVA 類型進行子類化或實現 Java 介面需要生成[Android 可調用包裝器](~/android/platform/java-integration/android-callable-wrappers.md)`Java.Lang.Object`(ACL),這些容器 在打包過程中為每個子類生成。 ACW 生成通過[Android.Runtime.註冊屬性](xref:Android.Runtime.RegisterAttribute)自定義屬性進行控制。

對 C#`[Register]`型態 ,自訂屬性建構函數需要一個參數:相應 JAva 類型的[JNI 簡化類型參考](#_Simplified_Type_References_1)。 這允許在 Java 和 C# 之間提供不同的名稱。

在 Xamarin.Android 4.0`[Register]`之前,自定義屬性無法"別名"現有 JAVA 類型。 這是因為 ACW 生成過程將為每個`Java.Lang.Object`遇到的子類生成 AW。

Xamarin.Android 4.0 引入了[註冊屬性.NotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)屬性。 此屬性指示 ACW 生成過程*跳過*帶批的類型,允許聲明新的託管可調用包裝,這將導致在包創建時生成 ACL。 這允許綁定現有 JAVA 類型。 例如,請考慮以下包含一個方法的簡單`Adder`JAVA`add`類 ,該類將添加到整數並返回結果:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

型`Adder`態可以結合:

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

此處,C#`Adder`類型*別名*Java`Adder`類型。 該`[Register]`屬性用於`mono.android.test.Adder`指定 Java 類型的 JNI 名稱,該`DoNotGenerateAcw`屬性用於禁止 ACW 生成。 這將導致為`ManagedAdder`類型生成 ACW,該類型將正確`mono.android.test.Adder`對 類型進行子類。 如果未使用`RegisterAttribute.DoNotGenerateAcw`該屬性,則 Xamarin.Android 生成過程`mono.android.test.Adder`將生成新的 Java 類型。 這將導致編譯錯誤,因為`mono.android.test.Adder`類型將在兩個單獨的檔中存在兩次。

### <a name="binding-virtual-methods"></a>繫結虛擬方法

`ManagedAdder`類 JAva`Adder`類型,但它不是特別有趣:C#`Adder`類型不定義任何虛擬`ManagedAdder`方法,因此 不能覆蓋任何內容。

允許`virtual`子類重寫的綁定方法需要完成以下兩類的幾項操作:

1. **方法繫結**

1. **方法註冊**

#### <a name="method-binding"></a>方法繫結

方法繫結要求在 C#`Adder`定義中新增兩個支援`ThresholdType`成員`ThresholdClass`: 與 。

##### <a name="thresholdtype"></a>ThresholdType

屬性`ThresholdType`傳回結合目前的類型:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`在方法綁定中使用,以確定何時應執行虛擬方法調度和非虛擬方法調度。 它應始終返回對應於`System.Type`聲明 C# 類型的實例。

##### <a name="thresholdclass"></a>閾值類

屬性`ThresholdClass`傳回結合型態的 JNI 類別參考:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`在調用非虛擬方法時在方法綁定中使用。

#### <a name="binding-implementation"></a>繫結實現

方法綁定實現負責 JAVA 方法的運行時調用。 它還包含作為方法註冊的`[Register]`一部分的自定義屬性聲明,將在方法註冊部分中討論:

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

該`id_add`欄位包含要呼叫的 Java 方法的方法 ID。 `id_add``class_ref``"add"``"(II)I"`該值從 獲得,這需要聲明類 ()、JAVA 方法名稱 () 和方法的 JNI 簽名 ()。 `JNIEnv.GetMethodID`

取得方法 ID`GetType`後,`ThresholdType`將與確定 是否需要虛擬或非虛擬派單進行比較。 當匹配`GetType``ThresholdType`時需要虛擬調度,`Handle`這 是指重寫方法的 Java 分配的子類。

當`GetType`不`ThresholdType`匹配`Adder`時, 已`ManagedAdder`分類(例如 由),並且只有在`Adder.Add``base.Add`子類調用 時才會調用實現。 這是非虛擬調度案例,這是進入的位置`ThresholdClass`。 `ThresholdClass`指定哪個 Java 類將提供要調用的方法的實現。

#### <a name="method-registration"></a>方法註冊

假設我們有一個更新`ManagedAdder`的定義,它重寫`Adder.Add`了 該方法:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

具有`[Register]`自`Adder.Add`訂 屬性的回想:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]`自訂屬性建構函數接受三個值:

1. Java 方法的名稱`"add"`, 在這種情況下。

1. 該方法的 JNI 類型`"(II)I"`簽名, 在本例中。

1. 連接器*connector method*方法`GetAddHandler`, 在這種情況下。
    稍後將討論連接器方法。

前兩個參數允許 ACW 生成過程生成方法聲明以重寫該方法。 產生的 ACW 將包含以下一些代碼:

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

請注意,聲明`@Override`一個方法,該方法委託給同`n_`名 的-預固定方法。 這可確保在 Java 代碼`ManagedAdder.add``ManagedAdder.n_add`調用 時呼叫 ,這將允許執行重`ManagedAdder.Add`寫的 C# 方法。

因此,最重要的問題:如何`ManagedAdder.n_add`挂鉤`ManagedAdder.Add`?

Java`native`方法通過[JNI註冊本機函數](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)在JAVA(Android運行時)運行時註冊。
`RegisterNatives`採用包含 Java 方法名稱、JNI 類型簽名和函式指標的結構陣列,以呼叫[JNI 呼叫約定](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)。
函數指標必須是一個函數,它採用兩個指標參數,後跟方法參數。 Java`ManagedAdder.n_add`方法必須通過具有以下 C 原型的函數實現:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.安卓不公開一`RegisterNatives`種方法。 相反,ACW 和 MCW 共同`RegisterNatives`提供了調用 所需的資訊:ACW 包含方法名稱和 JNI 類型簽名,唯一缺少的就是連接的函數指標。

這是*連接器方法*的用處。 第三`[Register]`個自定義屬性參數是在已註冊類型中定義的方法的名稱或未接受任何參數並返回`System.Delegate`的已註冊類型的基類。 返回`System.Delegate`的方法反過來是指具有正確 JNI 函數簽名的方法。 最後,連接器方法返回的委託*必須*根植,以便 GC 不會收集它,因為代理正在提供給 JAva。

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

該方法`GetAddHandler`創建一`Func<IntPtr, IntPtr, int, int,
int>`個 委託,該`n_Add`委託引用 該方法,然後調用[JNINativeWrapper。](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*)
`JNINativeWrapper.CreateDelegate`將所提供的方法包裝在 try/catch 塊中,以便處理任何未處理的異常,並導致引發[AndroidEvent.](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) 生成的委託存儲在靜態`cb_add`變數中,以便 GC 不會釋放委託。

最後,`n_Add`該方法負責將 JNI 參數封送到相應的託管類型,然後委派方法調用。

注意:在通過`JniHandleOwnership.DoNotTransfer`JAVA 實例獲取 MCW 時,始終使用。 將它們視為本地引用(因此呼叫`JNIEnv.DeleteLocalRef`)將中斷託管 -&gt; Java&gt;託管堆疊轉換。

### <a name="complete-adder-binding"></a>完整的加劑綁定

`mono.android.tests.Adder`類型的完整託管綁定是:

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

編寫符合以下條件的類型時:

1. 子`Java.Lang.Object`

1. 具有`[Register]`自訂屬性

1. `RegisterAttribute.DoNotGenerateAcw` 是 `true`

然後,對於 GC 交互,類型*不能*具有在運行時`Java.Lang.Object`可能`Java.Lang.Object`引用或子類的任何欄位。 例如,不允許類型`System.Object`欄位和任何介面類型。 允許參考`Java.Lang.Object`實體的類型,例如`System.String`與`List<int>`。 此限制是為了防止 GC 過早收集物件。

如果類型必須包含可以參考實體的`Java.Lang.Object`實體欄位,則欄位類型必須為`System.WeakReference`或`GCHandle`。

## <a name="binding-abstract-methods"></a>繫結抽象方法

綁定`abstract`方法與綁定虛擬方法基本相同。 只有兩個區別:

1. 抽象方法是抽象的。 它仍然保留屬性`[Register]`和關聯的方法註冊,方法綁定只是移動`Invoker`到 類型。

1. 創建非`abstract``Invoker`類型,該子類對抽象類型進行。 類型`Invoker`必須重寫基類中聲明的所有抽象方法,重寫實現是方法綁定實現,但可以忽略非虛擬調度大小寫。

例如,假設上述`mono.android.test.Adder.add`方法是`abstract`。 C# 繫結變更,`Adder.Add`以便是抽象的,並且`AdderInvoker`將定義新類型,`Adder.Add`該類型已實現:

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

僅當`Invoker`獲取對 JAVA 創建的實例的 JNI 引用時,才需要該類型。

## <a name="binding-interfaces"></a>繫結介面

綁定介面在概念上類似於包含虛擬方法的綁定類,但許多細節在細微(而不是不那麼微妙)方面有所不同。 請考慮以下[Java 介面聲明](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

介面繫結有兩個部份:C# 介面定義和介面的 Invoker 定義。

### <a name="interface-definition"></a>介面定義

C# 介面定義必須滿足以下要求:

- 介面定義必須具有`[Register]`自定義屬性。

- 介面定義必須延伸`IJavaObject interface`。
    否則將阻止 ACL 從 JAva 介面繼承。

- 每個介面方法必須包含指定`[Register]`相應 JAVA 方法名稱、JNI 簽名和連接器方法的屬性。

- 連接器方法還必須指定連接器方法可以位於的類型。

綁定`abstract``virtual`和方法時,將在要註冊的類型的繼承層次結構中搜索連接器方法。 介面不能包含實體,因此這不起作用,因此需要指定指示連接器方法所在位置的類型。 類型在連接器方法字串中指定,在冒號`':'`之後,並且必須是包含調用器的類型的程式集限定類型名稱。

介面方法聲明是使用*相容*類型的相應 JAVA 方法的翻譯。 對於 Java 內建類型,相容類型是相應的 C# 類型`int`,例如`int`Java 是 C# 。 對於引用類型,相容類型是一種類型,可以提供適當 JAVA 類型的 JNI 句柄。

Java&ndash;調用不會直接調用介面成員,它將通過 Invoker 類型&ndash;進行仲介,因此允許一定程度的靈活性。

Java 進度介面可以在[C# 中宣告為](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

請注意,在上面,我們將 JAVA`int[]`參數映射到[JAVAArray&lt;int&gt;](xref:Android.Runtime.JavaArray`1)。
這是沒有必要的:我們可以把它綁定到C#,`int[]`或`IList<int>`, 或其他的東西完全。 無論選擇哪種類型,`Invoker`都需要能夠將其轉換為`int[]`Java 類型以進行呼叫。

### <a name="invoker-definition"></a>呼叫器定義

`Invoker`類型定義必須繼承`Java.Lang.Object`、實現適當的介面,並提供介面定義中引用的所有連接方法。 還有一個與類綁定不同的建議:`class_ref`欄位和方法 ID 應該是實例成員,而不是靜態成員。

首選實例成員的原因與`JNIEnv.GetMethodID`Android 運行時中的行為有關。 (這也可能是JAVA行為;它尚未經過測試。`JNIEnv.GetMethodID`當查找來自已實現介面而不是聲明介面的方法時,傳回 null。 考慮[java.util.SortedMap&lt;K,V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) Java介面,它實現了[java.util.Map&lt; &gt; K,V](https://developer.android.com/reference/java/util/Map.html)介面。 映射提供了一[個明確的](https://developer.android.com/reference/java/util/Map.html#clear())方法,因此 SortedMap`Invoker`的一個看似合理的定義是:

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

上述將失敗,因為`JNIEnv.GetMethodID`通過`SortedMap`類`null`實例查`Map.clear`找 方法時將返回。

有兩種解決方案:跟蹤每個方法來自哪個介面,並且每個介面都有一個`class_ref`介面,或者將所有內容保留為實例成員,並在最派生的類類型(而不是介面類型)上執行方法查找。 後者是在**Mono.安卓.dll。**

Invoker`Dispose`定義分為六個部分:建構函數、方法`ThresholdType`、`ThresholdClass`和`GetObject`成員、 方法、介面方法實現和連接器方法實現。

#### <a name="constructor"></a>建構函式

建構函數需要查找要呼叫的實體的執行時類別,並將執行時類儲存在實體`class_ref`欄位中:

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

注意:`Handle`屬性必須在建構函數正文中使用,`handle`而不是 參數,因為在 Android v4.0 上,`handle`參數在基本構造函數完成執行後可能無效。

#### <a name="dispose-method"></a>Dispose 方法

該方法`Dispose`需要釋放構造函數中分配的全域引用:

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

#### <a name="thresholdtype-and-thresholdclass"></a>閾值類型和閾值類

與`ThresholdType``ThresholdClass`成員與類別的關聯中的內容相同:

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

支援延伸`GetObject`需要靜態方法[。&lt; &gt; ](xref:Android.Runtime.Extensions.JavaCast*)

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>介面方法

介面的每個方法都需要有一個實現,該實現通過 JNI 調用相應的 JAva 方法:

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

連接器方法和支援基礎結構負責將 JNI 參數封送到適當的 C# 類型。 Java`int[]`參數將作為`jintArray`JNI 傳遞 ,它是 C# 內的參數。 `IntPtr` `IntPtr`必須將 封`JavaArray<int>`送到 a,以支援呼叫 C# 介面:

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

如果`int[]`優先於`JavaList<int>`,則可以使用[JNIEnv.GetArray()](xref:Android.Runtime.JNIEnv.GetArray*)代替:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

但是請注意,在`JNIEnv.GetArray`VM 之間複製整個陣列,因此對於大型陣列,這可能導致大量增加的 GC 壓力。

### <a name="complete-invoker-definition"></a>完整的呼叫器定義

[完整的IAdderProgressInvoker定義](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

## <a name="jni-object-references"></a>JNI 物件參照

許多 JNIEnv 方法傳回*JNI* *物件引用*,`GCHandle`它們類似於 s。 JNI 提供三種不同類型的物件引用:本地引用、全域引用和弱全域引用。 所有三個都表示為`System.IntPtr`,*但*(根據 JNI 函數`IntPtr``JNIEnv`類型部分)並非從 方法返回的所有 s 都是引用。 例如[,JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*)傳回`IntPtr`一個 ,但它不傳回物件`jmethodID`引用,它傳回一個 。 有關詳細資訊,請參閱[JNI 功能文件](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)。

本地引用由*大多數*引用創建方法創建。
Android 只允許在任何給定時間(通常為 512)存在有限數量的本地引用。 可以通過[JNIEnv.DeletelocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)刪除本地引用。
與 JNI 不同,並非所有返回物件引用的引用 JNIEnv 方法都返回本地引用;[JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*)傳回*全域*引用。 強烈建議您盡可能快速刪除本地引用,可能透過在物件周圍建構[Java.Lang.object.object.object,](xref:Java.Lang.Object)並指定`JniHandleOwnership.TransferLocalRef` [Java.Lang.object.object(IntPtr 句柄,JniHandle擁有權傳輸)](xref:Java.Lang.Object#ctor*)構造函數。

全域引用由[JNIEnv.NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*)和[JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*)創建。
它們可以通過[JNIEnv.刪除全域Ref](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*)銷毀。
模擬器具有 2,000 個未完成的全域引用的限制,而硬體設備的限制約為 52,000 個全域引用。

弱全域引用僅在 Android v2.2 (Froyo) 和更高版本中可用。 弱全域引用可以通過[JNIEnv.刪除 WeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*)刪除。

### <a name="dealing-with-jni-local-references"></a>處理 JNI 本地端

[JNIEnv.GetObjectfield、](xref:Android.Runtime.JNIEnv.GetObjectField*) [JNIEnv.GetStaticObjectobjectfield,](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) [JNIEnv.CallObjectmethod、](xref:Android.Runtime.JNIEnv.CallObjectMethod*) [JNIEnv.CallNon虛擬物件法](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*)和[JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)方法返回 一個`IntPtr`包含對 Java 物件的`IntPtr.Zero`JNI`null`本地引用, 或者如果 Java 返回 . 由於可以同時未完成的本地引用數量有限(512 個條目),因此最好確保及時刪除引用。 可以處理本地引用的三種方法:顯式刪除它們、創建實例`Java.Lang.Object`來保存它們,以及使用`Java.Lang.Object.GetObject<T>()`在 它們周圍創建託管可調用包裝器。

### <a name="explicitly-deleting-local-references"></a>明確移除本地端

[JNIEnv.刪除本地Ref](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)用於刪除本地引用。 刪除本地引用後,它不能再使用,因此必須小心確保這是`JNIEnv.DeleteLocalRef`使用本地引用完成的最後一件事。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>使用 Java.Lang.Object 進行包裝

`Java.Lang.Object`提供了一個[Java.Lang.object(IntPtr句柄,JniHandle 擁有權傳輸)](xref:Java.Lang.Object#ctor*)構造函數,可用於包裝退出的JNI引用。 [JniHandle 擁有權](xref:Android.Runtime.JniHandleOwnership)參數確定`IntPtr`如何處理 該參數:

- [JniHandle擁有權.不要轉移](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer)&ndash;創建`Java.Lang.Object`的 實例將從參數`handle`創建新的 全域引用`handle`,並且 保持不變。
    如有必要,調用方負責釋放`handle`。

- [JniHandle擁有權.轉移本地Ref](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef)&ndash;創建`Java.Lang.Object`的 實體將從參數建立`handle`新的 全域引用,`handle`並刪除[JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) 。 調用方不能釋放`handle`,並且不能在構`handle`造 函數完成執行后使用。

- [JniHandle 擁有權.轉移全域Ref](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef)&ndash;創建`Java.Lang.Object`的 實例`handle`將接管 參數的擁有權。 呼叫者不得釋放`handle`。

由於 JNI 方法呼叫方法傳回`JniHandleOwnership.TransferLocalRef`本地參考, 因此通常使用:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

在垃圾回收實體之前,`Java.Lang.Object`不會釋放建立的全域引用。 如果能夠,處理實例將釋放全域引用,加快垃圾回收:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>使用&lt;Java.Lang.object.getobject&gt;T ()

`Java.Lang.Object`提供了一個[Java.Lang.object.GetObject&lt;T(IntPtr&gt;句柄,JniHandle擁有權傳輸)](xref:Java.Lang.Object.GetObject*)方法,可用於創建指定類型的託管可調用包裝器。

該類型`T`必須滿足以下要求:

1. `T`必須是引用類型。

1. `T` 必須實作 `IJavaObject` 介面。

1. 如果`T`不是抽象類或介面,則`T`必須 提供參數`(IntPtr,
    JniHandleOwnership)`類型的構造函數。

1. 如果是`T`抽象類別或介面,*則必須*有可`T`供的*呼叫器*。 調用器是繼承`T`或`T`實現 的非抽象類型,其`T`名稱與調用後綴相同。 例如,如果 T`Java.Lang.IRunnable`是介面`Java.Lang.IRunnableInvoker`,則類型 必須存在,並且`(IntPtr,
    JniHandleOwnership)`必須包含所需的構造函數。

由於 JNI 方法呼叫方法傳回`JniHandleOwnership.TransferLocalRef`本地參考, 因此通常使用:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>尋找 Java 類型

要查找 JNI 中的欄位或方法,必須首先查找欄位或方法的聲明類型。 [Android.Runtime.JNIEnv.FindClass(字串)](xref:Android.Runtime.JNIEnv.FindClass*)方法用於查找JAVA類型。 字串參數是*Java 型態的簡化型態參考*或*完整類型參考*。 有關簡化和完整類型引用的詳細資訊,請參閱[JNI 類型參考部分](#_JNI_Type_References)。

注意: 與`JNIEnv`返回 物件實例的其他所有方法`FindClass`不同, 傳回全域引用,而不是本地引用。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>實體欄位

欄位透過*欄位指示*進行操作。 欄位代碼 透過[JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*)取得 ,這需要在 中定義欄位的類別、欄位的名稱以及欄位的[JNI 類型簽署](#JNI_Type_Signatures)。

欄位號不需要釋放,只要載入了相應的 JAVA 類型,欄位指示就有效。 (Android 當前不支援類卸載。

有兩組用於操作實例欄位的方法:一組用於讀取實例欄位,另一組用於編寫實例欄位。 所有方法集都需要欄位 ID 來讀取或寫入欄位值。

### <a name="reading-instance-field-values"></a>讀取實體欄位值

讀取實體欄位值的方法集遵循命名模式:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

欄位`*`型態在哪裡:

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*)&ndash;讀取任何不是內置類型的實例欄位的值,`java.lang.Object`例如 ,陣列和介面類型。 返回的值是 JNI 本地引用。

- [JNIEnv.GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*)&ndash;讀取實例欄`bool`位的值。

- [JNIEnv.GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*)&ndash;讀取實例欄`sbyte`位 的值。

- [JNIEnv.GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*)&ndash;讀取實例欄`char`位 的值。

- [JNIEnv.GetShortfield](xref:Android.Runtime.JNIEnv.GetShortField*)&ndash;讀取實例欄`short`位 的值。

- [JNIEnv.GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*)&ndash;讀取實例欄`int`位 的值。

- [JNIEnv.GetLongfield](xref:Android.Runtime.JNIEnv.GetLongField*)&ndash;讀取實例欄`long`位 的值。

- [JNIEnv.GetFloatfield](xref:Android.Runtime.JNIEnv.GetFloatField*)&ndash;讀取實例欄`float`位 的值。

- [JNIEnv.GetDoublefield](xref:Android.Runtime.JNIEnv.GetDoubleField*)&ndash;讀取實例欄`double`位 的值。

### <a name="writing-instance-field-values"></a>寫入實體欄位值

編寫實體的欄位值的編寫設定模式:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

*類型*是欄位的類型:

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 編寫任何不是內建類型的欄位的值`java.lang.Object`,例如 ,數位和介面類型。 該`IntPtr`值可以是 JNI 本地引用、JNI 全域引用、JNI`IntPtr.Zero`弱`null`全域引用 或 (for )。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`bool`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`sbyte`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`char`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`short`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`int`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`long`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`float`實例 欄位的值。

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) 寫入`double`實例 欄位的值。

<a name="_Static_Fields" />

## <a name="static-fields"></a>靜態欄位

靜態欄位通過*字段指示*進行操作。 欄位代碼 透過[JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*)取得,這需要在 中定義的類別、欄位的名稱和欄位的[JNI 類型簽署](#JNI_Type_Signatures)。

欄位號不需要釋放,只要載入了相應的 JAVA 類型,欄位指示就有效。 (Android 當前不支援類卸載。

有兩組用於操作靜態欄位的方法:一組用於讀取實例欄位,另一組用於編寫實例欄位。 所有方法集都需要欄位 ID 來讀取或寫入欄位值。

### <a name="reading-static-field-values"></a>讀取靜態欄位值

讀取靜態欄位值的方法集遵循命名模式:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

欄位`*`型態在哪裡:

- [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*)&ndash;讀取任何不是內置類型的靜態欄位的值,`java.lang.Object`例如 ,陣列和介面類型。 返回的值是 JNI 本地引用。

- [JNIEnv.取得靜態布林欄位](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*)&ndash;讀取靜態欄位`bool`的值 。

- [JNIEnv.取得靜態位元組](xref:Android.Runtime.JNIEnv.GetStaticByteField*)&ndash;讀取靜態欄位的值`sbyte`。

- [JNIEnv.取得靜態字元欄位](xref:Android.Runtime.JNIEnv.GetStaticCharField*)&ndash;讀取靜態欄位的`char`值 。

- [JNIEnv.取得靜態短欄位](xref:Android.Runtime.JNIEnv.GetStaticShortField*)&ndash;讀取靜態欄位的`short`值 。

- [JNIEnv.取得靜態龍場](xref:Android.Runtime.JNIEnv.GetStaticLongField*)&ndash;讀取靜態欄位的`long`值 。

- [JNIEnv.取得靜態浮場](xref:Android.Runtime.JNIEnv.GetStaticFloatField*)&ndash;讀取靜態欄位的`float`值 。

- [JNIEnv.取得靜態雙欄位](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*)&ndash;讀取靜態欄位的`double`值 。

### <a name="writing-static-field-values"></a>寫入靜態欄位值

編寫靜態欄位值的方法集遵循命名模式:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

*類型*是欄位的類型:

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入任何不是內建類型的靜態欄位的值`java.lang.Object`,例如 ,陣列和介面類型。 該`IntPtr`值可以是 JNI 本地引用、JNI 全域引用、JNI`IntPtr.Zero`弱`null`全域引用 或 (for )。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`bool`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`sbyte`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`char`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`short`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`int`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`long`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`float`靜態 欄位的值。

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) 寫入`double`靜態 欄位的值。

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>實體方法

實例方法通過*方法 ID*調用。 方法 ID 透過[JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*)取得 ,這需要在 中定義該方法的類型、方法的名稱以及方法的[JNI 類型簽名](#JNI_Type_Signatures)。

方法 I 不需要釋放,只要載入了相應的 JAVA 類型,該方法就有效。 (Android 當前不支援類卸載。

調用方法有兩組方法:一組用於虛擬調用方法,另一組用於非虛擬調用方法。 這兩組方法都需要一個方法 ID 來調用 該方法,非虛擬調用還要求指定應調用的類實現。

介面方法只能在聲明類型中備份;無法抬起來自擴展/繼承介面的方法。 有關詳細資訊,請參閱後面的綁定介面/調用器實現部分。

可以備份類或任何基類或已實現介面中聲明的任何方法。

### <a name="virtual-method-invocation"></a>虛擬方法呼叫

呼叫方法的方法集實際上遵循命名模式:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

方法`*`的返回類型在哪裡。

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*)&ndash;呼叫一個返回非內置類型的方法,`java.lang.Object`如數位和介面。 返回的值是 JNI 本地引用。

- [JNIEnv.CallBoolean方法](xref:Android.Runtime.JNIEnv.CallBooleanMethod*)&ndash;調用`bool`返回 值的方法。

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*)&ndash;呼叫`sbyte`返回 值的方法。

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*)&ndash;呼叫`char`返回 值的方法。

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*)&ndash;呼叫`short`返回 值的方法。

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*)&ndash;呼叫`long`返回 值的方法。

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*)&ndash;呼叫`float`返回 值的方法。

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*)&ndash;呼叫`double`返回 值的方法。

### <a name="non-virtual-method-invocation"></a>非虛擬方法呼叫

用於非虛擬呼叫方法的方法集遵循命名模式:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

方法`*`的返回類型在哪裡。 非虛擬方法調用通常用於調用虛擬方法的基本方法。

- [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*)&ndash;非虛擬調用返回非內置類型的方法,`java.lang.Object`如陣列和介面。 返回的值是 JNI 本地引用。

- [JNIEnv.CallNonvirtualBoolean方法](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*)&ndash;非虛擬調用`bool`返回 值的方法。

- [JNIEnv.CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*)&ndash;非虛擬調用`sbyte`返回 值的方法。

- [JNIEnv.CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*)&ndash;非虛擬調用`char`返回 值的方法。

- [JNIEnv.CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*)&ndash;非虛擬調用`short`返回 值的方法。

- [JNIEnv.CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*)&ndash;非虛擬調用`long`返回 值的方法。

- [JNIEnv.CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*)&ndash;非虛擬調用`float`返回 值的方法。

- [JNIEnv.CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*)&ndash;非虛擬調用`double`返回 值的方法。

<a name="_Static_Methods" />

## <a name="static-methods"></a>靜態方法

靜態方法通過*方法 ID*調用。 方法 ID 透過[JNIEnv.GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)取得,這需要在中定義該方法的類型、方法的名稱以及方法的[JNI 類型簽署](#JNI_Type_Signatures)。

方法 I 不需要釋放,只要載入了相應的 JAVA 類型,該方法就有效。 (Android 當前不支援類卸載。

### <a name="static-method-invocation"></a>靜態方法呼叫

呼叫方法的方法集實際上遵循命名模式:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

方法`*`的返回類型在哪裡。

- [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)&ndash;呼叫一個靜態方法,該方法返回非內建類型,`java.lang.Object`如陣列和介面。 返回的值是 JNI 本地引用。

- [JNIEnv.CallStaticBoolean方法](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*)&ndash;調用返回值`bool`的 靜態方法。

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*)&ndash;調用返回值`sbyte`的 靜態方法。

- [JNIEnv.CallStaticCharmethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*)&ndash;呼叫返回值`char`的靜態方法。

- [JNIEnv.CallStaticShortmethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*)&ndash;呼叫返回值`short`的靜態方法。

- [JNIEnv.callStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*)&ndash;調用返回值`long`的 靜態方法。

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*)&ndash;呼叫返回值`float`的靜態方法。

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*)&ndash;調用返回值`double`的 靜態方法。

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 類型簽署

[JNI 類型簽名](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)是[JNI 類型引用](#_JNI_Type_References)(儘管不是簡化的類型引用),但方法除外。 對於方法,JNI 類型簽名是一個開放的括`'('`弧 ,後跟所有串聯在一起的參數類型的類型引用(沒有分隔逗號或其他任何內容),然後是閉合`')'`括弧 ,後跟方法返回類型的 JNI 類型引用。

例如,給定 Java 方法:

```java
long f(int n, String s, int[] array);
```

JNI 類型簽名將是:

```csharp
(ILjava/lang/String;[I)J
```

通常 *,強烈建議使用*該`javap`命令來確定 JNI 簽名。 例如[,java.lang.Thread.state.s.state.s.sa.state.值](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法的 JNI 類型簽名是"(Ljava/lang/String;)Ljava/lang/thread$狀態;",而[java.lang.Thread.state.value](https://developer.android.com/reference/java/lang/Thread.State.html#values)方法的 JNI 類型簽名是"()[Ljava/lang/lang/thread$State;"。 小心尾隨分號;*這些是*JNI 類型簽名的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 類型參考

JNI 類型引用不同於 JAVA 類型引用。 不能使用完全限定的 Java`java.lang.String`類型名稱 (如 JNI,則必須使用`"java/lang/String"``"Ljava/lang/String;"`JNI 變體 或 ,具體取決於上下文;有關詳細資訊,請參閱下文。
有四種類型的 JNI 類型引用:

- **內置**
- **簡化**
- **型別**
- **陣列**

### <a name="built-in-type-references"></a>內建型態參考

內置類型引用是單個字元,用於引用內置值類型。 映射如下:

- `"B"`for `sbyte` 。
- `"S"`for `short` 。
- `"I"`for `int` 。
- `"J"`for `long` 。
- `"F"`for `float` 。
- `"D"`for `double` 。
- `"C"`for `char` 。
- `"Z"`for `bool` 。
- `"V"`用於`void`方法返回類型。

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>簡化型態參考

簡化類型引用只能在[JNIEnv.FindClass(字串)](xref:Android.Runtime.JNIEnv.FindClass*)中使用。
有兩種方法可以派生簡化的類型引用:

1. 從完全限定的 Java 名稱`'.'`中, 將套件名稱中的每個與類型`'/'``'.'``'$'`名稱之前的每個 名稱取代為 。

1. 閱讀的`'unzip -l android.jar | grep JavaName'`輸出。

兩者中的任何一個都將導致 JAVA 類型[java.lang.Thread.狀態](https://developer.android.com/reference/java/lang/Thread.State.html)映射到`java/lang/Thread$State`簡化的類型引用 。

### <a name="type-references"></a>類型參考

類型引用是內置類型引用或具有`'L'`首碼和後綴`';'`的簡化類型引用。 對於 Java 類型[java.lang.String,](https://developer.android.com/reference/java/lang/String.html)`"java/lang/String"`簡化的類型參考為`"Ljava/lang/String;"`,而類型參考為 。

類型引用與陣列類型引用和 JNI 簽署一起使用。

獲取類型引用的另一種方法是讀取`'javap -s -classpath android.jar fully.qualified.Java.Name'`的 輸出。
根據所涉及的類型,可以使用構造函數聲明或方法返回類型來確定 JNI 名稱。 例如：

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

`Thread.State`是 Java 枚舉類型,因此`valueOf`我們可以使用 方法的簽名來確定類型引用是 Ljava/lang/Thread$State;。

### <a name="array-type-references"></a>陣列類型參考

陣列類型引用`'['`預指 JNI 類型引用。
指定陣列時,不能使用簡化的類型引用。

例如,`int[]``"[I"``int[][]`是`"[[I"`,`java.lang.Object[]``"[Ljava/lang/Object;"`與是 。

## <a name="java-generics-and-type-erasure"></a>Java 泛型和類型擦除

*大多數時候*,通過JNI看,JAva泛型*並不存在*。
有一些「皺紋」,但這些皺紋是JAVA如何與泛型交互,而不是JNI如何查找和調用泛型成員。

通過 JNI 進行交互時,泛型類型或成員和非泛型類型或成員之間沒有區別。 例如,泛型類型[&lt;&gt;java.lang.Class T](https://developer.android.com/reference/java/lang/Class.html)也是「原始」泛型`java.lang.Class`類型,兩者具有相同的簡化`"java/lang/Class"`類型引用 。

## <a name="java-native-interface-support"></a>Java 本機介面支援

[Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv)是 Jave 本機介面 (JNI) 的託管包裝器。 JNI 函數在[JAVA 本機介面規範](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)中聲明,儘管方法`JNIEnv*`已更改以刪除`IntPtr`顯式`jobject`參數,`jclass``jmethodID`並用於而不是、、 等。例如,請考慮[JNI NewObject 函數](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

這作為[JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法公開:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

兩個調用之間的轉換相當簡單。 在 C 中,您將有:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

C# 等效項為:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

一旦將 Java Object 實例在 IntPtr 中持有,您可能想要使用它執行某些操作。 您可以使用 JNIEnv 方法(如[JNIEnv.CallVoidMethod())執行此操作](xref:Android.Runtime.JNIEnv.CallVoidMethod*),但如果已有類比 C# 包裝器,則需要在 JNI 引用上建構包裝器。 您可以[透過\<延伸 .JavaCast T>](xref:Android.Runtime.Extensions.JavaCast*)延伸方法執行此操作:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

您還可以使用[Java.Lang.object.object.getObject\<T>](xref:Java.Lang.Object.GetObject*)方法:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外,通過刪除每個 JNI 函數中的`JNIEnv*`參數, 所有 JNI 函數都進行了修改。

## <a name="summary"></a>總結

直接與 JNI 打交道是一種可怕的經歷,應不惜一切代價避免。 不幸的是,它並不總是可以避免的;希望本指南將提供一些幫助,當你擊中未綁定的JAVA的情況下與Android的Mono。

## <a name="related-links"></a>相關連結

- [Java 本機介面規格](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java 本機介面函數](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
