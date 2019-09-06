---
title: .NET 嵌入錯誤
description: 本檔描述 .NET 內嵌所產生的錯誤。 錯誤是由程式碼所列出，並提供描述以協助進行疑難排解。
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: be62ba212cb92e031f760ef3f267bc3eac91c748
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282160"
---
# <a name="net-embedding-errors"></a>.NET 嵌入錯誤

## <a name="em0xxx-binding-error-messages"></a>EM0xxx:系結錯誤訊息

例如， 參數、環境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000:意外的錯誤-請于前填寫錯誤報表 https://github.com/mono/Embeddinator-4000/issues

發生未預期的錯誤狀況。 請盡可能以最多資訊提出[問題](https://github.com/mono/Embeddinator-4000/issues)，包括：

* 完整組建記錄檔，最詳細資訊
* 重現錯誤的最小測試案例
* 所有版本的

取得確切版本資訊的最簡單方式是使用  **Xamarin Studio**  功能表，**關於 Xamarin Studio**專案、**顯示詳細資料** 按鈕，以及複製/貼上版本資訊（您可以使用 **複製資訊** 按鈕）。

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001:無法建立輸出目錄`X`

所指定`-o=DIR`的目錄名稱不存在，因此無法建立。 它可能是不正確檔案系統名稱。

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002:不`X`支援選項

此工具不支援選項`X`。 另一個版本的工具可能會支援它，或不適用於此環境。

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003:平臺`X`無效。

此工具不支援此平臺`X`。 另一個版本的工具可能會支援它，或不適用於此環境。

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004:目標`X`無效。

此工具不支援目標`X`。 另一個版本的工具可能會支援它，或不適用於此環境。

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005:編譯目標`X`無效。

此工具不支援編譯目標`X`。 另一個版本的工具可能會支援它，或不適用於此環境。

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006:找不到 Xcode 位置。

此工具無法使用`xcode-select -p`命令找出目前選取的 Xcode 位置。 請確認此命令是否成功，並傳回正確的 Xcode 位置。

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007:無法取得 ' {sdk} ' 的 sdk 版本。

此工具無法使用`xcrun --show-sdk-version --sdk {sdk}`命令取得 SDK 版本。 請確認此命令是否成功，並傳回 SDK 版本。

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008:架構 ' {結構} ' 對 {platform} 無效。 {Platform} 的有效架構為： ' {架構} '。

錯誤訊息中的架構對目標平臺無效。 請確認--abi 選項已通過有效的架構。

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009:此功能`X`目前不是由產生器所執行

這是我們想要在未來的產生器版本中修正的已知問題。 歡迎投稿。

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010:無法合併架構 ' {simulatorFramework} ' 和 ' {deviceFramework} '，因為兩者都有檔案 ' {file} '。

此工具無法合併錯誤訊息中所述的架構，因為兩者之間有共同的檔案。

這可能表示 .NET 內嵌中有錯誤;請在[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)測試案例中提出錯誤報表。

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011:元件`X`不存在。

此工具找不到引數`X`中指定的元件。

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012:元件名稱`X`不是唯一的

提供的一個以上的元件具有相同的內部名稱，而且無法在執行時間加以區分。

最有可能的原因是在命令列引數上指定了一次以上的元件。 不過，重新命名的元件仍然會保留其原始名稱，而且無法可共存多個複本。

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013:找不到 ' Y ' 所參考的元件 ' X '。

此工具找不到元件 ' Y ' 所參考的元件 ' X '。 請確定所有參考的元件都位於與要系結之元件相同的目錄中。

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014:找不到 {product} （需要 {product} {min_version}）。

在系統上找不到錯誤訊息中所述的相依性。

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015:找不到 {product} 的有效版本（找到 {version}，但至少需要 {min_version}）。

在系統上找到錯誤訊息中所述的相依性，但它太舊了。 請更新為較新的版本。

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016:無法建立符號 ' {file} '-> ' {target} '：錯誤 {number}

無法建立錯誤訊息中所述的符號。

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 無法剖析命令列引數 ' A '： *

工具無法剖析為命令列選項`A`提供的語法。 這可能不正確，請參閱檔或說明，以取得正確的語法。

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099:內部錯誤 *。 請提出問題報告與測試案例 (https://github.com/mono/Embeddinator-4000/issues) 。

當 .NET 內嵌中的內部一致性檢查失敗時，就會回報此錯誤訊息。

這表示 .NET 內嵌中的 bug;請在[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)測試案例中提出錯誤報表。

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx:程式碼處理

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010:不`T`會產生類型， `X`因為不支援。

這是將忽略類型`T` （也就是不會產生任何內容）的警告，因為它使用`X`了不支援的功能。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011:未`T`產生類型，因為它缺少具有原生對應的封送處理常式代碼。

這是將忽略類型`T` （也就是不會產生任何專案）的警告，因為它會從 .net framework 公開需要額外封送處理的內容。

注意:在未來的工具版本中，這是可能受到支援的功能，但有一些限制。

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020:因為`C`不支援參數類型`T` ，所以不會產生此函數。

這是**警告**， `C`會忽略此函式（亦即不會產生任何內容），因為不支援類型`T`的參數。

應該會有稍早的警告，提供不支援`T`類型的詳細資訊。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021:此函式具有不會產生包裝函式的預設值。 `C`

這是一則**警告**，表示函式的`C`預設參數不會產生任何額外的程式碼。 最常見的原因是現有的方法已經具有相同的簽章。 例如， 在 .net 中，您可以：

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在這種情況下， `init`只會建立兩個產生的選取器，同時呼叫 Mono，但後面不會有包裝函式。

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030:因為`M`不支援傳回型`T`別，所以不會產生方法。

這是將忽略方法`M`的警告（也就是不會產生任何內容），因為它的傳回型`T`別不受支援。

應該會有稍早的警告，提供不支援`T`類型的詳細資訊。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031:因為`M`不支援參數類型`T` ，所以不會產生方法。

這是**警告**，會忽略方法`M` （也就是不會產生任何內容），因為不支援類型`T`的參數。

應該會有稍早的警告，提供不支援`T`類型的詳細資訊。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032:方法`M`具有不會產生包裝函式的預設值。

這是**警告**，方法`M`的預設參數不會產生任何額外的程式碼。 最常見的原因是現有的方法已經具有相同的簽章。 例如， 在 .net 中，您可以：

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在這種情況下， `increment`只會建立兩個產生的選取器，同時呼叫 Mono，但後面不會有包裝函式。

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033:因為`M`另一個方法會以易記名稱公開運算子，所以不會產生方法。

這是不會產生方法`M`的警告，因為另一個方法會使用易記名稱來公開運算子。 (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034:擴充方法`M`不會在類別內產生，因為無法在基本類型`T`上建立。 已產生一般的靜態方法。

這是一則**警告**，指出已找到 primivite 類型（例如`System.Int32`）上的擴充方法。 在 [目標-C] 中，您無法在基本類型上建立類別目錄。 相反地，產生器會產生一般的靜態方法。

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040:因為`P`不支援參數類型`T` ，所以不會產生屬性。

這是**警告**，會忽略屬性`P` （亦即不會產生任何內容），因為不支援公開的`T`類型。

應該會有稍早的警告，提供不支援`T`類型的詳細資訊。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041:因為不支援`T`多個索引屬性，所以不會產生上的索引屬性。

這是一則**警告**，表示將忽略`T`上的索引屬性（亦即不會產生任何內容），因為不支援多個索引屬性。

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050:因為`F`不支援欄位類型`T` ，所以不會產生欄位。

這是**警告**，將忽略欄位`F` （亦即不會產生任何內容），因為不支援公開的`T`類型。

應該會有稍早的警告，提供不支援`T`類型的詳細資訊。

注意:支援的功能會隨著新版本的工具進化。

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051:改`E` 為`F`產生元素，因為它的名稱與重要的目標-c 選取器相衝突。

這是將產生`E` `F`元素的警告，因為它的名稱與重要的目標-c 選取器衝突。

[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)上的選取器在目標 c 中有重要意義，必須謹慎地覆寫。

注意:保留的選取器清單會隨著新版本的工具進化。

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052:元素`E`的名稱與相同類別上的其他元素不會產生衝突。

這是**警告**，當元素`E`的名稱與相同類別上的其他專案衝突時，不會產生此專案。

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053:不`E`支援將目標用於 xamarin. iOS 和 xamarin. Mac。 只會將選項視為受支援，應加以使用。 `framework`

這是一則**警告**， `E`表示 Xamarin. iOS 和 xamarin 使用案例的目標被視為不受支援。 

靜態或動態 .NET 內嵌程式庫的耗用量可能需要額外的工作步驟或調整，而且在大部分的使用案例中都應該避免。

請考慮移除`--target`您的參數`--target=framework` ，或改為傳遞。

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx:程式碼產生

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
