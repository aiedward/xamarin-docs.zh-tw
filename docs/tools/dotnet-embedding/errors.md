---
title: .NET 內嵌錯誤
description: 本文件說明.NET 內嵌所產生的錯誤。 列出程式碼並指定描述，以協助疑難排解錯誤。
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: topgenorth
ms.author: toopge
ms.date: 04/11/2018
ms.openlocfilehash: 5aa95379b18fb9412e7888c79a2cd19997f7258f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793712"
---
# <a name="net-embedding-errors"></a>.NET 內嵌錯誤

## <a name="em0xxx-binding-error-messages"></a>EM0xxx： 繫結的錯誤訊息

例如， 參數、 環境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000： 未預期的錯誤-請填寫 錯誤報告在 https://github.com/mono/Embeddinator-4000/issues

發生未預期的錯誤狀況。 請[提出問題](https://github.com/mono/Embeddinator-4000/issues)盡可能最多資訊，包括：

* 完整建置記錄檔，具有最大值的詳細資訊
* 重現錯誤的最小測試案例
* 所有的版本資訊

若要取得正確的版本資訊的最簡單方式是使用**Xamarin Studio**功能表上，**關於 Xamarin Studio**項目，**顯示詳細資料**按鈕，然後複製/貼上的版本資訊 (您可以使用**複製資訊**按鈕)。

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001： 無法建立輸出目錄 `X`

所指定的目錄名稱`-o=DIR`不存在，而且無法建立。 可能是檔案系統的名稱無效。

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002： 選項`X`不支援

此工具不支援選項`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003： 平台`X`不正確。

此工具不支援的平台`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004： 目標`X`不正確。

此工具不支援目標`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005： 編譯目標`X`不正確。

此工具不支援編譯目標`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006： 找不到 Xcode 位置。

此工具找不到目前選取的 Xcode 位置使用`xcode-select -p`命令。 請確認此命令會成功，並傳回正確的 Xcode 位置。

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007： 無法取得 '{sdk}' 的 sdk 版本。

此工具無法取得 SDK 版本使用`xcrun --show-sdk-version --sdk {sdk}`命令。 請確認此命令會成功，並傳回的 SDK 版本。

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: '{arch}' 的架構 {平台} 無效。 {平台} 有效的架構: '{架構}'。

錯誤訊息中的架構為目標平台無效。 請確認-abi 選項傳遞有效的架構。

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009： 功能`X`目前不會實作由產生器

這是我們想要在產生器的未來版本中修正的已知的問題。 比重是歡迎畫面。

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010： 無法合併架構 '{simulatorFramework}' 和 '{deviceFramework}'，因為檔案 '{file}' 中都有。

此工具無法合併的錯誤訊息中所述的架構，因為它們之間沒有通用的檔案。

這可能表示.NET 內嵌; 中的 bug請檔案錯誤報告在[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)與測試案例。

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011： 組件`X`不存在。

此工具找不到組件`X`引數中指定。

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: 組件名稱`X`不是唯一的

提供的多個組件具有相同的內部名稱，並不會可能在執行階段加以區分。

最可能的原因是組件在命令列引數多次指定。 不過它是原來的名稱和多個複本已重新命名的組件仍會保持無法共存。

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013： 找不到 'X'、 'Y' 所參考的組件。

此工具找不到 'X'、 'Y' 的組件所參考的組件。 請確定會繫結的組件相同的目錄中的所有參考組件。

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014： 找不到 {產品} （{產品} {min_version} 為必要）。

在系統上找不到錯誤訊息中所述的相依性。

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015： 找不到有效的 {產品} 版本 (找到 {版本}，但至少 {min_version} 是必要項)。

相依性錯誤所述在系統上，找不到訊息，但太舊。 請更新為較新版本。

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016： 無法建立符號連結 '{file}'-> '{target}': 錯誤 {號碼}

無法建立錯誤訊息中所述的符號連結。

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 無法剖析命令列引數 'A': *

指定命令列選項的語法`A`無法剖析工具。 很可能不正確，請與文件或正確的語法的說明。

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099： 內部錯誤 *。 請提出問題報告與測試案例 (https://github.com/mono/Embeddinator-4000/issues)。

以.NET 內嵌內部一致性檢查失敗時，會報告此錯誤訊息。

這表示.NET 內嵌; 中的 bug請檔案錯誤報告在[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)與測試案例。

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx： 程式碼處理

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010： 輸入`T`，所以沒有產生`X`不支援。

這是**警告**的型別`T`會被忽略 （也就是不會產生） 因為它使用`X`，不支援的功能。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011： 輸入`T`不會產生，因為其欠缺封送處理程式碼與原生的對應項目。

這是**警告**的型別`T`會被忽略 （也就是不會產生） 因為它會公開從.NET framework 需要額外的封送處理的項目。

注意： 這是工具的可能會支援 get，但有一些限制，在未來版本中的項目。

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020： 建構函式`C`參數類型，因此不會產生`T`不支援。

這是**警告**的建構函式`C`會被忽略 （也就是不會產生） 因為型別參數`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021： 建構函式`C`有任何包裝函式產生的預設值。

這是**警告**的預設參數的建構函式`C`均未產生任何額外的程式碼。 最常見的原因是現有的方法已經有相同的簽章。 例如， 在.net 是可能有：

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在產生這類情況下只有兩個`init`將建立選取器，這兩個呼叫 Mono，但是沒有包裝函式更新會存在。

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030： 方法`M`不會產生，因為傳回型別`T`不支援。

這是**警告**，方法`M`會被忽略 （也就是不會產生） 因為它是傳回型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031： 方法`M`參數類型，因此不會產生`T`不支援。

這是**警告**，方法`M`會被忽略 （也就是不會產生） 因為型別參數`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032： 方法`M`有任何包裝函式產生的預設值。

這是**警告**之方法的預設參數`M`均未產生任何額外的程式碼。 最常見的原因是現有的方法已經有相同的簽章。 例如， 在.net 是可能有：

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在產生這類情況下只有兩個`increment`將建立選取器，這兩個呼叫 Mono，但是沒有包裝函式更新會存在。

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033： 方法`M`不會產生，因為另一種方法會公開易記名稱的運算子。

這是**警告**，方法`M`不會產生，因為另一種方法會公開易記名稱的運算子。 (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034： 擴充方法`M`不會產生內部類別因為他們無法建立基本型別`T`。 產生一般的靜態方法。

這是**警告**primivite 上的擴充方法的輸入 (例如`System.Int32`) 找不到。 Objective C 中不可能在基本類型上建立類別目錄。 而是產生器也會產生一般的靜態方法。

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040： 屬性`P`參數類型，因此不會產生`T`不支援。

這是**警告**，屬性`P`會被忽略 （也就是不會產生） 因為公開型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041： 索引的屬性上`T`不會產生，因為不支援多個索引的屬性。

這是**警告**，索引的屬性上`T`會被忽略 （也就是不會產生） 因為不支援多個索引的屬性。

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050： 欄位`F`欄位類型，因此不會產生`T`不支援。

這是**警告**，欄位`F`會被忽略 （也就是不會產生） 因為公開型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051： 項目`E`將產生做為`F`因為其名稱與重要的目標 c 選取器名稱衝突。

這是**警告**的項目`E`改為將會產生為`F`因為其名稱與重要的目標 c 選取器名稱衝突。

上的選取器[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) objective c 中有重要的意義，而且必須小心覆寫。

注意： 保留器的清單會隨新版的工具發展。

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052： 項目`E`就不會產生其名稱與相同類別上的其他項目衝突。

這是**警告**該項目`E`不會產生如其名稱與相同類別上的其他項目衝突。

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053： 目標`E`Xamarin.iOS 和 Xamarin.Mac 不支援。 只有`framework`會被視為受支援而且應該使用的選項。

這是**警告**目標`E`會被視為不受支援的 Xamarin.iOS 和 Xamarin.Mac 使用案例。 

靜態或動態的內嵌.NET 程式庫的耗用量可能會需要額外的工作步驟或做調整，並應該避免在大部分的使用案例中。

請考慮移除您`--target`參數或傳遞`--target=framework`改為。

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx： 程式碼產生

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
