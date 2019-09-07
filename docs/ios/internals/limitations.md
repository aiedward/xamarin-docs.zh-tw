---
title: Xamarin 的限制
description: 本檔說明 Xamarin 的限制、討論泛型、NSObjects 的一般子類別、泛型物件中的 P/Invoke 等等。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/09/2018
ms.openlocfilehash: 83c71ebf844102a7d3a16969868f187237fb0d04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753331"
---
# <a name="limitations-of-xamarinios"></a>Xamarin 的限制

由於使用 Xamarin 的應用程式會編譯為靜態程式碼，因此無法在執行時間使用需要產生程式碼的任何功能。

相較于桌面 Mono，這些是 Xamarin 的限制：

 <a name="Limited_Generics_Support" />

## <a name="limited-generics-support"></a>有限的泛型支援

與傳統 Mono/NET.TCP 不同的是，iPhone 上的程式碼會在一段時間內以靜態方式編譯，而不是由 JIT 編譯程式視需要進行編譯。

Mono 的[完整 AOT](https://www.mono-project.com/docs/advanced/aot/#full-aot)技術對於泛型有一些限制，因為並非每個可能的泛型具現化都可以在編譯時期預先決定。 這不是一般 .NET 或 Mono 執行時間的問題，因為程式碼一律會使用即時編譯器在執行時間編譯。 但是，這對像是 Xamarin 的靜態編譯器造成挑戰。

開發人員遇到的一些常見問題包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />

### <a name="generic-subclasses-of-nsobjects-are-limited"></a>NSObjects 的一般子類別受到限制

在建立 NSObject 類別的泛型子類別（例如，不支援泛型方法）時，Xamarin 目前僅提供有限的支援。 從7.2.1，您可以使用 NSObjects 的一般子類別，如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 雖然可以使用 NSObjects 的一般子類別，但還是有一些限制。 如需詳細資訊，請閱讀 NSObject 檔的[一般子類別](~/ios/internals/api-design/nsobject-generics.md)

 <a name="No_Dynamic_Code_Generation" />

## <a name="no-dynamic-code-generation"></a>不產生動態程式碼

由於 iOS 核心會防止應用程式動態產生程式碼，因此 Xamarin 不支援任何形式的動態程式碼產生。 它們包括：

- 無法使用 [System.object]。
- 不支援 System.web. Remoting。
- 不支援動態建立類型（沒有類型. GetType （"MyType ' 1"）），不過，雖然查閱現有的類型（例如，類型. GetType （"System.string"））也沒問題。
- 反向回呼必須在編譯時期向執行時間註冊。

 <a name="System.Reflection.Emit" />

### <a name="systemreflectionemit"></a>System.Reflection.Emit

缺少 System.object。 **發出**表示不會有任何相依于執行時間程式碼產生作業的程式碼。 這包括如下的內容：

- 動態語言執行時間。
- 以動態語言執行時間為基礎的任何語言。
- 遠端處理的 TransparentProxy 或任何其他動作都會造成執行時間動態產生程式碼。

  > [!IMPORTANT]
  > 請勿混淆**反映。** 使用**反映**發出。 反映：發出的是關於動態產生程式碼，並將該程式碼加以 jit 和編譯為機器碼。 由於 iOS （無 JIT 編譯）的限制，不支援這種情況。

但是整個反映 API，包括類型. GetType （"someClass"）、清單方法、清單屬性、提取屬性和值都能正常運作。

### <a name="using-delegates-to-call-native-functions"></a>使用委派呼叫原生函式

若要透過C#委派呼叫原生函式，委派的宣告必須以下列其中一個屬性裝飾：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute)（偏好，因為它是跨平臺且與 .NET Standard 1.1 + 相容）
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

若無法提供其中一個屬性，將會產生執行階段錯誤，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```

 <a name="Reverse_Callbacks" />

### <a name="reverse-callbacks"></a>反向回呼

在標準 Mono 中，可以將委派C#實例傳遞給非受控程式碼，代替函式指標。 執行時間通常會將這些函式指標轉換成可讓非受控碼回呼 managed 程式碼的小型 Thunk。

在 Mono 中，這些橋接器是由即時編譯器所執行。 當使用 iPhone 所需的預先編譯器時，此時有兩個重要的限制：

- 您必須使用[MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)來標示所有的回呼方法。
- 這些方法必須是靜態方法，不支援實例方法。

<a name="No_Remoting" />

## <a name="no-remoting"></a>無遠端處理

在 Xamarin. iOS 上無法使用遠端堆疊。

 <a name="Runtime_Disabled_Features" />

## <a name="runtime-disabled-features"></a>執行時間停用的功能

Mono 的 iOS 執行時間已停用下列功能：

- 程式碼剖析工具
- Reflection.Emit
- 反映。發出。儲存功能
- COM 系結
- JIT 引擎
- 中繼資料驗證器（因為沒有 JIT）

 <a name=".NET_API_Limitations" />

## <a name="net-api-limitations"></a>.NET API 限制

公開的 .NET API 是完整架構的子集，並非所有專案都可在 iOS 中使用。 如需[目前支援的元件清單](~/cross-platform/internals/available-assemblies.md)，請參閱常見問題。

特別是，Xamarin 所使用的 API 設定檔不包含 System. Configuration，因此無法使用外部 XML 檔案來設定執行時間的行為。
