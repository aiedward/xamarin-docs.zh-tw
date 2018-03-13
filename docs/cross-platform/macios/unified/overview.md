---
title: "統一的 API 概觀"
description: "新樣式應用程式開發介面會更容易比以往 Mac 和 iOS 以及可讓您支援具有相同 32 和 64 位元應用程式二進位之間共用程式碼。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 21245d741ff025cb8c2a680642ec0226369540cb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="unified-api-overview"></a>統一的 API 概觀

_新樣式應用程式開發介面會更容易比以往 Mac 和 iOS 以及可讓您支援具有相同 32 和 64 位元應用程式二進位之間共用程式碼。_

若要改善 Mac 和 iOS 之間共用的程式碼，並可讓開發人員有單一的程式碼基底的 32 和 64 位元，適用於早期 2015年我們引進了新的 API，稱為統一的 API Xamarin.iOS 和 Xamarin.Mac 產品中。

> [!IMPORTANT]
> **傳統的設定檔已被取代：** Xamarin.iOS 中加入新的平台時我們開始逐漸淘汰它們從傳統的設定檔 (monotouch.dll) 的功能。 例如，非 NRC （新 ref-計數） 選項已經移除。 NRC 一律已啟用所有整合的應用程式 （亦即非 NRC 從未選項），並沒有已知的問題。 未來版本將移除使用 Boehm 為記憶體回收行程的選項。 這也是整合的應用程式永遠不會使用的選項。 Xamarin.iOS 10.0 版的下一步 改為排定的傳統支援完整移除。

## <a name="ios"></a>iOS

`Xamarin.iOS.dll` Xamarin.iOS 8.6 所隨附的組件是我們**穩定且支援的第一個版本**iOS 的統一的 api。
預覽舊版統一的 API 是關閉但不是完全相容。

## <a name="mac"></a>Mac

`Xamarin.Mac.dll`穩定通道的 Xamarin.Mac 中的組件是我們**穩定且支援的第一個版本**mac 的統一的 api
預覽舊版統一的 API 是關閉但不是完全相容。

## <a name="runtime-defaults"></a>執行階段預設值

預設使用統一的 API **SGen**記憶體回收行程和[新參考計數](~/ios/internals/newrefcount.md)追蹤物件的擁有權的系統。 此相同功能已移植到 Xamarin.Mac。

