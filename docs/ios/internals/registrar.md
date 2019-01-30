---
title: 適用於 Xamarin.iOS 的型別註冊機構
description: 本文件說明 Xamarin.iOS 類型註冊機構，讓C#Objective C 執行階段的可用類別。
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 8/29/2018
ms.openlocfilehash: cd9e92e2c96d0a0696633e49869f2661e410d343
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233584"
---
# <a name="type-registrar-for-xamarinios"></a>適用於 Xamarin.iOS 的型別註冊機構

本文件說明 Xamarin.iOS 所使用的型別註冊系統。

## <a name="registration-of-managed-classes-and-methods"></a>註冊受管理的類別和方法

在啟動期間，將會註冊 Xamarin.iOS:

- 類別與[[註冊]](xref:Foundation.RegisterAttribute) OBJECTIVE-C 類別當做屬性。
- 類別與[[Category]](xref:ObjCRuntime.CategoryAttribute) OBJECTIVE-C 類別的屬性。
- 與互動[[通訊協定]](xref:Foundation.ProtocolAttribute)與 OBJECTIVE-C 通訊協定的屬性。
- 具有成員[[匯出]](xref:Foundation.ExportAttribute)，使得適用於 OBJECTIVE-C 可以存取它們。

例如，假設 managed`Main`在 Xamarin.iOS 應用程式中常見的方法：

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

此程式碼會告訴 Objective C 執行階段使用的型別，稱為`AppDelegate`為應用程式的委派類別。 若要能夠建立的執行個體 Objective C 執行階段C#`AppDelegate`類別，必須註冊類別。

自動在執行階段 （動態註冊），或在編譯時期 （靜態註冊） 時，Xamarin.iOS 將執行註冊。

動態註冊使用反映在啟動時若要尋找所有類別和方法，若要註冊，將它們傳遞至 OBJECTIVE-C 執行階段。 對於模擬器組建，則預設會使用動態註冊。

靜態註冊會檢查，在編譯時期，應用程式所使用的組件。 它的類別和方法，以向 OBJECTIVE-C 會決定，並產生對應，且會內嵌到您的二進位檔。
然後，在啟動時，它會向地圖 OBJECTIVE-C 執行階段。 靜態註冊用於裝置組建。

### <a name="categories"></a>分類

從 Xamarin.iOS 8.10 開始，就可以建立使用 OBJECTIVE-C 類別C#語法。

若要建立類別目錄，使用`[Category]`屬性，指定要擴充的型別。 例如，下列程式碼擴充`NSString`:

```csharp
[Category (typeof (NSString))]
```

每個類別的方法有`[Export]`屬性，將它提供給 OBJECTIVE-C 執行階段：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有受管理的擴充方法必須是靜態的但是仍可以建立使用標準的 Objective C 執行個體方法C#擴充方法的語法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

擴充方法的第一個引數是叫用方法時所在的執行個體：

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

此範例會新增原生`toUpper`執行個體方法，以`NSString`類別。 可以呼叫這個方法，從目標 c:

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

與 Xamarin.iOS 8.10 從開始，介面`[Protocol]`屬性將會匯出到 OBJECTIVE-C 通訊協定為：

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

此程式碼會將匯出`IMyProtocol`Objective C 做為通訊協定來呼叫`MyProtocol`類別呼叫`MyClass`實作通訊協定。

## <a name="new-registration-system"></a>新的註冊系統

從穩定的 6.2.6 版和 beta 6.3.4 版中，我們已新增新的靜態註冊機構。 在 7.2.1 版本中，我們做了新的註冊機構的預設值。

這個新的註冊系統會提供下列新功能：

- 編譯時期偵測的程式設計錯誤：
    - 正在註冊具有相同名稱的兩個類別。
    - 匯出至回應相同的選取器的多個方法
- 移除未使用的原生程式碼：
    - 新的註冊系統會加入程式碼，用於靜態程式庫，可刪除未使用的原生程式碼產生的二進位檔從原生連結器的強式參考。 在 Xamarin 的範例繫結，大部分的應用程式變得較小至少 300 k。

- 支援的泛型子`NSObject`; 請參閱 < [NSObject 的泛型](~/ios/internals/api-design/nsobject-generics.md)如需詳細資訊。 此外在新的註冊系統會攔截不支援泛型建構先前會導致在執行階段隨機的行為。

### <a name="errors-caught-by-the-new-registrar"></a>新的註冊機構所攔截到的錯誤

