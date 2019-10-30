---
title: 為 Xamarin 輸入註冊機構
description: 本檔說明「Xamarin 類型註冊機構」，這讓C#類別可供目標-C 執行時間使用。
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: f38c49ce9334a5659f0a8b5dd03e3bae8863cf5a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022270"
---
# <a name="type-registrar-for-xamarinios"></a>為 Xamarin 輸入註冊機構

本檔說明 Xamarin 所使用的類型註冊系統。

## <a name="registration-of-managed-classes-and-methods"></a>註冊 managed 類別和方法

在啟動期間，Xamarin 會註冊：

- 具有[[Register]](xref:Foundation.RegisterAttribute)屬性的類別做為目標-C 類別。
- 具有[[Category]](xref:ObjCRuntime.CategoryAttribute)屬性的類別做為目標-C 類別。
- 以[[Protocol]](xref:Foundation.ProtocolAttribute)屬性作為目標 C 通訊協定的介面。
- 具有[[Export]](xref:Foundation.ExportAttribute)的成員，可讓目標-C 存取它們。

例如，請考慮在 Xamarin iOS 應用程式中常見的 managed `Main` 方法：

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

此程式碼會指示目標 C 執行時間使用稱為 `AppDelegate` 的類型做為應用程式的委派類別。 若要讓目標 C 執行時間能夠建立C#`AppDelegate`類別的實例，則必須註冊該類別。

「Xamarin」會在執行時間（動態註冊）或在編譯時期（靜態註冊）自動執行註冊。

動態登錄會在啟動時使用反映，以尋找所有要註冊的類別和方法，並將它們傳遞至目標-C 執行時間。 預設會針對模擬器組建使用動態註冊。

靜態註冊會在編譯時期檢查應用程式所使用的元件。 它會判斷要向目標 C 註冊的類別和方法，並產生內嵌于二進位的對應。
然後，在啟動時，它會向目標-C 執行時間註冊對應。 靜態註冊用於裝置組建。

### <a name="categories"></a>分類

從 Xamarin. iOS 8.10 開始，您可以使用C#語法建立目標 C 類別。

若要建立類別目錄，請使用 `[Category]` 屬性，並指定要擴充的類型。 例如，下列程式碼會擴充 `NSString`：

```csharp
[Category (typeof (NSString))]
```

類別的每個方法都有一個 `[Export]` 屬性，讓它可供目標-C 執行時間使用：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有 managed 擴充方法都必須是靜態的，但您可以使用擴充方法的標準C#語法來建立目標-C 實例方法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

擴充方法的第一個引數是叫用方法所在的實例：

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

這個範例會將原生 `toUpper` 實例方法加入至 `NSString` 類別。 您可以從目標-C 呼叫這個方法：

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>通訊協定

從 Xamarin. iOS 8.10 開始，具有 `[Protocol]` 屬性的介面將會匯出至目標-C 作為通訊協定：

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

此程式碼會將 `IMyProtocol` 匯出為稱為 `MyProtocol` 的通訊協定，並將稱為 `MyClass` 的類別（其會執行此通訊協定）。

## <a name="new-registration-system"></a>新的註冊系統

從穩定的6.2.6 版本和搶鮮版（Beta）6.3.4 版本開始，我們新增了新的靜態註冊機構。 在7.2.1 版本中，我們已將新的註冊機構設為預設值。

這個新的註冊系統提供下列新功能：

- 程式設計人員錯誤的編譯時間偵測：
  - 使用相同名稱註冊兩個類別。
  - 匯出了一個以上的方法來回應相同的選取器
- 移除未使用的機器碼：
  - 新的註冊系統將會對靜態程式庫中使用的程式碼加入強式參考，讓原生連結器可以從產生的二進位檔中去除未使用的機器碼。 在 Xamarin 的範例系結上，大部分的應用程式會變成至少300k.wvx 的小。

- 支援 `NSObject`的泛型子類別。如需詳細資訊，請參閱[NSObject 泛型](~/ios/internals/api-design/nsobject-generics.md)。 此外，新的註冊系統將會攔截不受支援的泛型結構，這在執行時間會造成隨機行為。

### <a name="errors-caught-by-the-new-registrar"></a>新註冊機構攔截到的錯誤

以下是新註冊機構所攔截到之錯誤的一些範例。

