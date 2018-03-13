---
title: "類型的註冊機構"
ms.topic: article
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 57ebffc2bd195a37240ab4fe3d397566a9a70bd0
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="type-registrar"></a>類型的註冊機構

本文件說明 Xamarin.iOS 所使用的型別註冊系統。

## <a name="registration-of-managed-classes-and-methods"></a>註冊 managed 的類別和方法

在啟動期間，將會註冊 Xamarin.iOS:

  - 類別與[[註冊]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) OBJECTIVE-C 類別當做屬性。
  - 類別與[[Category]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute) OBJECTIVE-C 類別當做屬性。
  - 介面為[[通訊協定]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/)視為 Objective C 的通訊協定的屬性。

在每個案例成員[[匯出]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)屬性匯出至目標。 這可讓受管理的類別，能夠建立和管理從 Objective C 呼叫的方法和方法和屬性 C# 全局和一個 OBJECTIVE-C 之間連結的方式。

一個非常簡單的範例是 AppDelegate 類別具有每個應用程式。 您可以回想受管理的 Main 方法具有類似以下的一行：

    UIApplication.Main (args, null, "AppDelegate");

這會告知 Objective C 執行階段為應用程式的委派類別建立稱為 「 AppDelegate"的類型。  Objective C 執行階段知道如何建立以 C# 撰寫的 「 AppDelegate"類別的執行個體，這個類別已註冊。

Xamarin.iOS 執行階段會負責的註冊，就內部而言，您可以完全在執行階段 （動態註冊） 進行此註冊，或可以透過在編譯時期 （靜態註冊）。  動態方法牽涉到在啟動時使用反映，來找出所有類別及註冊，並將那些 Objective C 執行階段傳遞的方法。  靜態方法會檢查應用程式在編譯時期所使用的組件。  它判斷的類別和方法，以向 Objective C，並產生對應的內嵌到您的二進位檔。  然後，在啟動時，我們向地圖 Objective C 執行階段。

### <a name="categories"></a>分類

啟動 Xamarin.iOS 8.10 與它有可能建立 OBJECTIVE-C 類別使用 C# 語法。

這是使用 [類別目錄] 屬性，指定要做為引數的屬性延伸的類型。
下列範例將執行個體擴充 NSString:

    [Category (typeof (NSString))]

每個類別方法使用標準機制來 Objective C 使用 [匯出] 屬性來匯出方法：

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

所有受管理的擴充方法必須是靜態，但您可建立 Objective C 的執行個體方法使用標準語法的 C# 中的擴充方法：

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

與擴充方法的第一個引數將叫用方法時所在的執行個體。

完整的範例：

    [Category (typeof (NSString))]
    public static class MyStringCategory
    {
        [Export ("toUpper")]
        static string ToUpper (this NSString self)
        {
            return self.ToString ().ToUpper ();
        }
    }

這個範例會將原生 toUpper 執行個體方法加入至 NSString 類別，可從目標 C.可以叫用

    [Category (typeof (UIViewController))]
    public static class MyViewControllerCategory
    {
        [Export ("shouldAutoRotate")]
        static bool GlobalRotate ()
        {
            return true;
        }
    }

### <a name="protocols"></a>通訊協定

開頭 Xamarin.iOS 8.10 介面具有 [通訊協定] 屬性會匯出到 Objective C 做為通訊協定。

範例：

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

這將做為一種通訊協定 (MyProtocol)，以及實作通訊協定的類別 (MyClass) 匯出至 Objective C。

 **動態註冊**

會用於模擬器組建，因為它加速組建/偵錯循環。  這是每次啟動應用程式，並改為使用一般用途啟動器每次產生類別對應和本表對應編譯至應用程式啟動器消除步驟的結果。  您的桌上型電腦有許多能力來執行執行階段類別的快速掃描，讓效能永遠不會造成問題。

 **靜態註冊**

適用於組建裝置行動裝置低於桌上型電腦，以及執行執行階段掃描很慢。  由於裝置組建一定要建立新的二進位檔，偵錯組建/循環不會受到註冊對應的建立。

## <a name="new-registration-system"></a>新的註冊系統

從穩定 6.2.6 我們已加入新的靜態註冊機構的 beta 6.3.4 版本。 在 7.2.1 版本我們進行新的註冊機構預設值。

這個新的註冊系統提供下列新功能：

- 編譯階段偵測程式設計錯誤：
    - 正在註冊具有相同名稱的兩個類別。
    - 匯出至回應相同的選取器的多個方法



- 可以移除未使用的原生程式碼
    - 新的註冊系統將強式參考程式碼的使用中的靜態程式庫，可讓原生的連結器，以刪除未使用的原生程式碼將產生的二進位檔。
      在 Xamarin 的範例的繫結，大部分的應用程式變得較小至少 300 k。

- 支援的 NSObject 泛型子類別。 請參閱[NSObject 泛型](~/ios/internals/api-design/nsobject-generics.md)如需詳細資訊。 此外在新的註冊系統將會攔截先前可能已造成隨機的行為在執行階段不支援泛型建構。

