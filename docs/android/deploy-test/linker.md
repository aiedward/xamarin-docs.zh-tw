---
title: 在 Android 上連結
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2018
ms.openlocfilehash: a79dcf14ddefd13d17e218602030a6467a3f1448
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643833"
---
# <a name="linking-on-android"></a>在 Android 上連結

Xamarin.Android 應用程式會使用「連結器」  來縮小應用程式的大小。 連結器會採用您應用程式的靜態分析來判斷實際使用到的組件、類型及成員。 連結器然後會像「記憶體回收行程」  一樣，持續尋找參考的組件、類型及成員，直到整個參考組件、類型及成員終止為止。 然後，所有在此終止之外的項目便會遭到「捨棄」  。

例如，[Hello, Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellom4a) 範例：

|組態|1.2.0 大小|4.0.1 大小|
|---|---|---|
|沒有連結的版本：|14.0 MB|16.0 MB|
|有連結的版本：|4.2 MB|2.9 MB|

連結可將套件的大小縮小至原始 (未連結) 套件的 30% (1.2.0) 及 18% (4.0.1)。



## <a name="control"></a>控制

連結乃根據「靜態分析」(  。 因此，無法偵測相依於執行階段環境的任何項目：

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```


### <a name="linker-behavior"></a>連結器行為

控制連結器的主要機制位於 [專案選項]  對話方塊中的 [連結器行為]  (Visual Studio 中的 [連結]  ) 下拉式功能表。 有三種選項：

1.  **不要連結** (Visual Studio 中的 [無]  )
1.  **連結 SDK 組件** (僅 SDK 組件) 
1.  []**連結所有組件** (SDK 及使用者組件) 


[不要連結]  選項會關閉連結器，上述「沒有連結的版本」應用程式大小範例便使用了這項行為。 這在針對執行階段進行疑難排解失敗時非常有用，可查看問題是否出於連結器。 這項設定通常不建議用於正式版本。

[連結 SDK 組件]  選項只會連結 [Xamarin.Android 隨附的組件](~/cross-platform/internals/available-assemblies.md)。
所有其他的組件 (例如您的程式碼) 皆不會連結。

[連結所有組件]  選項會連結所有組件，表示若您的程式碼沒有靜態參考，也可能會遭到移除。

上述範例若選取 [不要連結]  與 [連結 SDK 組件]  選項皆可以正常運作，但若選取 [連結所有組件]  便會失敗，並產生下列錯誤：

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```


### <a name="preserving-code"></a>保留程式碼

連結器有時候會移除您想要保留的程式碼。 例如︰

-   您也有可能會透過 `System.Reflection.MemberInfo.Invoke` 動態呼叫程式碼。

-   若您是以動態方式來具現化類型，您可能會想要保留您類型的預設建構函式。

-   若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

在這些案例下，您可以使用 [Android.Runtime.Preserve](xref:Android.Runtime.PreserveAttribute) 屬性。 由於每個未由應用程式靜態連結的成員都會遭到移除，因此這個屬性可用來標示沒有靜態參考，但您的應用程式仍然需要的成員。 您可以將此屬性套用到類型的每個成員，或是類型本身。

在下列範例中，此屬性會用於保留 `Example` 類別的建構函式：

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

若您要保留整個類型，可使用下列屬性語法：

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

例如，在下列程式碼片段中，整個 `Example` 類別都會針對 XML 序列化進行保留：

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

有時候您會想要只有在包含的類型受到保留時，才保留特定的成員。 在這些案例中，請使用下列屬性語法：

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

若您不想要相依於 Xamarin 程式庫 &ndash; 例如，您正在建置一個跨平台可攜式類別庫 (PCL) &ndash; 您仍然可以使用 `Android.Runtime.Preserve` 屬性。 若要執行此動作，請遵循此方式在 `Android.Runtime` 命名空間中宣告 `PreserveAttribute` 類別：

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```

在上述範例中，在 `Android.Runtime` 命名空間中宣告了 `Preserve` 屬性，但因為連結器會依類型名稱查詢此屬性，所以您可在任何命名空間中使用 `Preserve` 屬性。



### <a name="falseflag"></a>falseflag

若無法使用 [Preserve] 屬性，其通常提供一個程式碼區塊，讓連結器認為有使用到該類型，同時防止程式碼區塊在執行階段時執行是很有用的做法。 若要使用這項技術，我們可以這樣做：

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```



### <a name="linkskip"></a>linkskip

您也可以指定一組使用者提供的組件完全不進行連結，同時透過利用 [AndroidLinkSkip MSBuild 屬性](~/android/deploy-test/building-apps/build-process.md)允許其他使用者組件使用「連結 SDK 組件」  行為來跳過：

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```


### <a name="linkdescription"></a>LinkDescription

[`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
**建置動作**可用於包含[自訂連結器組態檔](~/cross-platform/deploy-test/linker.md)。
檔的 SSDL 區段。 為保留需保留的 `internal` 或 `private` 成員，自訂連結器組態檔可能為必要項目。



### <a name="custom-attributes"></a>自訂屬性

連結組件時，會從所有成員移除下列自訂屬性類型：

-  System.ObsoleteAttribute
-  System.MonoDocumentationNoteAttribute
-  System.MonoExtensionAttribute
-  System.MonoInternalNoteAttribute
-  System.MonoLimitationAttribute
-  System.MonoNotSupportedAttribute
-  System.MonoTODOAttribute
-  System.Xml.MonoFIXAttribute


連結組件時，會從發行組建中的所有成員移除下列自訂屬性類型：

-  System.Diagnostics.DebuggableAttribute
-  System.Diagnostics.DebuggerBrowsableAttribute
-  System.Diagnostics.DebuggerDisplayAttribute
-  System.Diagnostics.DebuggerHiddenAttribute
-  System.Diagnostics.DebuggerNonUserCodeAttribute
-  System.Diagnostics.DebuggerStepperBoundaryAttribute
-  System.Diagnostics.DebuggerStepThroughAttribute
-  System.Diagnostics.DebuggerTypeProxyAttribute
-  System.Diagnostics.DebuggerVisualizerAttribute


## <a name="related-links"></a>相關連結

- [自訂連結器設定](~/cross-platform/deploy-test/linker.md)
- [在 iOS 上連結](~/ios/deploy-test/linker.md)
