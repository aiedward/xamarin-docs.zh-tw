---
title: Xamarin.iOS 的限制
description: 本文件說明 Xamarin.iOS，討論泛型、 泛型子類別的 NSObjects、 P/Invokes 中泛型的物件，以及其他的限制。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/09/2018
ms.openlocfilehash: e154e4e1688b8a3d03459956934409fa9d5aef35
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998081"
---
# <a name="limitations-of-xamarinios"></a>Xamarin.iOS 的限制

使用 Xamarin.iOS iPhone 上的應用程式會編譯成靜態程式碼，因為它不使用任何需要在執行階段產生的程式碼的功能。

相較於 Mono 桌面的 Xamarin.iOS 限制如下：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型支援

不同於傳統 Mono/.NET，iPhone 上的程式碼會以靜態方式編譯而不是由 JIT 編譯器正在編譯視事先。

Mono[完整 AOT](http://www.mono-project.com/docs/advanced/aot/#full-aot)技術有幾項限制對於泛型，因為您可以事先判斷不是每個可能的泛型具現化，在編譯時期，這些造成。 因為程式碼一律會在執行階段使用 Just in 編譯器編譯，這是不一般的.NET 或 Mono 執行階段發生問題。 但是，這會造成類似 Xamarin.iOS 靜態編譯器的挑戰。

一些常見的問題，開發人員執行，包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>泛型 NSObjects 子類別會受到限制

Xamarin.iOS 目前具有有限的支援建立 NSObject 類別，例如不支援泛型方法的泛型子類別。 從 7.2.1，開始使用 NSObjects 的泛型子類別，是可行的與下列類似的：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 雖然可能會有 NSObjects 的泛型子類別，有一些限制。 讀取[NSObject 的泛型子](~/ios/internals/api-design/nsobject-generics.md)文件的詳細資訊



### <a name="pinvokes-in-generic-types"></a>P/叫用泛型型別中

泛型類別中的 P/Invokes 不支援：

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>不支援可為 Null 的型別上 Property.SetInfo

若要設定可為 Null 的值使用反映的 Property.SetInfo&lt;T&gt;目前不支援。

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>實值型別，做為字典索引鍵

使用實值型別做為字典&lt;TKey，TValue&gt;機碼是有問題，做為預設字典建構函式會嘗試使用 EqualityComparer&lt;TKey&gt;。預設值。 EqualityComparer&lt;TKey&gt;。預設值，接著會嘗試使用反映來具現化新的型別會實作 IEqualityComparer&lt;TKey&gt;介面。

這適用於參考型別 (反映 + 建立新類型的步驟會略過)，但值類型它當機，並快燒壞，一旦您嘗試在裝置上使用它。

 **因應措施**： 以手動方式實作[IEqualityComparer&lt;TKey&gt; ](xref:System.Collections.Generic.IEqualityComparer`1)介面中的新類型，並提供該類型的執行個體[字典&lt;TKey，TValue&gt; ](xref:System.Collections.Generic.Dictionary`2) [(IEqualityComparer&lt;TKey&gt;)](xref:System.Collections.Generic.IEqualityComparer`1)建構函式。


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>不產生動態程式碼

IPhone 的核心會防止應用程式，以動態方式產生程式碼，因為在 iPhone 上的 Mono 不支援任何形式的動態程式碼產生。 它們包括：

-  無法使用 System.Reflection.Emit。
-  System.Runtime.Remoting 不支援。
-  不支援動態建立型別 (沒有 Type.GetType ("MyType ' 1"))，雖然查閱現有型別 (Type.GetType ("System.String")，例如運作正常)。 
-  反向的回呼必須向執行階段，在編譯時期。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

System.Reflection 缺乏。 **發出**取決於執行階段程式碼產生任何程式碼運作的方式。 這包括像是：

-  Dynamic Language Runtime。
-  動態語言執行平台為基礎的任何語言。
-  遠端處理之 TransparentProxy 或任何其他項目，會導致執行階段動態產生程式碼。 


 **重要事項︰** 請勿混淆**Reflection.Emit**具有**反映**。 Reflection.Emit 即將以動態方式產生程式碼，並具有該程式碼加以 Jit 和編譯為原生程式碼。 在 iphone （沒有 JIT 編譯） 的限制不是支援此。

但是，整個的反映 API，包括 Type.GetType (「 someClass") 方法，列出屬性，擷取屬性和值可正常運作。

### <a name="using-delegates-to-call-native-functions"></a>使用委派來呼叫原生函式

若要呼叫原生的函式，透過 C# 委派，委派的宣告必須使用其中一個下列的屬性裝飾：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) （偏好選項，因為它是跨平台和相容於.NET Standard 1.1 +）
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

無法提供其中一個屬性，會導致執行階段錯誤，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向的回呼

在標準 Mono 中就可以傳遞至 unmanaged 程式碼取代函式指標的 C# 委派執行個體。 執行階段通常會將這些函式指標轉換成小型的 thunk，可讓 unmanaged 程式碼來回呼至 managed 程式碼中。

在 Mono 中這些橋接器由在時間只要編譯器。 當使用預先 just-in-time 編譯器所需的 iPhone 有兩個重要的限制的此時：

-  您必須加上旗標的回呼方法與所有[MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  方法必須是靜態方法，沒有支援執行個體方法。 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>沒有遠端執行功能

無法在 Xamarin.iOS 上使用遠端堆疊。


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>執行階段停用功能

在 iOS 中 Mono 執行階段已停用下列功能：

-  程式碼剖析工具
-  這些事件處理常式
-  Reflection.Emit.Save 功能
-  COM 繫結
-  JIT 引擎
-  中繼資料驗證器 （因為沒有任何 JIT）


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>.NET API 限制

因為並非所有使用 iOS 中，公開的.NET API 會是完整架構的子集。 請參閱常見問題集[目前支援的組件清單](~/cross-platform/internals/available-assemblies.md)。



特別是，使用 Xamarin.iOS 的 API 設定檔不包含 System.Configuration，因此無法使用外部 XML 檔案來設定執行階段行為。
