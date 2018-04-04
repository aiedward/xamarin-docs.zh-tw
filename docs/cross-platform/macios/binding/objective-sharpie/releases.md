---
title: 發行記錄
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: a9eb4b1ea958f2756eb2a21ee44d0d0dfcf8b931
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="release-history"></a>發行記錄

## <a name="34-october-11-2017"></a>3.4 (2017 年 10 月 11，)

[下載 v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支援適用於 Xcode 9: iOS 11、 macOS 10.13，tvOS 11 和 watchOS 4
* 現在應該修正 SIMD 與 tgmath 問題
* 完全移除遙測

## <a name="33-august-3-2016"></a>3.3 (2016 年 8 月 3 日)

[下載 v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 對於 Xcode 8 Beta 4，iOS 10、 macOS 10.12 tvOS 10 和 watchOS 3 支援。
* 更新為最新 Clang 主要組建 (2016年-08-02)
* [保存遙測送出選項](https://twitter.com/Symbiatch/status/760373403878559744)從`sharpie pod bind`至`sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 (2016 年 6 月 14 日)

[下載 v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支援 Xcode 8 Beta 1、 iOS 10、 macOS 10.12、 tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 (2016 年 31 日)

[下載 v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* CocoaPods 1.0 的支援
* 第一個建置原生改善 CocoaPods 繫結可靠性`.framework`然後繫結，
* 複製 CocoaPods`.framework`和繫結至定義`Binding`要輕鬆地進行整合與 Xamarin.iOS 和 Xamarin.Mac 繫結專案目錄

## <a name="30-october-5-2015"></a>3.0 (2015 年 10 月 5 日)

[下載 v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 包括輕量型泛型和 null 屬性，Xcode 7 中引進的新 Objective C 語言功能的支援
* 支援的最新的 iOS 和 Mac 的 Sdk。
* 建置和剖析支援的 Xcode 專案 ！ 您現在可以傳遞至目標 Sharpie 完整的 Xcode 專案和它將會執行效果最佳，找出正確的做法 (例如`sharpie bind Project.xcodeproj -sdk ios`)。
* [CocoaPods](https://cocoapods.org)支援 ！ 您可以現在設定、 建置及直接從目標 Sharpie 繫結 CocoaPods (例如`sharpie pod init ios AFNetworking && sharpie pod bind`)。
* 如果架構支援，導致最正確地剖析的架構，因為架構的結構由定義 Clang 模組時繫結架構，將會啟用其`module.modulemap`。
* Xcode 專案建置架構產品，請剖析該產品，而不是中繼產品目標為非 framework 為目標的 Xcode 專案中可能仍無法自動解決模稜兩可。

## <a name="216-march-17-2015"></a>2.1.6 (2015 年 3 月 17日日)

[下載 v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 固定的二元運算子運算式繫結： 運算式的左邊不正確已交換與右側 (例如`1 << 0`不正確地繫結為`0 << 1`)。 到 Adam Kemp 感謝您的注意這 ！
* 已修正的問題與`NSInteger`和`NSUInteger`所繫結為`int`和`uint`而不是`nint`和`nuint`上 i386;`-DNS_BUILD_32_LIKE_64`現在會傳遞至要剖析的 Clang `objc/NSObjCRuntime.h` i386 上正常運作。
* Mac OS X Sdk 的預設架構 (例如`-sdk macosx10.10`) 現在是 x86_64 而不是 i386，因此`-arch`可以省略，除非想要使用覆寫預設值。

## <a name="210-march-15-2015"></a>2.1.0 (2015 年 3 月 15日日)

[下載 v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc #27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849)： 確保`using ObjCRuntime;`時產生`ArgumentSemantic`用。
* [bxc #27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850)： 確保`using System.Runtime.InteropServices;`時產生`DllImport`用。
* [bxc #27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852)： 預設`DllImport`載入符號`__Internal`。
* [bxc #27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848)： 略過向前宣告 OBJECTIVE-C 容器宣告。
* [bxc #27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846)： 繫結通訊協定類型具有單一的限定性條件，為具象介面 (`id<Foo>`為`Foo`而不是`Foundation.NSObject<Foo>`)。
* [bxc #28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037)： 繫結`UInt32`， `UInt64`，和`Int64`做為常值`Int32`卸除`u`及/或`uL`後置字元時的值可以安全地放入`Int32`。
* [bxc #28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038)： 修正列舉名稱對應，當原始的原生名稱開頭`k`前置詞。
* `sizeof` 將評估 Clang C 運算式的引數型別不會對應到 C# 基本型別，並將其繫結為整數常值，以避免產生無效 C#。
* 修正 Objective C 屬性的型別是區塊的語法 （Objective C 程式碼會出現在 繫結的宣告上方的註解）。
* 以其原始型別繫結毒死類型 (`int[]` decays 至`int*`語意在分析期間 Clang 中，但將它繫結做為寫入原始`int[]`改為)。

感謝您對 Dave Dunkin 報告許多此點的版本中修正的 bug ！

## <a name="200-march-9-2015"></a>2.0.0: 2015 年 3 月 9 日

[下載 v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目標的 Sharpie 2.0 的功能改良的 Clang 基礎驅動程式和剖析器和新的 NRefactory 基礎繫結引擎的主要版本。 這些改良的元件提供_大部分_較佳的繫結，特別是以型別繫結。 已進行內部目標 Sharpie 會產生許多使用者看見的功能在未來版本中的許多其他的增強功能。

目標 Sharpie 2.0 根據 Clang 3.6.1。

### <a name="type-binding-improvements"></a>型別繫結的增強功能

* 現在支援 OBJECTIVE-C 區塊。 這包括匿名/內嵌區塊，以及透過具名區塊`typedef`。 匿名的區塊會繫結為`System.Action`或`System.Func`委派，而具名的區塊將會為強式名稱的結合`delegate`型別。

* 沒有立即前面加上的匿名列舉改善命名啟發學習法`typedef`解析內建的整數類資料類型例如`long`或`int`。

* C 指標現在會繫結為 C#`unsafe`指標，而非`System.IntPtr`。 這會導致當您可能想要開啟到的指標參數的繫結中的更清楚`out`或`ref`參數。 不可能永遠推斷出參數是否應為`out`或`ref`，因此，指標會保留在繫結，以便更容易稽核。

* 上述的指標繫結的例外狀況時，遇到 OBJECTIVE-C 物件 2 順位指標做為參數。 在這些情況下，為凗慣例和參數會繫結為`out`(例如`NSError **error`→ `out NSError error`)。

### <a name="verify-attribute"></a>確認屬性

您通常會發現目標 Sharpie 所產生的繫結現在註與`[Verify]`屬性。 這些屬性會指出您應該_確認_目標 Sharpie 未正確的做法，藉由比較原始 C/Objective C 宣告 （這會提供繫結的宣告上方的註解） 的繫結。

驗證_建議_所有繫結的宣告，但最有可能是_必要_宣告附註的`[Verify]`屬性。 這是因為在許多情況下，沒有足夠的中繼資料來推斷如何最產生繫結原始來源原生程式碼中。 您可能需要參考文件或程式碼註解內的標頭檔，以便做出最佳的繫結決策。

請參閱[確認屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)文件以取得詳細資料。

### <a name="other-notable-improvements"></a>其他值得注意的改進功能

* `using` 陳述式現在會產生根據繫結的型別。 比方說，如果`NSURL`已繫結`using Foundation;`也會產生陳述式。

* `struct` 和`union`宣告現在會繫結，使用`[FieldOffset]`一輪牌等位。

* 使用常數運算式初始設定式的列舉值將會立即適當地繫結;完整的運算式會轉譯為 C#。

* Variadic 方法和區塊現在繫結。

* 架構現在支援透過`-framework`選項。 請參閱說明文件[繫結的原生架構](http://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks)如需詳細資訊。

* Objective C 程式碼將會自動偵測，這應該不需要傳遞`-ObjC`或`-xobjective-c`到 Clang 手動。

* Clang 模組使用 (`@import`) 現在會自動偵測這應該不需要傳遞`-fmodules`到 Clang 以手動方式為使用 Clang 中新的模組支援的程式庫。

* Xamarin 統一的 API 現在是預設的繫結目標。使用`-classic`為目標的 32 位元的唯一傳統 API 選項。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修正

* 修正`instancetype`OBJECTIVE-C 類別中使用時，繫結
* 完整名稱 OBJECTIVE-C 類別
* 前置詞 Objective C 的通訊協定與`I`(例如`INSCopying`而不是`NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 2014 年 12 月 21 日

[下載 v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

次要的 bug 修正。

## <a name="111-december-15-2014"></a>1.1.1: 2014 年 12 月 15 日

[下載 v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 為內部使用和在目標 Sharpie 初始預覽遵循在 2013 年 4 月中的 Xamarin 的開發 1.5 年後的第一個主要版本。 此版本是通常被視為穩定且可供各種不同的原生程式庫，並且包含新的 Clang 後端的第一個。