- 在同一個類別中多次匯出相同的選取器：

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo:")]
      void Foo (NSString str);
      [Export ("foo:")]
      void Foo (string str)
  }
  ```

- 匯出多個具有相同目標-C 名稱的 managed 類別：

  ```csharp
  [Register ("Class")]
  class MyClass : NSObject {}

  [Register ("Class")]
  class YourClass : NSObject {}
  ```

- 匯出泛型方法：

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo")]
      void Foo<T> () {}
  }
  ```

### <a name="limitations-of-the-new-registrar"></a>新註冊機構的限制

關於新的註冊機構，請注意下列事項：

- 某些協力廠商程式庫必須更新，才能與新的註冊系統搭配使用。 如需詳細資訊，請參閱下面的[必要修改](#required-modifications)。

- 短期的缺點也是，如果使用帳戶架構，就必須使用 Clang （這是因為 Apple 的**帳戶 .h**標頭只能由 Clang 編譯）。 如果您使用 Xcode 4.6 或更早版本，請將 `--compiler:clang` 新增至其他 mtouch 引數，以使用 Clang （Xamarin 會自動選取 Xcode 5.0 或更新版本中的 Clang）。

- 如果使用 Xcode 4.6 （或更早版本），則當匯出的類型名稱包含非 ASCII 字元時，必須選取 GCC/G + + （這是因為隨附于 Xcode 4.6 的 Clang 版本不支援在目標-C 程式碼中的識別碼內有非 ASCII 字元）。 將 `--compiler:gcc` 新增至其他 mtouch 引數，以使用 GCC。

## <a name="selecting-a-registrar"></a>選取註冊機構

您可以在專案的 [ **IOS 組建**] 設定中，將下列其中一個選項新增至其他 mtouch 引數，以選取不同的註冊機構：

- `--registrar:static` –裝置組建的預設值
- `--registrar:dynamic` –模擬器組建的預設值

> [!NOTE]
> Xamarin 的 Classic API 支援其他選項，例如 `--registrar:legacystatic` 和 `--registrar:legacydynamic`。 不過，Unified API 不支援這些選項。

## <a name="shortcomings-in-the-old-registration-system"></a>舊的註冊系統中的缺點

舊的註冊系統具有下列缺點：

- 協力廠商原生程式庫中的目標 C 類別和方法沒有（原生）靜態參考，這表示我們無法要求原生連結器移除未實際使用的協力廠商原生程式碼（因為所有專案都會被移除）。 這是 `-force_load libNative.a` 每個協力廠商系結必須執行的原因（或 `[LinkWith]` 屬性中的對等 `ForceLoad=true`）。
- 您可以使用相同的目標-C 名稱匯出兩個 managed 類型，而不發出警告。 在不同的命名空間中，常見的案例是有兩個 `AppDelegate` 類別。 在執行時間，已挑選的是完全隨機的（事實上，它會隨著應用程式的執行不會重建而改變，這是為了進行非常令人困惑且令人沮喪的調試經驗）。
- 您可以使用相同的目標-C 簽章匯出兩個方法。 同樣地，從目標-C 呼叫其中一個是隨機的（但此問題並不像前一個），這通常是因為實際遇到此 bug 的唯一方式是覆寫假面具受管理的方法）。
- 在動態和靜態組建之間，已匯出的一組方法稍有不同。
- 匯出泛型類別時，它無法正常運作（在執行時間執行的確切泛型實作為隨機，實際上會產生不確定的行為）。

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>新的註冊機構：系結的必要變更

本節說明必須進行的系結變更，才能使用新的註冊機構。

### <a name="protocols-must-have-the-protocol-attribute"></a>通訊協定必須具有 [Protocol] 屬性

通訊協定現在必須具有 `[Protocol]` 屬性。 如果您不這麼做，將會發生原生連結器錯誤，例如：

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>選取器必須有有效的參數數目

所有的選取器都必須正確指出參數的數目。 先前已忽略這些錯誤，而且可能會造成執行時間問題。

簡言之，冒號的數目必須符合參數數目：

- 無參數： `foo`
- 一個參數： `foo:`
- 兩個參數： `foo:parameterName2:`

下列是不正確的用法：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在匯出中使用 IsVariadic 參數

Variadic 函數必須使用 `[Export]` 屬性的 `IsVariadic` 引數：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必須連結到現有的符號

不可能系結不存在於原生程式庫中的類別。
如果已在原生程式庫中移除或重新命名類別，請務必更新要符合的系結。
