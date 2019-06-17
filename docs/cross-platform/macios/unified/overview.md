---
title: 統一的 API 概觀
description: Xamarin 的統一 API 可讓您能夠 Mac 和 iOS 以及支援 32 位元及 64 位元應用程式具有相同的二進位檔之間共用程式碼。
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261125"
---
# <a name="unified-api-overview"></a>統一的 API 概觀

Xamarin 的統一 API 可讓您能夠 Mac 和 iOS 以及支援 32 位元及 64 位元應用程式具有相同的二進位檔之間共用程式碼。 在新的 Xamarin.iOS 和 Xamarin.Mac 專案中的預設會使用統一的 API。

> [!IMPORTANT]
> Xamarin 傳統 API，前面有統一的 API，已被取代。 
> - Xamarin.iOS 支援傳統的 API (monotouch.dll) 的最後一個版本是 Xamarin.iOS 9.10。
> - Xamarin.Mac 仍支援傳統的 API，但它不會再更新。 因為它已被取代，開發人員應該將移至統一的 API 應用程式。

## <a name="updating-classic-api-based-apps"></a>更新傳統 API 為基礎的應用程式

請遵循您的平台的相關指示：

- [更新現有的應用程式](updating-apps.md)
- [更新現有的 iOS 應用程式](updating-ios-apps.md)
- [更新現有的 Mac 應用程式](updating-mac-apps.md)
- [更新現有 Xamarin.Forms 應用程式](updating-xamarin-forms-apps.md)
- [將繫結移轉至 Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[將程式碼更新至 Unified API 的祕訣](updating-tips.md)

無論何種應用程式，您要移轉，請參閱[祕訣](updating-tips.md)來協助您已成功更新至 Unified API。

## <a name="library-split"></a>程式庫分割

從這裡開始，我們的 Api 會呈現兩種方式：

-  **傳統的 API:** 限制為 32 位元 （僅限），並公開`monotouch.dll`和`XamMac.dll`組件。
-  **統一的 API:** 支援 32 和 64 位元開發，以單一 API 中可用`Xamarin.iOS.dll`和`Xamarin.Mac.dll`組件。

這表示，為企業開發人員 （不為目標的應用程式存放區），您可以繼續使用現有的傳統 Api，因為會保留將我們維護它們，或者您可以升級至新的 Api。

<a name="namespace-changes" />

## <a name="namespace-changes"></a>命名空間變更

若要減少摩擦 Mac 和 iOS 產品之間共用程式碼，我們會變更命名空間的產品中的 api。

我們會卸除前置詞"MonoTouch 「 與我們的 iOS 產品"MonoMac"Mac 產品上的資料類型。

這可讓您更容易而不需使用條件式編譯的 Mac 和 iOS 平台之間共用程式碼，並會減少雜訊，在您的原始程式碼檔的頂端。

-  **傳統的 API:** 使用命名空間`MonoTouch.`或`MonoMac.`前置詞。
-  **統一的 API:** 沒有命名空間前置詞

## <a name="runtime-defaults"></a>執行階段預設值

預設使用統一的 API **SGen**記憶體回收行程並[新參考計數](~/ios/internals/newrefcount.md)追蹤物件擁有權的系統。 這個相同的功能已經移植到 Xamarin.Mac。

這樣可以解決一些問題，開發人員面對舊系統，並簡化[記憶體管理](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

請注意，就可以啟用新的 Refcount 更傳統的 API，但預設值是保守，而且不需要使用者進行任何變更。 使用統一的 API 時，我們藉此機會變更預設的並讓開發人員所有的改良成果，同時它們重構，並重新測試他們的程式碼。

## <a name="api-changes"></a>API 變更

統一的 API 會移除已被取代的方法，並有少數的執行個體，有個 API 名稱中打錯字時就已繫結至原始的 MonoTouch 和 MonoMac 命名空間，在傳統的 Api。 這些執行個體已修正在新的統一的 Api，並將需要更新您的元件、 iOS 和 Mac 應用程式中。 以下是您可能會遇到最常見之威脅的清單：

|傳統的 API 方法名稱|統一的 API 方法名稱|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

從傳統模式切換至統一 API 時變更的完整清單，請參閱我們[傳統 (monotouch.dll) 與整合 (Xamarin.iOS.dll) API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)文件。

## <a name="updating-to-unified"></a>更新至整合

中的數個舊/中斷/已被取代 API**傳統**不適用於**整合**API。 它可以是您更輕鬆地修正`CS0616`警告啟動程式 （手動或自動） 之前升級，因為您必須`[Obsolete]`屬性訊息 （警告的一部分），指引您到正確的 API。

請注意，我們會發佈[ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)的傳統 vs 統一的 API 可用之前，或您的專案更新之後的變更。 還是修正 obsoletes 傳統會呼叫通常是節省時間 （較少的文件查閱）。

請遵循下列指示[更新現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)，或[Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)至統一的 API。
檢閱此頁面上的其餘部分並[祕訣](~/cross-platform/macios/unified/updating-tips.md)如需有關移轉您的程式碼的詳細資訊。

### <a name="nuget"></a>NuGet

先前支援透過傳統 API 的 Xamarin.iOS 的 NuGet 套件發行它們使用的組件**Monotouch10**平台的 moniker。

統一的 API 導入了新的平台識別項的相容套件- **Xamarin.iOS10**。 現有的 NuGet 套件必須更新以新增這個平台，支援針對統一的 API 建置的。

> [!IMPORTANT]
> 如果您在表單中有錯誤 _"錯誤 3 不能在相同的 Xamarin.iOS 專案中包含 'monotouch.dll' 和 'Xamarin.iOS.dll'-'monotouch.dll' 所參考時明確地參考 'Xamarin.iOS.dll' ' xxx，版本 = 0.0.000，文化特性 = 中性，PublicKeyToken = null'"_ 之後轉換至 Unified Api 的應用程式，它通常是因為有尚未更新至 Unified API 專案中的元件或 NuGet 套件。 您必須移除現有的元件/NuGet 更新為支援統一的 Api 版本，執行乾淨的組建。

### <a name="the-road-to-64-bits"></a>邁向 64 位元

如需支援 32 和 64 位元應用程式和架構的相關資訊的背景，請參閱[32 和 64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)。

 <a name="new-data-types" />

#### <a name="new-data-types"></a>新的資料類型

差異的核心，在 Mac 和 iOS Api 使用一律在 32 位元平台和 64 位元平台上的 64 位元的 32 位元架構特定資料型別。

Objective C 的對應，例如`NSInteger`資料類型`int32_t`32 位元系統上以及`int64_t`64 位元系統上。

比對這種行為，我們統一的 API，我們要取代先前使用`int`(在.NET 會定義為永遠`System.Int32`) 成新的資料類型： `System.nint`。  您可以想像"n"名的意義 「 原生 」，因此輸入平台的原生的整數。

我們引進`nint`，`nuint`和`nfloat`在必要時，也提供資料類型建立在其上。

若要深入了解這些資料類型變更，請參閱[原生類型](~/cross-platform/macios/nativetypes.md)文件。

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>如何偵測的 iOS 應用程式架構

可能有應用程式必須知道它正在執行 32 位元或 64 位元 iOS 系統上的情況。 下列程式碼可用來檢查架構：

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>陣列和 System.Collections.Generic

因為C#索引子會預期的型別`int`，您必須明確轉換`nint`值`int`來存取集合或陣列中的項目。 例如: 

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

這是預期的行為，因為從 cast`int`至`nint`是 64 位元上失真，隱含的轉換不是。

### <a name="converting-datetime-to-nsdate"></a>將 DateTime 轉換成 NSDate

使用統一的 Api，隱含轉換時才`DateTime`至`NSDate`值將不再執行。 這些值必須明確地轉換到另一種類型的。 下列擴充方法可以用來自動化此程序中：

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>已被取代的 Api 和錯字

在 Xamarin.iOS 傳統 API (monotouch.dll)`[Obsolete]`屬性使用方式有兩種：

-  **已被取代的 iOS API:** 這是當您停止使用 API，因為它已被取代被較新的 Apple 提示。 傳統的 API 是仍舊沒關係，通常需要 （如果您支援較舊 iOS 版本）。
 這類 API (和`[Obsolete]`屬性) 會納入新的 Xamarin.iOS 組件。
-  **不正確的 API**某些 API 的名稱有錯字。

原始的組件 （monotouch.dll 和 XamMac.dll） 中，我們保留舊的程式碼適用於相容性，但是已經移除從統一的 API 組件 （Xamarin.iOS.dll 和 Xamarin.Mac）

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject 子類別.ctor(IntPtr)

每隔`NSObject`子類別具有建構函式可接受`IntPtr`。 這是如何可以產生新的受控執行個體，從原生的 ObjC 控制代碼。

這是在傳統`public`建構函式。 不過，您很容易誤用使用者程式碼中的這項功能，例如建立數個受管理單一 ObjC 執行個體的執行個體*或*建立受控執行個體，則會缺少預期的受管理的狀態 （適用於子類別）。

若要避免這些問題的種類`IntPtr`建構函式現在是`protected`中**統一**API，來僅用於子類別化。 這可確保更正/安全 API 用來建立受控執行個體控制代碼，從，也就是

    var label = Runtime.GetNSObject<UILabel> (handle);

此 API 會傳回現有的受控執行個體 （如果已經存在） 或將建立一個新 （要求時）。 您已有傳統且統一的 API 中。

請注意，`.ctor(NSObjectFlag)`現在還有`protected`但外部子類別化很少使用這一個。

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction 取代動作

使用統一的 Api 中，`NSAction`都已由標準.NET `Action`。 這是一項重大進步，因為`Action`是一般的.NET 類型，而`NSAction`以前專門 xamarin.ios。 兩者都執行完全相同的動作，但不同和不相容的類型，且不必寫入來達成相同結果的更多程式碼所導致。

例如，如果您現有的 Xamarin 應用程式包含下列程式碼：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
現在可以使用簡單 lambda 取代：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

先前，就是編譯器錯誤因為`Action`無法指派給`NSAction`，但由於`UITapGestureRecognizer`現在採用`Action`而不是`NSAction`Unified Api 中有效。

### <a name="custom-delegates-replaced-with-actiont"></a>取代動作的自訂委派<T>

在 **統一**有些很簡單 （例如一個參數）.net 委派已被取代`Action<T>`。 例如，

    public delegate void NSNotificationHandler (NSNotification notification);

現在可用來當做`Action<NSNotification>`。 此升級程式碼重複使用，並減少 Xamarin.iOS 和自己的應用程式內的程式碼重複。

### <a name="taskbool-replaced-with-taskbooleannserror"></a>工作<bool>工作 < 布林值、 NSError >> 取代

在 **傳統**發生某些非同步 Api 傳回`Task<bool>`。 不過有些其中所要使用的時機`NSError`是簽章的一部分，也就是`bool`已經`true`且您必須攔截例外狀況，無法取得`NSError`。

因為有些錯誤是很常見，而不是很有用的傳回值已變更此模式**統一**返回`Task<Tuple<Boolean,NSError>>`。 這可讓您檢查成功，以及任何可能的非同步呼叫期間所發生的錯誤。

### <a name="nsstring-vs-string"></a>NSString vs 字串

從變更，在少數的情況下有一些常數`string`至`NSString`，例如 `UITableViewCell`

**傳統**

    public virtual string ReuseIdentifier { get; }

**整合**

    public virtual NSString ReuseIdentifier { get; }

一般我們偏好.NET`System.String`型別。 不過，儘管 Apple 指導方針，某些原生 API 會比較常數指標 （不本身是字串），而且這只能處理時，我們會公開做為常數`NSString`。

 <a name="protocols" />

### <a name="objective-c-protocols"></a>OBJECTIVE-C 通訊協定

原始 MonoTouch 沒有完整支援 ObjC 通訊協定，以及一些，未最佳化，API 已加入以支援最常見的案例。 這項限制不不再存在，但回溯相容性，多個 Api 會保留內`monotouch.dll`和`XamMac.dll`。

已移除這些限制並清除統一的 api。 大部分的變更將會如下所示：

**傳統**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**整合**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

`I`前置詞表示**統一**公開介面，而不是特定的類型，ObjC 通訊協定。 這讓您更容易，您不想要子類別 Xamarin.iOS 所提供的特定型別。

它也允許某些 API 更精確且容易使用，例如：

**傳統**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**整合**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

這類 API 現在更容易，而不會參考文件，而且 IDE 的程式碼完成功能會為您提供更有用的建議根據通訊協定/介面。

#### <a name="nscoding-protocol"></a>NSCoding 通訊協定

我們原始的繫結包含每一個型別-.ctor(NSCoder)，即使它不支援`NSCoding`通訊協定。  單一`Encode(NSCoder)`方法已存在於`NSObject`編碼物件。
但是，如果執行個體符合 NSCoding 通訊協定，此方法會只處理。

在統一的 API 上我們已修正此問題。  只會有新的組件`.ctor(NSCoder)`如果該類型符合`NSCoding`。 這類類型現在還有`Encode(NSCoder)`方法符合`INSCoding`介面。

低衝擊：在大部分情況下這項變更不會影響應用程式，因為無法使用舊的、 已移除，建構函式。

## <a name="further-tips"></a>進一步的提示

要注意的其他變更已列在[更新至 Unified API 的應用程式的祕訣](~/cross-platform/macios/unified/updating-tips.md)。

## <a name="sample-code"></a>程式碼範例

截至年 7 月 31 日，我們已發佈的這個新的 API iOS 範例的連接埠上`magic-types`在分支[monotouch 範例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

For Mac 中，我們會檢查範例中都[mac 範例](https://github.com/xamarin/mac-samples)存放庫 （Mavericks/Yosemite 中顯示新的 Api），以及 32/64 位元 magic 類型分支中的範例[mac 範例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

## <a name="related-links"></a>相關連結

- [更新 iOS 應用程式](updating-ios-apps.md)
- [更新 Mac 應用程式](updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](updating-xamarin-forms-apps.md)
- [更新繫結](update-binding.md)
- [更新祕訣](updating-tips.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
