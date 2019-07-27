---
title: 目標 Sharpie 發行歷程記錄
description: 本檔說明目標 Sharpie 的發行歷程記錄, 這是用來自動建立C#目標 C 程式碼系結的工具。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 86c9f46064b66dc31f805e830309cb061a78a2c8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509641"
---
# <a name="objective-sharpie-release-history"></a>目標 Sharpie 發行歷程記錄

## <a name="34-october-11-2017"></a>3.4 (2017 年10月11日)

[下載 v 3.4。0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支援 Xcode 9: iOS 11、macOS 10.13、tvOS 11 和 watchOS 4
* 現在應已修正 SIMD 和 tgmath.h> 的問題
* 已完全移除遙測

## <a name="33-august-3-2016"></a>3.3 (2016 年8月3日)

[下載 v 3.3。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 支援 Xcode 8 Beta 4、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。
* 已更新為最新的 Clang 主要組建 (2016-08-02)
* 將[遙測提交選項](https://twitter.com/Symbiatch/status/760373403878559744)從`sharpie pod bind`保存`sharpie bind`到。

## <a name="32-june-14-2016"></a>3.2 (2016 年6月14日)

[下載 v 3.2。3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支援 Xcode 8 Beta 1、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 (5 月31日, 2016)

[下載 v 3.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* 支援 CocoaPods 1。0
* 藉由先建立原生`.framework`然後系結, 來改善 CocoaPods 系結的可靠性
* 將 CocoaPods `.framework`和系結定義複製`Binding`到目錄中, 以便更輕鬆地與 xamarin 和 xamarin 連結專案進行整合

## <a name="30-october-5-2015"></a>3.0 (2015 年10月5日)

[下載 v 3.0。8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 支援新的目標 C 語言功能, 包括輕量泛型和 null 屬性 (如 Xcode 7 中所引進)
* 支援最新的 iOS 和 Mac Sdk。
* Xcode 專案建立和剖析支援! 您現在可以將完整的 Xcode 專案傳遞至目標 Sharpie, 它會盡力找出正確的做法 (例如`sharpie bind Project.xcodeproj -sdk ios`)。
* [CocoaPods](https://cocoapods.org)支援! 您現在可以直接從目標 Sharpie (例如`sharpie pod init ios AFNetworking && sharpie pod bind`) 設定、建立和系結 CocoaPods。
* 系結架構時, 如果架構支援 Clang 模組, 就會啟用它, 因此會產生最正確的架構剖析, 因為架構的結構是由其`module.modulemap`所定義。
* 若為建立架構產品的 Xcode 專案, 請將該產品 (而不是中繼產品目標) 剖析為 Xcode 專案中的非架構目標, 可能仍會有無法自動解決的多義性。

## <a name="216-march-17-2015"></a>2.1.6 (2015 年3月17日)

[下載 v 2.1。6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 已修正二元運算子運算式系結: 運算式的左邊未正確地與右手交換 (例如`1 << 0` , 不正確地系結為`0 << 1`)。 感謝 Adam Kemp 注意這一點!
* 已`NSInteger`修正和`nint` `uint` `nuint`在 i386 上系結`int`為和, 而不是和的問題。 `NSUInteger`現在會傳遞至 Clang, 以便在 i386 上以預期的方式進行剖析`objc/NSObjCRuntime.h`。 `-DNS_BUILD_32_LIKE_64`
* Mac OS X sdk 的預設架構 (例如`-sdk macosx10.10`) 現在是 x86_64, 而不是 i386, 因此`-arch`除非您想要覆寫預設值, 否則可以省略。

## <a name="210-march-15-2015"></a>2.1.0 (2015 年3月15日)

[下載 v 2.1。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849):請`using ObjCRuntime;`確定在使用`ArgumentSemantic`時產生。
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850):請`using System.Runtime.InteropServices;`確定在使用`DllImport`時產生。
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852):預設`DllImport`為載入`__Internal`符號。
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848):略過向前宣告的目標-C 容器宣告。
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846):將具有單一限定性的通訊協定類型系結`id<Foo>`為`Foo`具體介面`Foundation.NSObject<Foo>`(而非)。
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037):`UInt64` `Int32` `u` `Int32`將、和`uL`常值系結為, 以便在可以安全地納入時, 卸載和/或尾碼。 `Int64` `UInt32`
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038):修正原始原生名稱以`k`前置詞開頭時的列舉名稱對應。
* `sizeof`如果 C 運算式的引數類型未對應至C#基本型別, 則會在 Clang 中評估並系結為整數常值, C#以避免產生不正確。
* 修正類型為區塊之屬性的目標-C 語法 (目標-C 程式碼會出現在上述系結宣告的批註中)。
* 將一旦產生蛻變類型系結為其原始`int[]`類型 ( `int*`在 Clang 中進行語義分析時 decays 至, 但`int[]`改為將它系結為原始的)。

感謝您 Dave Dunkin, 以回報此點發行中已修正的許多 bug!

## <a name="200-march-9-2015"></a>2.0.02015年3月9日

[下載 v 2.0。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目標 Sharpie 2.0 是一種主要版本, 具備改良的 Clang 驅動程式和剖析器, 以及新的 NRefactory 架構系結引擎。 這些改良的元件提供更好的系結, 特別是關於型別系結。 許多其他的改良功能都是目標 Sharpie 的內部, 這會在未來的版本中產生許多使用者可見的功能。

目標 Sharpie 2.0 是以 Clang 3.6.1 為基礎。

### <a name="type-binding-improvements"></a>類型系結改善

* 現在支援目標-C 區塊。 這包括透過所命名`typedef`的匿名/內嵌區塊和區塊。 匿名區塊會系結為`System.Action`或`System.Func`委派, 而命名區塊則會系結為強`delegate`式命名的型別。

* 已改善匿名列舉的命名啟發法, 緊接在`typedef`解析為內建整數類資料類型`long` (例如或`int`) 之前。

* C 指標現在會系結C# `unsafe`為指標, `System.IntPtr`而不是。 當您想要將指標參數轉換為`out`或`ref`參數時, 這會更清楚地系結中的。 不一定可以推斷參數是否應為`out`或`ref`, 因此指標會保留在系結中, 以允許更容易的審核。

* 上述指標系結的例外狀況是當目標-C 物件的2排名指標做為參數時。 在這些情況下, 慣例是主要的, 而參數會系`out`結為 ( `NSError **error`例如`out NSError error`→)。

### <a name="verify-attribute"></a>Verify 屬性

您通常會發現, 目標 Sharpie 所產生的系結現在會以`[Verify]`屬性標注。 這些屬性工作表示您應該藉由比較系結與原始的 C/目標-C 宣告 (將會在系結宣告上方的批註中提供), 來_驗證_目標 Sharpie 是否執行正確的動作。

_建議_對所有系結宣告進行驗證, 但最有可能_需要_針對以`[Verify]`屬性標注的宣告。 這是因為在許多情況下, 原始原生原始程式碼中沒有足夠的中繼資料來推斷如何最佳產生系結。 您可能需要參考標頭檔中的檔或程式碼批註, 以做出最佳的系結決策。

如需詳細資訊, 請參閱[驗證屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)檔。

### <a name="other-notable-improvements"></a>其他值得注意的改進

* `using`現在會根據系結的類型來產生語句。 例如, 如果`NSURL`已系結`using Foundation;` , 則也會產生語句。

* `struct`和`union`宣告現在會系結, 並`[FieldOffset]`使用等位的訣竅。

* 具有常數運算式初始化運算式的列舉值現在會正確地系結;完整運算式會轉譯為C#。

* 現在已系結 Variadic 方法和區塊。

* 現在可以透過`-framework`選項來支援架構。 如需詳細資訊, 請參閱關於系結[原生](~/cross-platform/macios/binding/objective-sharpie/index.md)架構的檔。

* 現在會自動偵測到目標-C 原始程式碼, 這應該不需要手動傳遞`-ObjC`或`-xobjective-c` Clang。

* 現在會自動偵測`@import`Clang 模組使用方式 (), 這應該不需要針對使用 Clang `-fmodules`中新模組支援的程式庫, 以手動方式傳遞至 Clang。

* Xamarin Unified API 現在是預設的系結目標。`-classic`使用選項, 僅以32位的 Classic API 為目標。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修正

* 修正`instancetype`在目標-C 類別中使用時的系結
* 完整名稱目標-C 類別
* 將目標 C 通訊協定`I`加上前置詞 ( `INSCopying`例如`NSCopying`, 而不是)

## <a name="1135-december-21-2014"></a>1.1.35:2014年12月21日

[下載 v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

次要錯誤修正。

## <a name="111-december-15-2014"></a>1.1.12014年12月15日

[下載1.1.1 版](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 是 2005 2013 年4月的目標 Sharpie 首次預覽1.5 之後, 在 Xamarin 內部使用和開發的第一個主要版本。 這一版的第一個是一般被視為穩定的版本, 可用於各式各樣的原生程式庫, 以新的 Clang 後端為特色。