這些是新 registar 攔截到錯誤的一些範例：

匯出一次以上相同類別中相同的選取器。

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo:")]
    void Foo (NSString str);
    [Export ("foo:")]
    void Foo (string str)
}
```

匯出以 OBJECTIVE-C 同名的多個 managed 的類別。

```csharp
[Register ("Class")]
class MyClass : NSObject {}

[Register ("Class")]
class YourClass : NSObject {}
```

匯出的泛型方法。

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo")]
    void Foo<T> () {}
}
```



要記住關於新的註冊機構的一些事項：
- 某些協力廠商文件庫必須更新為使用新的註冊系統，請參閱節[需要修改以下](#required_modifications)如需詳細資訊。
- 短期缺點是，也會使用帳戶架構，，必須使用 Clang （這是因為 Clang 只可以編譯 Apple accounts.h 標頭）。 新增<code>--compiler:clang</code>其他 mtouch 引數使用 Clang，如果您正在使用 Xcode 4.6 或更早版本 （Xamarin.iOS 會自動選取 Clang Xcode 5.0 版或更新版本）。

    <li>如果 Xcode 4.6 （或更早版本） 上使用，GCC / G + + 中必須選取 如果匯出型別名稱包含非 ascii 字元 （這是因為 Objective C 程式碼中使用 Xcode 4.6 Clang 隨附的版本不支援非 ascii 字元在識別項）。 新增<code>--compiler:gcc</code>用於 GCC 的額外 mtouch 引數。


## <a name="selecting-a-registrar"></a>選取註冊機構

您可以藉由新增下列選項的其中一個其他 mtouch 引數，在專案的 iOS 建置選項中選取不同的註冊機構：

-  `--registrar:static` ： 建立裝置的預設值
-  `--registrar:dynamic` ： 建置模擬器的預設值
-  `--registrar:legacystatic` ： 建置 Xamarin.iOS 7.2.1 直到的裝置的預設值
-  `--registrar:legacydynamic` ： 模擬器建置 Xamarin.iOS 7.2.1 之前的預設值


## <a name="shortcomings-in-the-old-registration-system"></a>舊的註冊系統的缺點

舊的註冊系統有下列缺點：

-  沒有任何 （原生） 的靜態參考 OBJECTIVE-C 類別和方法在第三方原生程式庫，即代表我們無法要求原生的連結器，以移除實際上不是使用 （因為會移除所有項目） 的第三方原生程式碼。 這是原因 」-force_load libNative.a"所有協力廠商的繫結都只需要 (或對等 ForceLoad = true [LinkWith] 屬性中的)。
-  您無法匯出兩個 managed 型別具有相同 Objective C 的名稱，但沒有出現警告。 罕見案例就是最後兩個 AppDelegate 類別 （在不同的命名空間）。 在執行階段就會完全隨機哪一個已轉 （事實上，它執行的應用程式，即使未重建-成為很令人感到困惑和令人沮喪的偵錯經驗之間各不相同）。
-  您無法匯出相同的簽章 Objective C 的兩種方法。 一次會從 Objective C 呼叫哪一個是隨機 （但大部分因為實際發生此錯誤的唯一方式是覆寫不幸的 managed 的方法，這個問題未與先前，那麼常見）。
-  匯出的方法集合已動態和靜態組建之間稍有不同。
-  它無法正常運作時匯出泛型類別 （在執行階段執行的確切的一般實作會是隨機的有效地產生未定的行為）。


 <a name="required_modifications" />


## <a name="new-registrar-required-changes-to-bindings"></a>新的註冊機構： 所需的變更繫結


可能需要更新為使用新 registar 現有 OBJECTIVE-C 繫結。

以下是需要進行的變更清單。

### <a name="protocols-must-have-the-protocol-attribute"></a>通訊協定必須要有 [通訊協定] 屬性

通訊協定實作，現在必須具有 [通訊協定] 屬性套用至它們。  如果不這麼做，您會將這類的原生的連結器錯誤：

```csharp
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>選取器數必須是有效的參數

所有的選取器必須正確指出參數的數目。  之前，這些錯誤會略過，而且可能會造成執行階段問題。

簡單地說，冒號的數目必須符合參數的數目。

例如: 

-  沒有參數: 'foo'
-  一個參數: ' foo:'
-  兩個參數: ' foo:parameterName2:'


不正確的用法如下：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在匯出中使用 IsVariadic 參數

Variadic 函式必須假設，在它們的匯出屬性中 （這是因為選取器是關於引數數目不正確，也就是第 2 點。 從上方違反）：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必須連結到現有的符號

就無法繫結不存在於原生程式庫中的類別。

如果您嘗試繫結不存在的類別，您會收到原生連結器錯誤。

當繫結已存在於段時間，而原生程式碼已在這段時間，讓特定的原生類別已移除或重新命名，而不更新繫結時，就會發生這種情形。

