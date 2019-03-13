---
title: 目標 Sharpie 版本歷程記錄
description: 本文件說明目標 Sharpie，用來自動建立的工具版本記錄，C#繫結 OBJECTIVE-C 程式碼。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 03e4a5ac8906d2593cbdf3c15f6b2d1f4a2c6d19
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667253"
---
# <a name="objective-sharpie-release-history"></a>目標 Sharpie 版本歷程記錄

## <a name="34-october-11-2017"></a>3.4 (2017 年 10 月 11 日)

[下載 v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Xcode 9 的支援： iOS 11，macOS 10.13，tvOS 11 和 watchOS 4
* 現在應該修正 SIMD 與 tgmath 問題
* 已完全移除遙測

## <a name="33-august-3-2016"></a>3.3 (2016 年 8 月 3 日)

[下載 v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 支援 Xcode 8 Beta 4，iOS 10，macOS 10.12 tvOS 10 和 watchOS 3。
* 更新為最新 Clang 主要建置 (2016年-08-02)
* [保存遙測提交選項](https://twitter.com/Symbiatch/status/760373403878559744)從`sharpie pod bind`至`sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 (2016 年 6 月 14 日)

[下載 v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支援 Xcode 8 Beta 1、 iOS 10、 macOS 10.12、 tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 (2016 5 月 31 日)

[下載 v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* CocoaPods 1.0 支援
* 第一個建置原生改善 CocoaPods 繫結可靠性`.framework`和繫結，
* 複製 CocoaPods`.framework`和繫結定義`Binding`目錄，以便於使用 Xamarin.iOS 和 Xamarin.Mac 的繫結專案的整合

## <a name="30-october-5-2015"></a>3.0 (2015 年 10 月 5 日)

[下載 v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 支援新的 Objective C 語言功能，包括輕量級泛型和可 null 性，Xcode 7 中引進
* 適用於最新的 iOS 和 Mac Sdk 支援。
* 建立和剖析支援的 Xcode 專案 ！ 您現在可以傳遞完整的 Xcode 專案目標 Sharpie 來和它將會盡其所能找出執行正確的動作 (例如`sharpie bind Project.xcodeproj -sdk ios`)。
* [CocoaPods](https://cocoapods.org)支援 ！ 您現在可以設定、 建置，並直接從目標 Sharpie 繫結 CocoaPods (例如`sharpie pod init ios AFNetworking && sharpie pod bind`)。
* 如果架構支援，導致最正確地剖析的架構，因為架構的結構會定義由 Clang 模組時繫結架構，將會啟用其`module.modulemap`。
* 對於建置架構的產品的 Xcode 專案，請剖析該產品，而不是中繼的產品目標，因為 Xcode 專案中的非 framework 目標可能仍然會有無法自動解析模稜兩可。

## <a name="216-march-17-2015"></a>2.1.6 (2015 年 3 月 17日日)

[下載 v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 固定的二元運算子運算式繫結： 左手邊的運算式已正確交換與右側 (例如`1 << 0`不正確地繫結為`0 << 1`)。 到 Adam Kemp 感謝您的注意到這 ！
* 已修正的問題`NSInteger`和`NSUInteger`做為繫結`int`並`uint`而不是`nint`和`nuint`i386; 上`-DNS_BUILD_32_LIKE_64`現在會傳遞至要剖析的 Clang `objc/NSObjCRuntime.h` i386 上如預期運作。
* Mac OS X Sdk 的預設架構 (例如`-sdk macosx10.10`) 現在是 x86_64 而不是 i386，因此`-arch`可以省略，除非想要使用覆寫預設值。

## <a name="210-march-15-2015"></a>2.1.0 (2015 年 3 月 15日日)

[下載 v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849):請確定`using ObjCRuntime;`時產生`ArgumentSemantic`用。
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850):請確定`using System.Runtime.InteropServices;`時產生`DllImport`用。
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852):預設值`DllImport`載入符號從`__Internal`。
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848):略過向前宣告 OBJECTIVE-C 容器宣告。
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846):繫結具有單一的限定性條件，做為具象介面的通訊協定類型 (`id<Foo>`作為`Foo`而不是`Foundation.NSObject<Foo>`)。
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037):繫結`UInt32`， `UInt64`，並`Int64`做為常值`Int32`若要卸除`u`及/或`uL`尾碼時的值可以安全地放入`Int32`。
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038):修正列舉名稱對應，當原始的原生名稱開頭為`k`前置詞。
* `sizeof` C 運算式，其引數類型未對應到C#基本類型會於 Clang 中評估並繫結為整數常值，以避免產生不正確C#。
* 修正 OBJECTIVE-C 語法，其類型是區塊的屬性 （OBJECTIVE-C 程式碼會出現在繫結宣告上方的註解）。
* 以其原始型別繫結毒死類型 (`int[]`至 decays`int*`語意在分析期間於 Clang 中，但將它繫結做為寫入原始`int[]`改為)。

非常謝謝 Dave Dunkin 來報告許多點本版已修正的 bug ！

## <a name="200-march-9-2015"></a>2.0.0:2015 年 3 月 9日日

[下載 v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目標 Sharpie 2.0 是主要的版本，具有改良的 Clang 型驅動程式和剖析器和新的 NRefactory 基礎繫結引擎。 這些改良的元件提供_大部分_更好的繫結，特別是以型別繫結。 已進行內部目標 Sharpie 會產生許多使用者可見的功能，在未來版本中的許多其他改進功能。

目標 Sharpie 2.0 為基礎 Clang 3.6.1。

### <a name="type-binding-improvements"></a>型別繫結的改進

* 現在支援 OBJECTIVE-C 區塊。 這包括匿名/內嵌區塊和區塊名為透過`typedef`。 將繫結為匿名的區塊`System.Action`或是`System.Func`委派，而具名的區塊將會繫結為強式名稱`delegate`類型。

* 沒有立即前面加上的匿名列舉改善命名啟發學習法`typedef`這類內建整數型別解析`long`或`int`。

* C 指標現在繫結為C#`unsafe`指標，而非`System.IntPtr`。 這會導致當您可能想要開啟指標參數的繫結中的更清楚`out`或`ref`參數。 不可能永遠推斷出參數是否應該是`out`或`ref`，因此指標會保留在繫結，以便更容易稽核。

* 上述的指標繫結的例外狀況時，遇到 OBJECTIVE-C 物件的陣序規範 2 指標做為參數。 在這些情況下，慣例是主導因素和參數會做為繫結`out`(例如`NSError **error`→ `out NSError error`)。

### <a name="verify-attribute"></a>確認屬性

您通常會發現，目標 Sharpie 所產生的繫結將會立即使用註解`[Verify]`屬性。 這些屬性會指出您應該_確認_目標 Sharpie 未正確的動作，藉由比較原始的 C/Objective C 宣告 （這會在繫結宣告上方的註解中提供） 的繫結。

確認已_建議_所有的繫結宣告，但很可能是_必要_宣告標註的`[Verify]`屬性。 這是因為在許多情況下，沒有足夠的中繼資料推斷如何最能產生繫結原生來源原始程式碼。 您可能需要參考文件或要做出最佳的繫結決策的標頭檔內的程式碼註解。

請參閱[確認屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)文件，如需詳細資訊。

### <a name="other-notable-improvements"></a>其他值得注意的改進功能

* `using` 陳述式現在會產生根據繫結的型別。 比方說，如果`NSURL`繫結，`using Foundation;`也會產生陳述式。

* `struct` 並`union`宣告會立即繫結，使用`[FieldOffset]`一輪的牌，等位的。

* 使用常數運算式的初始設定式的列舉值會立即正確繫結;完整的運算式會轉譯為C#。

* Variadic 方法和區塊現在繫結。

* 架構現在也支援透過`-framework`選項。 請參閱文件[繫結原生架構](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks)如需詳細資訊。

* OBJECTIVE-C 來源的程式碼會自動偵測到現在，這應該不需要傳遞`-ObjC`或`-xobjective-c`來手動 Clang。

* Clang 模組使用方式 (`@import`) 現在會自動偵測，這應該不需要傳遞`-fmodules`到 Clang 手動針對程式庫可於 Clang 中使用新的模組支援。

* Xamarin 統一 API 現在是預設繫結目標;使用`-classic`32 位元唯一的傳統 API 為目標的選項。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修正

* 修正`instancetype`繫結 Objective C 類別中使用時
* 完整名稱的 Objective C 類別
* 前置詞與 OBJECTIVE-C 通訊協定`I`(例如`INSCopying`而不是`NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35:2014 年 12 月 21日日

[下載 v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

小幅度 bug 修正。

## <a name="111-december-15-2014"></a>1.1.1:2014 年 12 月 15日日

[下載 v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 已在內部使用和在 2013 年 4 月後目標 Sharpie 初始預覽版本的 Xamarin 開發的 1.5 年後的第一個主要版本。 此版本是以穩定且可供各種不同的原生程式庫，提供新的 Clang 後端通常被視為第一個。