以下是新的註冊機構所攔截到錯誤的一些範例。

- 匯出一次以上相同類別中相同的選取器：

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

- 匯出有同名的 Objective C 的多個 managed 的類別：

    ```csharp
    [Register ("Class")]
    class MyClass : NSObject {}

    [Register ("Class")]
    class YourClass : NSObject {}
    ```

- 匯出的泛型方法：

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo")]
        void Foo<T> () {}
    }
    ```

### <a name="limitations-of-the-new-registrar"></a>新的註冊機構的限制

新的註冊機構留意一些事項︰

- 某些協力廠商程式庫必須更新為使用新的註冊系統。 請參閱[需要修改](#required_modifications)下方以取得詳細資料。

- 短期的缺點也是如果會使用帳戶的架構，必須使用 Clang (這是因為 Apple **accounts.h**標頭只可以由 Clang)。 新增`--compiler:clang`其他 mtouch 引數，如果您使用 Xcode 4.6 或更早版本使用 Clang （Xamarin.iOS 會自動選取 Clang Xcode 5.0 或更新版本）。

- 如果 Xcode 4.6 （或更早版本），使用 GCC / G + + 必須選取 如果匯出型別名稱包含非 ASCII 字元 （這是因為 OBJECTIVE-C 程式碼中使用 Xcode 4.6 Clang 隨附的版本不支援識別碼內的非 ASCII 字元）。 新增`--compiler:gcc`使用 GCC 其他 mtouch 引數。

## <a name="selecting-a-registrar"></a>選取註冊機構

您可以藉由將下列選項的其中一個其他 mtouch 引數，在專案中選取不同的註冊機構**iOS 組建**設定：

- `--registrar:static` -針對裝置組建預設值
- `--registrar:dynamic` – 對於模擬器組建預設值

> [!NOTE]
> Xamarin 的傳統 API 支援其他選項這類`--registrar:legacystatic`和`--registrar:legacydynamic`。 不過，透過統一的 API 不支援這些選項。

## <a name="shortcomings-in-the-old-registration-system"></a>在舊的註冊系統的缺點

舊的註冊系統會有下列缺點：

- 沒有任何 （原生） 的靜態參考，OBJECTIVE-C 類別和方法，在第三方原生程式庫，這表示我們無法要求原生連結器移除實際上不是在 （因為會移除所有項目） 所使用的第三方原生程式碼。 這是基於`-force_load libNative.a`每個第三方繫結必須執行 (或同等權限`ForceLoad=true`在`[LinkWith]`屬性)。
- 您可以匯出具有兩個受管理的類型有同名的 Objective C 不發出警告。 罕見的案例是具有兩個得到`AppDelegate`不同命名空間中的類別。 在執行階段會完全隨機哪一個已挑選 （事實上，它執行的應用程式，即使未重建的-對非常令人感到困惑且令人沮喪的偵錯體驗之間各不相同）。
- 您可以匯出兩個具有相同的 Objective C 簽章的方法。 一次會從 OBJECTIVE-C 呼叫的其中一個是隨機 （但這個問題不雖不如前一個位置，這多半是因為實際上會遇到這個錯誤的唯一方式是覆寫背受管理的方法）。
- 已匯出的方法組是動態和靜態組建之間稍有不同。
- 它無法正常運作時匯出泛型類別 （在執行階段中執行的確切的泛型實作會是隨機的有效地產生未定的行為）。

## <a name="new-registrar-required-changes-to-bindings"></a>新的註冊機構： 必要的繫結的變更

本章節描述繫結變更，必須變更才能使用新的註冊機構。

### <a name="protocols-must-have-the-protocol-attribute"></a>通訊協定必須具有 [Protocol] 屬性

現在必須具有通訊協定`[Protocol]`屬性。 如果不這麼做，您將這類會原生連結器錯誤：

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>選取器必須是有效的參數數目

所有的選取器必須正確指出參數的數目。 之前，這些錯誤，所以忽略，而且可能會造成執行階段問題。

簡單地說，冒號數目必須符合參數的數目：

- 沒有參數： `foo`
- 一個參數： `foo:`
- 兩個參數： `foo:parameterName2:`

以下是不正確的使用：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在匯出中使用 IsVariadic 參數

Variadic 函式必須使用`IsVariadic`引數`[Export]`屬性：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必須連結到現有的符號

就無法繫結不存在於原生程式庫中的類別。
如果已移除或在原生程式庫中重新命名的類別，請務必更新以符合繫結項目。
