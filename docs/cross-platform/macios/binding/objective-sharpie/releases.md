---
title: 目標 Sharpie 發行歷程記錄
description: 本檔說明目標 Sharpie 的發行歷程記錄，這是用來自動建立C#目標 C 程式碼系結的工具。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 2f1fb3706012fa86834986064a366071b644b2dc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015959"
---
# <a name="objective-sharpie-release-history"></a>目標 Sharpie 發行歷程記錄

## <a name="34-october-11-2017"></a>3.4 （2017年10月11日）

[下載 v 3.4。0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支援 Xcode 9： iOS 11、macOS 10.13、tvOS 11 和 watchOS 4
* 現在應已修正 SIMD 和 tgmath.h> 的問題
* 已完全移除遙測

## <a name="33-august-3-2016"></a>3.3 （2016年8月3日）

[下載 v 3.3。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 支援 Xcode 8 Beta 4、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。
* 已更新為最新的 Clang 主要組建（2016-08-02）
* 將[遙測提交選項](https://twitter.com/Symbiatch/status/760373403878559744)從 `sharpie pod bind` 保存到 `sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 （2016年6月14日）

[下載 v 3.2。3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支援 Xcode 8 Beta 1、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 （5月31日，2016）

[下載 v 3.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* 支援 CocoaPods 1。0
* 藉由先建立原生 `.framework`，然後將其系結，來改善 CocoaPods 系結可靠性
* 將 CocoaPods `.framework` 和系結定義複製到 `Binding` 目錄中，以便更輕鬆地與 Xamarin 和 Xamarin 系結專案進行整合。

## <a name="30-october-5-2015"></a>3.0 （2015年10月5日）

[下載 v 3.0。8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 支援新的目標 C 語言功能，包括輕量泛型和 null 屬性（如 Xcode 7 中所引進）
* 支援最新的 iOS 和 Mac Sdk。
* Xcode 專案建立和剖析支援！ 您現在可以將完整的 Xcode 專案傳遞至目標 Sharpie，它會盡力找出正確的東西（例如 `sharpie bind Project.xcodeproj -sdk ios`）。
* [CocoaPods](https://cocoapods.org)支援！ 您現在可以直接從目標 Sharpie （例如 `sharpie pod init ios AFNetworking && sharpie pod bind`）來設定、建立和系結 CocoaPods。
* 系結架構時，如果架構支援 Clang 模組，就會啟用它，因此會產生最正確的架構剖析，因為 framework 的結構是由其 `module.modulemap`所定義。
* 若為建立架構產品的 Xcode 專案，請將該產品（而不是中繼產品目標）剖析為 Xcode 專案中的非架構目標，可能仍會有無法自動解決的多義性。

## <a name="216-march-17-2015"></a>2.1.6 （2015年3月17日）

[下載 v 2.1。6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 已修正二元運算子運算式系結：運算式的左邊未正確地與右手交換（例如，`1 << 0` 不正確地系結為 `0 << 1`）。 感謝 Adam Kemp 注意這一點！
* 已修正 `NSInteger` 和 `NSUInteger` 系結為 `int` 和 `uint`，而不是在 i386 上 `nint` 和 `nuint` 的問題。`-DNS_BUILD_32_LIKE_64` 現在會傳遞至 Clang，使剖析 `objc/NSObjCRuntime.h` 在 i386 上如預期般運作。
* Mac OS X Sdk 的預設架構（例如 `-sdk macosx10.10`）現在已 x86_64 而不是 i386，因此除非您想要覆寫預設值，否則會省略 `-arch`。

## <a name="210-march-15-2015"></a>2.1.0 （2015年3月15日）

[下載 v 2.1。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849)：確保在使用 `ArgumentSemantic` 時，會產生 `using ObjCRuntime;`。
* [bxc # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850)：確保在使用 `DllImport` 時，會產生 `using System.Runtime.InteropServices;`。
* [bxc # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852)：預設 `DllImport` 從 `__Internal`載入符號。
* [bxc # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848)：略過向前宣告的目標-C 容器宣告。
* [bxc # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846)：將具有單一限定性的通訊協定類型系結為具體介面（`id<Foo>` 為 `Foo`，而不是 `Foundation.NSObject<Foo>`）。
* [bxc # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037)：將 `UInt32`、`UInt64`和 `Int64` 常值系結為 `Int32`，以便在可以安全地納入 `u` 時，捨棄 `uL` 和/或 `Int32`尾碼。
* [bxc # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038)：修正原始原生名稱以 `k` 前置詞開頭時的列舉名稱對應。
* 若 `sizeof` C 運算式的引數類型未對應至C#基本型別，則會在 Clang 中評估並系結為整數常值， C#以避免產生不正確。
* 修正類型為區塊之屬性的目標-C 語法（目標-C 程式碼會出現在上述系結宣告的批註中）。
* 將一旦產生蛻變類型系結為其原始類型（`int[]` decays，以在 Clang 中的語義分析期間 `int*`，但改為將它系結為原始的寫 `int[]`。

感謝您 Dave Dunkin，以回報此點發行中已修正的許多 bug！

## <a name="200-march-9-2015"></a>2.0.0：2015年3月9日

[下載 v 2.0。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目標 Sharpie 2.0 是一種主要版本，具備改良的 Clang 驅動程式和剖析器，以及新的 NRefactory 架構系結引擎。 這些改良的元件提供_更好的_系結，特別是關於型別系結。 許多其他的改良功能都是目標 Sharpie 的內部，這會在未來的版本中產生許多使用者可見的功能。

目標 Sharpie 2.0 是以 Clang 3.6.1 為基礎。

### <a name="type-binding-improvements"></a>類型系結改善

* 現在支援目標-C 區塊。 這包括透過 `typedef`命名的匿名/內嵌區塊和區塊。 匿名區塊會系結為 `System.Action` 或 `System.Func` 委派，而命名區塊則會系結為強式名稱 `delegate` 類型。

* 已改善匿名列舉的命名啟發法，緊接在 `typedef` 解析為內建整數類資料類型（例如 `long` 或 `int`）。

* C 指標現在系結為C# `unsafe` 指標，而不是 `System.IntPtr`。 當您想要將指標參數轉換成 `out` 或 `ref` 參數時，這會更清楚地系結中的。 您無法一律推斷參數是否應 `out` 或 `ref`，因此指標會保留在系結中，以允許更容易的審核。

* 上述指標系結的例外狀況是當目標-C 物件的2排名指標做為參數時。 在這些情況下，慣例是主要的，而參數會系結為 `out` （例如 `NSError **error` → `out NSError error`）。

### <a name="verify-attribute"></a>Verify 屬性

您通常會發現，目標 Sharpie 所產生的系結現在會以 `[Verify]` 屬性來標注。 這些屬性工作表示您應該藉由比較系結與原始的 C/目標-C 宣告（將會在系結宣告上方的批註中提供），來_驗證_目標 Sharpie 是否執行正確的動作。

_建議_對所有系結宣告進行驗證，但最有可能_需要_針對以 `[Verify]` 屬性標注的宣告。 這是因為在許多情況下，原始原生原始程式碼中沒有足夠的中繼資料來推斷如何最佳產生系結。 您可能需要參考標頭檔中的檔或程式碼批註，以做出最佳的系結決策。

如需詳細資訊，請參閱[驗證屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)檔。

### <a name="other-notable-improvements"></a>其他值得注意的改進

* 現在會根據系結的類型來產生 `using` 語句。 例如，如果 `NSURL` 系結，則也會產生 `using Foundation;` 語句。

* `struct` 和 `union` 宣告現在會系結，並針對等位使用 `[FieldOffset]` 的技巧。

* 具有常數運算式初始化運算式的列舉值現在會正確地系結;完整運算式會轉譯為C#。

* 現在已系結 Variadic 方法和區塊。

* 現在透過 `-framework` 選項支援架構。 如需詳細資訊，請參閱關於系結[原生](~/cross-platform/macios/binding/objective-sharpie/index.md)架構的檔。

* 現在會自動偵測目標-C 原始程式碼，而不需要以手動方式將 `-ObjC` 或 `-xobjective-c` 傳遞給 Clang。

* 現在會自動偵測 Clang 模組使用方式（`@import`），這應該不需要針對使用 Clang 中新模組支援的程式庫，以手動方式將 `-fmodules` 傳遞給 Clang。

* Xamarin Unified API 現在是預設的系結目標。使用 [`-classic`] 選項僅以32位為目標，Classic API。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修正

* 修正在目標-C 類別中使用時的 `instancetype` 系結
* 完整名稱目標-C 類別
* 使用 `I` （例如 `INSCopying` 而非 `NSCopying`）前置詞目標-C 通訊協定

## <a name="1135-december-21-2014"></a>1.1.35：2014年12月21日

[下載 v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

次要錯誤修正。

## <a name="111-december-15-2014"></a>1.1.1：2014年12月15日

[下載1.1.1 版](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 是 2005 2013 年4月的目標 Sharpie 首次預覽1.5 之後，在 Xamarin 內部使用和開發的第一個主要版本。 這一版的第一個是一般被視為穩定的版本，可用於各式各樣的原生程式庫，以新的 Clang 後端為特色。
