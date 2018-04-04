---
title: .NET 內嵌錯誤
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 64caaf6610d9f9193a686d91b4731cd4d4953fa6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="em0xxx-binding-error-messages"></a>EM0xxx： 繫結錯誤訊息

例如， 參數、 環境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->
<h3><a name="EM0000"/>EM0000： 未預期的錯誤-請填寫 錯誤報告在 https://github.com/mono/Embeddinator-4000/issues</h3>

發生未預期的錯誤狀況。 請[提出問題](https://github.com/mono/Embeddinator-4000/issues)盡可能最多資訊，包括：

* 完整建置記錄檔，具有最大值的詳細資訊
* 重現錯誤的最小測試案例
* 所有的版本資訊

若要取得正確的版本資訊的最簡單方式是使用**Xamarin Studio**功能表上，**關於 Xamarin Studio**項目，**顯示詳細資料**按鈕，然後複製/貼上的版本資訊 (您可以使用**複製資訊**按鈕)。

<h3><a name="EM0001"/>EM0001： 無法建立輸出目錄 `X`</h3>

所指定的目錄名稱`-o=DIR`不存在，而且無法建立。 可能是檔案系統的名稱無效。

<h3><a name="EM0002"/>EM0002： 選項`X`不支援</h3>

此工具不支援選項`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<h3><a name="EM0003"/>EM0003： 平台`X`不正確。</h3>

此工具不支援的平台`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<h3><a name="EM0004"/>EM0004： 目標`X`不正確。</h3>

此工具不支援目標`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<h3><a name="EM0005"/>EM0005： 編譯目標`X`不正確。</h3>

此工具不支援編譯目標`X`。 它可能是工具的另一個版本支援它或它不適用於此環境。

<h3><a name="EM0006"/>EM0006： 找不到 Xcode 位置。</h3>

此工具找不到目前選取的 Xcode 位置使用`xcode-select -p`命令。 請確認此命令會成功，並傳回正確的 Xcode 位置。

<h3><a name="EM0007"/>EM0007： 無法取得 '{sdk}' 的 sdk 版本。</h3>

此工具無法取得 SDK 版本使用`xcrun --show-sdk-version --sdk {sdk}`命令。 請確認此命令會成功，並傳回的 SDK 版本。

<h3><a name="EM0008"/>EM0008: '{arch}' 的架構 {平台} 無效。 {平台} 有效的架構: '{架構}'。</h3>

錯誤訊息中的架構為目標平台無效。 請確認-abi 選項傳遞有效的架構。

<h3><a name="EM0009"/>EM0009： 功能`X`目前不會實作由產生器</h3>

這是我們想要在產生器的未來版本中修正的已知的問題。 比重是歡迎畫面。

<h3><a name="EM0010"/>EM0010： 無法合併架構 '{simulatorFramework}' 和 '{deviceFramework}'，因為檔案 '{file}' 中都有。</h3>

此工具無法合併的錯誤訊息中所述的架構，因為它們之間沒有通用的檔案。

這可能表示 Embeddinator 4000; 中的 bug請檔案錯誤報告在[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)與測試案例。

<h3><a name="EM0011"/>EM0011： 組件`X`不存在。</h3>

此工具找不到組件`X`引數中指定。

<h3><a name="EM0012"/>EM0012: 組件名稱`X`不是唯一的</h3>

提供的多個組件具有相同的內部名稱，並不會可能在執行階段加以區分。

最可能的原因是組件在命令列引數多次指定。 不過它是原來的名稱和多個複本已重新命名的組件仍會保持無法共存。

<h3><a name="EM0013"/>EM0013： 找不到 'X'、 'Y' 所參考的組件。</h3>

此工具找不到 'X'、 'Y' 的組件所參考的組件。 請確定會繫結的組件相同的目錄中的所有參考組件。

<h3><a name="EM0014"/>EM0014： 找不到 {產品} （{產品} {min_version} 為必要）。</h3>

在系統上找不到錯誤訊息中所述的相依性。

<h3><a name="EM0015"/>EM0015： 找不到有效的 {產品} 版本 (找到 {版本}，但至少 {min_version} 是必要項)。</h3>

相依性錯誤所述在系統上，找不到訊息，但太舊。 請更新為較新版本。

<h3><a name="EM0016">EM0016： 無法建立符號連結 '{file}'-> '{target}': 錯誤 {號碼}</h3>

無法建立錯誤訊息中所述的符號連結。

<h3><a name="EM0026"/>EM0026 無法剖析命令列引數 'A': *</h3>

指定命令列選項的語法`A`無法剖析工具。 很可能不正確，請與文件或正確的語法的說明。

<h3><a name="EM0099"/>EM0099： 內部錯誤 *。 請提出問題報告與測試案例 (https://github.com/mono/Embeddinator-4000/issues)。</h3>

在 Embeddinator 4000 內部一致性檢查失敗時，會報告此錯誤訊息。

這表示 Embeddinator 4000; 中的 bug請檔案錯誤報告在[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)與測試案例。


<!-- 1xxx: code processing -->

# <a name="em1xxx-code-processing"></a>EM1xxx： 程式碼處理

<h3><a name="EM1010"/>型別`T`，所以沒有產生`X`不支援。</h3>

這是**警告**的型別`T`會被忽略 （也就是不會產生） 因為它使用`X`，不支援的功能。

附註： 支援的功能將持續改進與新版本的工具。


<h3><a name="EM1011"/>型別`T`不會產生，因為其欠缺原生的對應項目。</h3>

這是**警告**的型別`T`會被忽略 （也就是不會產生） 因為它會公開一些從.NET framework 中的原生的平台有沒有對應項目使用它。


<h3><a name="EM1011"/>型別`T`不會產生，因為其欠缺封送處理程式碼與原生的對應項目。</h3>

這是**警告**的型別`T`會被忽略 （也就是不會產生） 因為則會使用這個項目從需要額外封送處理.NET framework 公開。

注意： 這是項目可能會取得支援，但有一些限制，在未來版本的工具。


<h3><a name="EM1020"/>建構函式`C`參數類型，因此不會產生`T`不支援。</h3>

這是**警告**的建構函式`C`會被忽略 （也就是不會產生） 因為型別參數`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。


<h3><a name="EM1021"/>建構函式`C`有任何包裝函式產生的預設值。</h3>

這是**警告**的預設參數的建構函式`C`均未產生任何額外的程式碼。 最常見的原因是現有的方法已經有相同的簽章。 例如， 在.net 是可能有：

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在產生這類情況下只有兩個`init`將建立選取器，這兩個呼叫 mono，但是沒有包裝函式更新會存在。


<h3><a name="EM1030"/>方法`M`不會產生，因為傳回型別`T`不支援。</h3>

這是**警告**，方法`M`會被忽略 （也就是不會產生） 因為它是傳回型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。


<h3><a name="EM1031"/>方法`M`參數類型，因此不會產生`T`不支援。</h3>

這是**警告**，方法`M`會被忽略 （也就是不會產生） 因為型別參數`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。


<h3><a name="EM1032"/>方法`M`有任何包裝函式產生的預設值。</h3>

這是**警告**之方法的預設參數`M`均未產生任何額外的程式碼。 最常見的原因是現有的方法已經有相同的簽章。 例如， 在.net 是可能有：

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在產生這類情況下只有兩個`increment`將建立選取器，這兩個呼叫 mono，但是沒有包裝函式更新會存在。


<h3><a name="EM1033"/>方法`M`不會產生，因為另一種方法會公開易記名稱的運算子。</h3>

這是**警告**，方法`M`不會產生，因為另一種方法會公開易記名稱的運算子。 (https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)


<h3><a name="EM1034"/>擴充方法`M`不會產生內部類別因為他們無法建立基本型別`T`。 產生一般的靜態方法。</h3>

這是**警告**primivite 上的擴充方法的輸入 (例如`System.Int32`) 找不到。 在 ObjC 不可能在基本類型上建立類別目錄。 而是產生器也會產生一般的靜態方法。



<h3><a name="EM1040"/>屬性`P`參數類型，因此不會產生`T`不支援。</h3>

這是**警告**，屬性`P`會被忽略 （也就是不會產生） 因為公開型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<h3><a name="EM1041"/>索引屬性上`T`不會產生，因為不支援多個索引的屬性。</h3>

這是**警告**，索引的屬性上`T`會被忽略 （也就是不會產生） 因為不支援多個索引的屬性。


<h3><a name="EM1050"/>欄位`F`欄位類型，因此不會產生`T`不支援。</h3>

這是**警告**，欄位`F`會被忽略 （也就是不會產生） 因為公開型別`T`不支援。

必須有原因的詳細資訊，提供早期警告輸入`T`不支援。

附註： 支援的功能將持續改進與新版本的工具。

<h3><a name="EM1051"/>項目`E`將產生做為`F`因為其名稱與重要的目標 c 選取器名稱衝突。</h3>

這是**警告**的項目`E`改為將會產生為`F`因為其名稱與重要的目標 c 選取器名稱衝突。

上的選取器[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) objective c 中有重要的意義，而且必須小心覆寫。

注意： 保留器的清單會隨新版的工具發展。


<!-- 2xxx: code generation -->

# <a name="em2xxx-code-generation"></a>EM2xxx： 程式碼產生


<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
