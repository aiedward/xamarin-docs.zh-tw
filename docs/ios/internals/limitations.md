---
title: 限制
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a75d76ad1292955003705a5ddc1d52381addc796
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="limitations"></a>限制

使用 Xamarin.iOS 在 iPhone 上的應用程式會編譯成靜態程式碼，因為它不使用任何需要在執行階段產生的程式碼的功能。

以下為相較於桌面 Mono Xamarin.iOS 限制：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型支援

不同於傳統 Mono/.NET，而不是由 JIT 編譯器編譯視事先以靜態方式編譯在 iPhone 上的程式碼。

單聲道的[完整 AOT](http://www.mono-project.com/docs/advanced/aot/#full-aot)技術具有泛用方面有一些限制，這些會導致因為不是每個可能的泛型具現化可以在編譯時期的最前面位置來決定。 這不是一般的.NET 或 Mono 執行階段的問題如下程式碼永遠會在執行階段使用 Just 時間編譯器中編譯。 但是，這會帶來 Xamarin.iOS 靜態編譯器的挑戰。

遇到了開發人員，常見的問題包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>泛型 NSObjects 子類別會受到限制

目前 Xamarin.iOS 有限建立泛型子 NSObject 類別，例如不支援泛型方法的支援。 為準，7.2.1，使用泛型的 NSObjects 的子類別是可行的如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 雖然可能會有的 NSObjects 泛型子類別，但有一些限制。 讀取[泛型子類別的 NSObject](~/ios/internals/api-design/nsobject-generics.md)文件的詳細資訊



### <a name="pinvokes-in-generic-types"></a>P/Invokes 中泛型類型

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


### <a name="value-types-as-dictionary-keys"></a>實值類型做為字典索引鍵

使用實值類型做為字典&lt;Dictionary<tkey，Tvalue>&gt;金鑰會有問題，做為預設字典建構函式會嘗試使用 EqualityComparer&lt;TKey&gt;。預設值。 EqualityComparer&lt;TKey&gt;。預設值，接著嘗試使用反映來具現化新的類型會實作 IEqualityComparer&lt;TKey&gt;介面。

這適用於參考類型 (為反映 + 建立新會略過型別步驟)，但如值類型，當機和快速而燒壞，一旦您嘗試使用裝置上。

 **因應措施**： 手動實作[IEqualityComparer&lt;TKey&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/)介面中的新類型，並提供該類型的執行個體[字典&lt;TKey，Tvalue>&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%3CTKey,TValue%3E/) [(IEqualityComparer&lt;TKey&gt;)](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/)建構函式。


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>不產生動態程式碼

因為在 iPhone 核心避免應用程式動態產生程式碼 Mono 在 iPhone 上的不支援任何形式的動態程式碼產生。 它們包括：

-  System.Reflection.Emit 就無法使用。
-  System.Runtime.Remoting 不支援。
-  不支援動態建立類型 (沒有 Type.GetType ("MyType ' 1"))，雖然查閱現有型別 (Type.GetType ("System.String")，例如運作正常)。 
-  反向回呼必須向執行階段，在編譯時間。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

System.Reflection 缺乏。 **發出**取決於執行階段程式碼產生任何程式碼運作的方式。 這包括下列作業：

-  Dynamic Language Runtime。
-  任何語言建置動態語言執行平台之上。
-  遠端處理之 TransparentProxy 或任何其他項目，可能導致執行階段動態產生程式碼。 


 **重要事項：**請勿混淆**Reflection.Emit**與**反映**。 這些事件處理常式是關於動態產生程式碼，並擁有該 Jit 編譯為原生程式碼和。 由於在 iphone （沒有 JIT 編譯） 限制，所以這不支援。

但是，整個反映 API，包括 Type.GetType (「 someClass 」) 方法，列出擷取屬性和值的屬性，就可以正常運作。

 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向回呼

在標準 Mono 可能會傳遞至 unmanaged 程式碼，就不需函式指標的 C# 委派執行個體。 執行階段通常會將這些函式指標轉換為小型的 thunk，可讓 unmanaged 程式碼來呼叫 managed 程式碼。

在時間恰好中 Mono 實作這些橋接器編譯器。 當使用前的時間編譯器所需 iPhone，有兩個重要限制此時：

-  您必須加上旗標的回呼方法與所有[MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  方法可以是靜態方法，沒有支援執行個體方法。 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>沒有遠端執行功能

遠端處理堆疊不是使用 Xamarin.iOS 上。


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>執行階段停用功能

單聲道的 iOS 執行階段中已停用下列功能：

-  程式碼剖析工具
-  Reflection.Emit
-  Reflection.Emit.Save 功能
-  COM 繫結
-  JIT 引擎
-  中繼資料驗證器 （因為沒有任何 JIT）


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>.NET API 限制

在 iOS 中可取得不是 framework 的所有項目時，公開的.NET API 是 framework 的完整的子集。 請參閱常見問題集[目前支援的組件清單](~/cross-platform/internals/available-assemblies.md)。



特別的是，使用 Xamarin.iOS 的 API 設定檔不包含 System.Configuration，因此無法使用外部 XML 檔案，設定執行階段的行為。