這樣可以解決一些問題，開發人員面對舊的系統和也可簡化[記憶體管理](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

請注意，很可能甚至的傳統應用程式開發介面，啟用新的參考計數會保守的預設值，而且不需要使用者進行任何變更。 使用統一的 API 中，我們變更預設的機會，並同時讓開發人員所有改良他們重構和重新測試其程式碼。

<a name="namespace-changes" />

## <a name="library-split"></a>程式庫分割

從這裡開始，我們的 Api，會發生兩種方式：

-  **傳統應用程式開發介面：**限制為 32 位元 （僅限） 和中公開`monotouch.dll`和`XamMac.dll`組件。
-  **統一的 API:**支援使用單一 API 中可用的 32 和 64 位元開發`Xamarin.iOS.dll`和`Xamarin.Mac.dll`組件。

這表示，企業開發人員 （不以應用程式存放區），您可以繼續使用現有的傳統 Api，因為將會保留將我們維護它們，或者您可以升級至新的 Api。

### <a name="namespace-changes"></a>命名空間變更

若要減少摩擦到我們 Mac 和 iOS 的產品之間共用程式碼，我們會變更命名空間中之產品的 Api。

我們會卸除前置詞"MonoTouch 「 與我們的 iOS 產品"MonoMac"Mac 產品的資料類型上。

這可讓您更容易 Mac 和 iOS 平台，而不必條件式編譯之間共用程式碼，並會減少在您的原始程式碼檔的頂端雜訊。

-  **傳統應用程式開發介面：**命名空間使用`MonoTouch.`或`MonoMac.`前置詞。
-  **統一的 API:**沒有命名空間前置詞

### <a name="api-changes"></a>應用程式開發介面變更

統一的 API 中移除已被取代的方法，並有少數的執行個體的錯字時中沒有 API 名稱已繫結至傳統的應用程式開發介面中的原始 MonoTouch 和 MonoMac 命名空間。 這些執行個體已修正在新的統一的 Api，而且必須在您的元件、 iOS 和 Mac 應用程式中更新。 以下是您可能會遇到的最常見的清單：

<table width="100%" border="1">
<tr>
    <th>傳統應用程式開發介面方法名稱</th>
    <th>統一的 API 方法名稱</th>
</tr>
<tr>
    <td>UINavigationController.PushViewControllerAnimated()</td>
    <td>UINavigationController.PushViewController()</td>
</tr>
<tr>
    <td>UINavigationController.PopViewControllerAnimated()</td>
    <td>UINavigationController.PopViewController()</td>
</tr>
<tr>
    <td>CGContext.SetRGBFillColor()</td>
    <td>CGContext.SetFillColor()</td>
</tr>
<tr>
    <td>NetworkReachability.SetCallback()</td>
    <td>NetworkReachability.SetNotification()</td>
</tr>
<tr>
    <td>CGContext.SetShadowWithColor</td>
    <td>CGContext.SetShadow</td>
</tr>
<tr>
    <td>UIView.StringSize</td>
    <td>UIKit.UIStringDrawing.StringSize</td>
</tr>
</table>

從傳統切換到統一的 API 時變更的完整清單，請參閱我們[傳統 (monotouch.dll) 與整合 (Xamarin.iOS.dll) API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)文件。

### <a name="updating-to-unified"></a>更新至整合

中的數個舊/中斷/已被取代 API**傳統**不適用於**統一**應用程式開發介面。 很容易修正`CS0616`警告啟動程式 （手動或自動） 之前升級，因為您必須在`[Obsolete]`屬性右邊的應用程式開發介面引導您的訊息 （警告的一部分）。

請注意，我們會發佈[*差異*](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)在傳統的 vs 統一的 API 變更，您可用之前或之後您專案的更新。 仍在修正 obsoletes 傳統會在呼叫通常能節省時間 （較少的文件查閱）。

請遵循下列指示[更新現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)，或[Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)統一的 api。
檢閱此頁面上的其餘部分和[這些秘訣](~/cross-platform/macios/unified/updating-tips.md)如需有關移轉您的程式碼的詳細資訊。

## <a name="components-and-nuget"></a>元件和 NuGet

大部分現有的元件和 NuGet 套件，必須更新為支援統一的 API。
針對傳統的應用程式開發介面建置的元件不能從統一的 API 專案參考。

沒有任何參考的現有元件`monotouch.dll`(或`XamMac.dll`) 應該不需要更新。

### <a name="components"></a>元件

中的 iOS 元件[Xamarin 元件存放區](https://components.xamarin.com/)必須更新為使用統一的 API 專案。 Xamarin 正在更新元件，我們發行，並且鼓勵大家其他以相同的作者。

### <a name="nuget"></a>NuGet

先前支援透過傳統的 API Xamarin.iOS 的 NuGet 套件發行它們使用的組件**Monotouch10**平台 moniker。

統一的 API 導入了新的平台識別項的相容封裝- **Xamarin.iOS10**。 現有的 NuGet 封裝必須更新以新增支援此平台，建置針對統一的 API。

> [!IMPORTANT]
> **注意：**如果您在表單中有錯誤_"錯誤 3 不能在相同的 Xamarin.iOS 專案中包含 'monotouch.dll' 和 'Xamarin.iOS.dll'-'monotouch.dll' 正由時明確地參考 'Xamarin.iOS.dll' ' xxx，版本 = 0.0.000，Culture = neutral，PublicKeyToken = null'"_之後轉換您的應用程式，以統一的 Api，它通常是因為有尚未更新統一的 API 的專案中的元件或 NuGet 封裝。 您必須移除現有元件/NuGet、 更新為支援統一的 Api 版本，執行乾淨的組建。

<a name="deprecated-apis" />

## <a name="arrays-and-systemcollectionsgeneric"></a>陣列和 System.Collections.Generic

因為 C# 索引子會預期一種`int`，您必須明確轉換`nint`值`int`存取陣列或集合中的項目。 例如: 

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

這是預期的行為，因為從轉換`int`至`nint`是 64 位元上失真，隱含的轉換不是。

## <a name="converting-datetime-to-nsdate"></a>將 DateTime 轉換成 NSDate

使用統一的 Api，隱含的轉換時`DateTime`至`NSDate`值將不再執行。 這些值必須明確地轉換成另一種類型的。 下列擴充方法可以用來自動化此程序：

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

## <a name="deprecated-apis-and-typos"></a>已被取代的 Api 和錯字

內部 Xamarin.iOS 傳統應用程式開發介面 (monotouch.dll)`[Obsolete]`屬性使用方式有兩種：

-  **IOS 應用程式開發介面已被取代：** Apple 提示要您停止使用應用程式開發介面，因為已有另一個較所取代時。 傳統應用程式開發介面，而且仍然正常通常需要 （如果您支援較舊 iOS 版本）。
 這類應用程式開發介面 (和`[Obsolete]`屬性) 會併入新的 Xamarin.iOS 組件。
-  **不正確的 API**某些應用程式開發介面的主管姓名上有錯字。

原始組件 （monotouch.dll 和 XamMac.dll） 中，我們保留舊的程式碼相容性，但從統一的 API 組件 （Xamarin.iOS.dll 和 Xamarin.Mac） 已經移除

<a name="NSObject_ctor" />

## <a name="nsobject-subclasses-ctorintptr"></a>NSObject 子類別.ctor(IntPtr)

每個`NSObject`子類別具有建構函式可接受`IntPtr`。 這是我們可以產生原生的 ObjC 控制代碼從受管理的新執行個體的方式。

這是在傳統`public`建構函式。 不過，您很容易不當使用這項功能，使用者程式碼中的，例如，建立數個受管理單一 ObjC 執行個體的執行個體*或*建立受管理的執行個體，則會缺少預期的受管理的狀態 （適用於子類別）。

若要避免這些問題的種類`IntPtr`建構函式現在是`protected`中**統一**只用於子類別化應用程式開發介面。 這可確保更正/安全應用程式開發介面用來建立受管理的執行個體控制代碼，也就是

    var label = Runtime.GetNSObject<UILabel> (handle);

這個 API 會傳回現有的 managed 執行個體 （如果已經存在） 或將建立一個新 （要求時）。 它是已經用於傳統和統一的 API。

請注意，`.ctor(NSObjectFlag)`現在也是`protected`但外部子類別化很少使用這一個。

<a name="NSAction" />

## <a name="nsaction-replaced-with-action"></a>NSAction 取代動作

使用統一的 Api，`NSAction`移除了改用標準.NET `Action`。 這是因為大改進`Action`是一般的.NET 類型，而`NSAction`已特有 Xamarin.iOS。 兩者都執行完全相同的動作，但不同且不相容的型別和它們導致更多程式碼，不必撰寫，以達成相同結果。

例如，如果您現有的 Xamarin 應用程式包含下列程式碼：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
它現在可取代的簡單 lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

先前也就是編譯器錯誤因為`Action`無法指派給`NSAction`，但由於`UITapGestureRecognizer`現在採用`Action`而不是`NSAction`統一的 Api 中有效。

## <a name="custom-delegates-replaced-with-actiont"></a>取代動作的自訂委派<T>

在**統一**一些簡單 （例如一個參數） 所取代.net 委派`Action<T>`。 例如，

    public delegate void NSNotificationHandler (NSNotification notification);

現在可用來當作`Action<NSNotification>`。 此升級程式碼重複使用，並減少 Xamarin.iOS 和自己的應用程式內的程式碼重複。

## <a name="taskbool-replaced-with-taskbooleannserror"></a>工作<bool>工作 < 布林值、 NSError >> 取代

在**傳統**發生某些非同步 Api 傳回`Task<bool>`。 不過有些其中位置是時，用`NSError`已簽章的一部分，也就是`bool`已`true`，且您必須攔截例外狀況以取得`NSError`。

有些錯誤是很常見，並傳回值不是很有用，因為此模式已變更，**統一**傳回`Task<Tuple<Boolean,NSError>>`。 這可讓您檢查成功，無法在非同步呼叫期間發生任何錯誤。

## <a name="nsstring-vs-string"></a>NSString vs 字串

在少數情況下某些常數必須從變更`string`至`NSString`，例如 `UITableViewCell`

**Classic**

    public virtual string ReuseIdentifier { get; }

**整合**

    public virtual NSString ReuseIdentifier { get; }

我們通常最好.NET`System.String`型別。 不過，儘管 Apple 的指導方針，某些原生 API 會比較常數指標 （不字串本身），而且這只能搭配時，我們已公開做為常數`NSString`。

 <a name="protocols" />

## <a name="objective-c-protocols"></a>Objective C 的通訊協定

原始 MonoTouch 沒有完整支援 ObjC 通訊協定以及一些最佳做法，應用程式開發介面已加入以支援最常見的案例。 這項限制不不再存在，但回溯相容性，保留多個 Api 周圍內`monotouch.dll`和`XamMac.dll`。

這些限制已經移除，以及清除整合應用程式開發介面。 大部分的變更看起來像這樣：

**Classic**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**整合**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

`I`首碼表示**統一**公開的介面，而不是特定類型，ObjC 通訊協定。 這將可簡化情況下，您不希望子類別 Xamarin.iOS 所提供的特定型別。

它也允許某些 API 來為更精確且易於使用，例如：

**Classic**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**整合**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

這類應用程式開發介面現在更容易 us，而不參考文件集，而且 IDE 的程式碼完成功能將會提供更有用的建議根據通訊協定/介面。

### <a name="nscoding-protocol"></a>NSCoding 通訊協定

我們的原始繫結包含的每個類型-.ctor(NSCoder)，即使它不支援`NSCoding`通訊協定。  單一`Encode(NSCoder)`方法已存在於`NSObject`編碼物件。
但是這個方法只會運作，如果執行個體符合 NSCoding 通訊協定。

統一的 API 上我們已修正此問題。  只會有新的組件`.ctor(NSCoder)`如果類型符合`NSCoding`。 這種型別現在還有`Encode(NSCoder)`方法符合`INSCoding`介面。

影響很小： 在大部分情況下這項變更不會影響應用程式時無法使用的舊、 已移除，建構函式。

## <a name="further-tips"></a>進一步的提示

要注意的其他變更已列在[統一的 API 來更新應用程式的提示](~/cross-platform/macios/unified/updating-tips.md)。

## <a name="sample-code"></a>程式碼範例

自年 7 月 31 日，我們已發行的這個新的 API iOS 範例的連接埠上`magic-types`在分支[monotouch 範例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

適用於 Mac，我們會檢查範例中都[mac 範例](https://github.com/xamarin/mac-samples)（Mavericks/Yosemite 中顯示新的應用程式開發介面） 的儲存機制，以及 32/64 位元 magic 類型分支中的範例[mac 範例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

## <a name="related-links"></a>相關連結

- [更新的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新繫結](~/cross-platform/macios/unified/update-binding.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [更新的秘訣](~/cross-platform/macios/unified/updating-tips.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
