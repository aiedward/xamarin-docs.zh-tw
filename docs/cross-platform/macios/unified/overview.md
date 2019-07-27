---
title: Unified API 總覽
description: Xamarin 的 Unified API 可讓您在 Mac 和 iOS 之間共用程式碼, 並以相同的二進位檔支援32和64位應用程式。
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 9d36101c1416ea8ddf451f5677258972c4f34990
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511137"
---
# <a name="unified-api-overview"></a>Unified API 總覽

Xamarin 的 Unified API 可讓您在 Mac 和 iOS 之間共用程式碼, 並以相同的二進位檔支援32和64位應用程式。 預設會在新的 Xamarin. iOS 和 Xamarin 專案中使用 Unified API。

> [!IMPORTANT]
> 在 Unified API 之前的 Xamarin Classic API 已被取代。 
> - 支援 Classic API (monotouch) 的最新版本是 Xamarin. iOS 9.10。
> - Xamarin 仍然支援 Classic API, 但不會再更新。 由於它已被取代, 因此開發人員應該將他們的應用程式移至 Unified API。

## <a name="updating-classic-api-based-apps"></a>更新以 Classic API 為基礎的應用程式

遵循您平臺的相關指示:

- [更新現有的應用程式](updating-apps.md)
- [更新現有的 iOS 應用程式](updating-ios-apps.md)
- [更新現有的 Mac 應用程式](updating-mac-apps.md)
- [更新現有 Xamarin.Forms 應用程式](updating-xamarin-forms-apps.md)
- [將繫結移轉至 Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[將程式碼更新至 Unified API 的祕訣](updating-tips.md)

不論您要遷移的應用程式為何, 請查看[這些秘訣](updating-tips.md), 以協助您成功更新 Unified API。

## <a name="library-split"></a>程式庫分割

從這裡開始, 我們的 Api 會以兩種方式呈現:

-  **Classic API:** 限制為32位 (僅限), 並在`monotouch.dll`和`XamMac.dll`元件中公開。
-  **Unified API:** 使用`Xamarin.iOS.dll` 和`Xamarin.Mac.dll`元件中提供的單一 API, 同時支援32和64位開發。

這表示對於企業開發人員 (不瞄準 App Store), 您可以繼續使用現有的傳統 Api, 因為我們會持續保留它們, 或者您可以升級至新的 Api。

<a name="namespace-changes" />

## <a name="namespace-changes"></a>命名空間變更

為了減少在 Mac 和 iOS 產品之間共用程式碼的摩擦, 我們將變更產品中 Api 的命名空間。

我們會從我們的 iOS 產品中卸載前置詞 "MonoTouch", 並從我們的 Mac 產品中的資料類型中刪除 "MonoMac"。

這可讓您更輕鬆地在 Mac 和 iOS 平臺之間共用程式碼, 而不需要進行條件式編譯, 並且會減少原始程式碼檔案頂端的雜訊。

-  **Classic API:** 命名空間`MonoTouch.`使用`MonoMac.`或前置詞。
-  **Unified API:** 沒有命名空間前置詞

## <a name="runtime-defaults"></a>執行時間預設值

Unified API 預設會使用**SGen**垃圾收集行程和新的[參考計數](~/ios/internals/newrefcount.md)系統來追蹤物件擁有權。 這項功能已移植到 Xamarin. Mac。

這解決了開發人員在舊系統上面臨的許多問題, 同時也可簡化[記憶體管理](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

請注意, 即使是 Classic API 也可以啟用新的 Refcount, 但是預設值很保守, 而且不需要使用者進行任何變更。 有了 Unified API, 我們就有機會變更預設值, 同時為開發人員提供在重構和重新測試其程式碼時的所有改進。

## <a name="api-changes"></a>API 變更

Unified API 會移除已淘汰的方法, 而且有一些實例會在 API 名稱系結至傳統 Api 中的原始 MonoTouch 和 MonoMac 命名空間時, 出現錯誤的情況。 這些實例已經在新的整合 Api 中更正, 必須在您的元件、iOS 和 Mac 應用程式中更新。 以下是您可能會遇到的最常見的清單:

|Classic API 方法名稱|Unified API 方法名稱|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

如需從傳統切換到 Unified API 時的完整變更清單, 請參閱我們的[傳統 (monotouch) 與整合 (Xamarin) API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)檔。

## <a name="updating-to-unified"></a>更新為統一的

在**傳統**中, 已有數個舊/已中斷/已淘汰的 api 無法用於**整合**api。 在啟動您的 (手動或`CS0616`自動) 升級之前, 您可以更輕鬆地修正警告, 因為您`[Obsolete]`會有屬性訊息 (警告的一部分) 引導您到正確的 API。

請注意, 我們發佈的是傳統與統一 API 變更的[*差異*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md), 可以在專案更新之前或之後使用。 仍然修正傳統中的 obsoletes 呼叫通常會是時間保護 (較少的檔查閱)。

請遵循這些指示, 將[現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)或[Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)更新為 Unified API。
請參閱此頁面的其餘部分, 以及[這些提示](~/cross-platform/macios/unified/updating-tips.md), 以取得有關遷移程式碼的其他資訊。

### <a name="nuget"></a>NuGet

先前透過 Classic API 支援 Xamarin 的 NuGet 套件會使用**Monotouch10**平臺標記來發行其元件。

Unified API 為相容的套件導入了新的平臺識別碼- **Xamarin. iOS10**。 您必須更新現有的 NuGet 套件, 以新增對此平臺的支援, 方法是針對 Unified API 進行建立。

> [!IMPORTANT]
> 如果您的錯誤格式為「_錯誤3不能在相同的 Xamarin 中同時包含 ' monotouch ' 和 ' xamarin ', 則會明確參考 ' monotouch ', 而 ' ' 則會參考 ' xxx, Version = 0.0.000, Culture =中性, PublicKeyToken = null ' "_ 將您的應用程式轉換成統一的 api 之後, 通常是因為專案中的元件或 NuGet 套件尚未更新為 Unified API。 您必須移除現有的元件/NuGet、更新為支援統一 Api 的版本, 並執行全新的組建。

### <a name="the-road-to-64-bits"></a>64位的道路

如需支援32和64位應用程式的背景, 以及架構的相關資訊, 請參閱[32 和64位平臺考慮](~/cross-platform/macios/32-and-64/index.md)。

 <a name="new-data-types" />

#### <a name="new-data-types"></a>新的資料類型

就差異的核心而言, Mac 和 iOS Api 都會使用在32位平臺上一律為32位的架構專屬資料類型, 以及在64位平臺上的64位。

例如, 目標-C 會將`NSInteger`資料類型對應到`int32_t` `int64_t` 32 位系統上的, 以及64位系統上的。

為了符合此行為, 我們的 Unified API 上, 我們會將先前的`int`使用 (在 .net 中定義為`System.Int32`always as) 取代為新的資料類型: `System.nint`。  您可以將 "n" 視為意義「原生」, 因此是平臺的原生整數類型。

我們正引進`nint`, `nuint`並`nfloat`在必要時, 也提供以其為基礎的資料類型。

若要深入瞭解這些資料類型的變更, 請參閱[原生類型](~/cross-platform/macios/nativetypes.md)檔。

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>如何偵測 iOS 應用程式的架構

在某些情況下, 您的應用程式可能需要知道它是在32位或64位的 iOS 系統上執行。 下列程式碼可以用來檢查架構:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>陣列和 System.object

因為C#索引子預期的類型`int`, 所以您必須明確地將`nint`值轉換`int`為, 才能存取集合或陣列中的元素。 例如：

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

這是預期的行為, 因為從`int`轉換成`nint`會在64位上遺失, 因此不會執行隱含轉換。

### <a name="converting-datetime-to-nsdate"></a>將 DateTime 轉換成 NSDate

使用整合 api 時, 不會再執行`DateTime`對`NSDate`值的隱含轉換。 這些值必須明確轉換成另一種類型。 您可以使用下列擴充方法來自動化此程式:

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

### <a name="deprecated-apis-and-typos"></a>已淘汰的 Api 和錯誤

在 Xamarin 中, 會以兩種不同的`[Obsolete]`方式使用屬性:

-  **已淘汰的 iOS API:** 這是當您要停止使用 API 的 Apple 提示, 因為它是由較新的應用程式所取代。 Classic API 仍然正常, 而且通常是必要的 (如果您支援舊版的 iOS)。
 這類 API (和`[Obsolete]`屬性) 會包含在新的 Xamarin iOS 元件中。
-  **不正確的 API**有些 API 的名稱有錯誤。

針對原始元件 (monotouch .dll 和 XamMac), 我們將舊版程式碼保持為可供相容, 但已從 Unified API 元件 (Xamarin. .dll 和 Xamarin) 中移除。

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject 子類別 .ctor (IntPtr)

每`NSObject`個子類別都有一個可`IntPtr`接受的函式。 這就是我們可以從原生 ObjC 控制碼將新的受控實例具現化的方式。

在傳統中, 這`public`是一個函式。 不過, 在使用者程式碼中誤用這項功能很容易, 例如, 為單一 ObjC 實例建立數個受控實例,*或*建立不需要預期受控狀態的受控實例 (適用于子類別)。

為了避免這類問題`IntPtr` , 這些函式現在`protected`位於**整合**API 中, 僅用於子類別化。 這可確保使用正確/安全的 API, 從控制碼建立受控實例, 亦即

    var label = Runtime.GetNSObject<UILabel> (handle);

此 API 會傳回現有的受控實例 (如果它已經存在), 或將建立一個新的 (如有需要)。 這項功能已在傳統和統一的 API 中提供。

請注意, 現在也`protected` 是,但這種情況很少用於子類別化以外。`.ctor(NSObjectFlag)`

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction 已取代為 Action

透過統一的 api, `NSAction`已移除, 以改用標準的 .net。 `Action` 這是很大的改進`Action` , 因為是常見的 .net 類型`NSAction` , 而專屬於 Xamarin. iOS。 它們會執行完全相同的動作, 但它們是不同且不相容的類型, 因此需要撰寫更多程式碼才能達到相同的結果。

例如, 如果您現有的 Xamarin 應用程式包含下列程式碼:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
現在可以使用簡單的 lambda 來取代它:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

先前這會是`Action`編譯器錯誤`NSAction`, 因為無法指派給, `Action`但因為`UITapGestureRecognizer`現在會接受, 而不`NSAction`是它在統一 api 中是有效的。

### <a name="custom-delegates-replaced-with-actiont"></a>以 Action 取代的自訂委派<T>

在**統一**的部分簡單 (例如一個參數) 中, .net 委派已`Action<T>`取代為。 例如，

    public delegate void NSNotificationHandler (NSNotification notification);

現在可以當做來使用`Action<NSNotification>`。 這會提升程式碼重複使用, 並減少在 Xamarin 和您自己的應用程式內重複的程式碼。

### <a name="taskbool-replaced-with-taskbooleannserror"></a>工作<bool>已取代為工作 < 布林值, NSError > >

在**傳統**中`Task<bool>`, 有一些非同步 api 會傳回。 不過, 其中有些是簽章的一部分時`NSError`要使用的, 也就是說`bool` , 已經`true`是, 而您必須攔截例外狀況才能取得`NSError`。

由於某些錯誤很常見, 而且傳回值並不實用, 因此此模式在**統一**中已變更, `Task<Tuple<Boolean,NSError>>`以傳回。 這可讓您同時檢查是否成功, 以及在非同步呼叫期間可能發生的任何錯誤。

### <a name="nsstring-vs-string"></a>NSString vs string

在少數情況下, 某些常數必須從`string`變更為`NSString`, 例如`UITableViewCell`

**傳統**

    public virtual string ReuseIdentifier { get; }

**整合**

    public virtual NSString ReuseIdentifier { get; }

一般來說, 我們偏好使用 .net `System.String`型別。 不過, 儘管 Apple 方針, 某些原生 API 會比較常數指標 (而不是字串本身), 而且只有在將常數公開為`NSString`時才能使用。

 <a name="protocols" />

### <a name="objective-c-protocols"></a>目標-C 通訊協定

原始的 MonoTouch 並沒有完整的 ObjC 通訊協定支援, 而且部分、非最佳的 API 已新增來支援最常見的案例。 這項限制已不再存在, 但為了回溯相容性, 會在和`monotouch.dll` `XamMac.dll`中保留數個 api。

這些限制已移除, 並已在統一的 Api 上清除。 大部分的變更看起來像這樣:

**傳統**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**整合**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

前置詞表示 ObjC 通訊協定的整合會公開介面, 而不是特定類型。  `I` 當您不想要將所提供之特定類型的子類別化時, 這會很容易。

它也允許某些 API 更精確且便於使用, 例如:

**傳統**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**整合**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

這類 API 現在可讓我們更輕鬆地使用, 而不需參考檔, 而您的 IDE 程式碼完成將會根據通訊協定/介面提供更實用的建議。

#### <a name="nscoding-protocol"></a>NSCoding 通訊協定

我們的原始系結包含每種類型的 .ctor (NSCoder), 即使它不支援此`NSCoding`通訊協定。  中出現`Encode(NSCoder)`單一方法,以將物件編碼。`NSObject`
但是, 只有當實例符合 NSCoding 通訊協定時, 這個方法才可行。

在 Unified API 我們已修正此問題。  只有在類型`.ctor(NSCoder)` `NSCoding`符合時, 新元件才會有。 此外, 這類類型現在`Encode(NSCoder)`具有符合`INSCoding`介面的方法。

影響度低:在大部分情況下, 這項變更不會影響應用程式, 因為舊的、已移除的、無法使用的函式。

## <a name="further-tips"></a>進一步的秘訣

要注意的其他變更會列在將[應用程式更新至 Unified API 的秘訣](~/cross-platform/macios/unified/updating-tips.md)中。

## <a name="sample-code"></a>程式碼範例

自7月31日起, 我們已在`magic-types` [monotouch](https://github.com/xamarin/monotouch-samples/commits/magic-types)的分支上, 將 iOS 範例的埠發佈到這個新的 API。

針對 Mac, 我們會檢查[mac 範例](https://github.com/xamarin/mac-samples)存放庫 (在 Mavericks/Yosemite 中顯示新的 api) 的範例, 以及神奇類型分支[Mac 範例](https://github.com/xamarin/monotouch-samples/commits/magic-types)中的32/64 位範例。

## <a name="related-links"></a>相關連結

- [更新 iOS 應用程式](updating-ios-apps.md)
- [更新 Mac 應用程式](updating-mac-apps.md)
- [更新 Xamarin. Forms 應用程式](updating-xamarin-forms-apps.md)
- [更新系結](update-binding.md)
- [更新秘訣](updating-tips.md)
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
