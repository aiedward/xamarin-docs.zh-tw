---
title: "適用於 Java 開發人員的 Xamarin"
description: "如果您是 Java 開發人員，您應該已經開始在 Xamarin 平台上運用自己的技巧和現有的程式碼，並享受 C# 的程式碼重複使用優勢。 您將會發現 C# 語法與 Java 語法非常類似，而且這兩種語言提供非常類似的功能。 此外，您將探索可讓開發工作更容易的 C# 特有功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A3B6C041-4052-4E7D-999C-C4FA10BE3D67
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 7abcaa218c6755a58e6f35e982a1144060df0b3b
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="xamarin-for-java-developers"></a>適用於 Java 開發人員的 Xamarin

_如果您是 Java 開發人員，您應該已經開始在 Xamarin 平台上運用自己的技巧和現有的程式碼，並享受 C# 的程式碼重複使用優勢。您將會發現 C# 語法與 Java 語法非常類似，而且這兩種語言提供非常類似的功能。此外，您將探索可讓開發工作更容易的 C# 特有功能。_


## <a name="overview"></a>總覽

本文提供適用於 Java 開發人員的 C# 程式設計簡介，主要著重於您在開發 Xamarin.Android 應用程式時可能遇到的 C# 語言功能。 此外，本文將說明這些功能與其 Java 對應項目之間的差異，並介紹無法在 Java 中使用的重要 C# 功能 (與 Xamarin.Android 相關)。 還會提供與其他參考資料的連結，讓您可以使用本文作為進一步研究 C# 和 .NET 的「出發點」。

如果您熟悉 Java，將能立即感受到 C# 的語法。 C# 語法與 Java 語法非常類似 &ndash; C# 是一種類似 Java、C 及 C++ 的「大括號」語言。 在許多方面，C# 語法的讀取方式就像 Java 語法的超集，但有一些重新命名和新增的關鍵字。

您可以在 C# 中找到多個 Java 的主要特性：

-   以類別為基礎的物件導向程式設計

-   強類型

-   支援介面

-   泛型

-   記憶體回收

-   執行階段編譯

Java 和 C# 都會編譯為要在受控執行環境中執行的中繼語言。 C# 和 Java 均屬靜態類型，而這兩種語言都會將字串視為不可變的類型。
這兩種語言都會使用單一根目錄的類別階層。 就像 Java，C# 僅支援單一繼承，不允許全域方法。
在這兩種語言中，會使用 `new` 關鍵字在堆積上建立物件，並於不再使用物件時進行記憶體回收。 這兩種語言都會使用 `try`/`catch` 語意，來提供正式的例外狀況處理支援。 這兩者都會提供執行緒管理和同步處理支援。

不過，Java 和 C# 之間有許多差異。 例如: 

-   Java 不支援隱含類型的區域變數 (C# 支援 `var` 關鍵字)。

-   在 Java 中，您只能依值傳遞參數，但在 C# 中，您可以傳址和依值方式進行傳遞 (C# 提供 `ref` 和 `out` 關鍵字，用來以傳址方式傳遞參數；這些關鍵字在 Java 中沒有對等項目)。

-   Java 不支援像是 `#define` 的前置處理器指示詞。

-   Java 不支援不帶正負號的整數類型，但 C 提供不帶正負號的整數類型，例如 `ulong`、`uint`、`ushort` 和 `byte`。

-   Java 不支援運算子多載；您可以在 C# 中多載運算子和轉換。

-   在 Java `switch` 陳述式中，程式碼可繼續進行下一個 switch 區段，但在 C# 中，每個 `switch` 區段的結尾都必須終止切換 (每個區段的結尾都必須使用 `break` 陳述式加以關閉)。

-   在 Java 中，您可以使用 `throws` 關鍵字來指定方法所擲回的例外狀況，但 C# 並沒有檢查例外狀況的概念 &ndash; C# 中不支援 `throws` 關鍵字。

-   C# 支援 Language-Integrated Query (LINQ)，讓您能夠使用保留字 `from`、`select` 和 `where`，以類似於資料庫查詢的方式撰寫對集合的查詢。


當然，在 C# 和 Java 之間還有更多本文未提及的差異。 此外，Java 和 C# 都會持續進行改良 (例如，Java 8 (尚未存在於 Android 工具鏈中) 支援 C# 樣式的 Lambda 運算式)，讓這些差異會隨著時間而改變。 此處僅概述不熟悉 Xamarin.Android 之 Java 開發人員目前所遇到的最重要差異。

-   [從 Java 轉為 C# 開發](#fundamentals)會介紹 C# 和 Java 之間的基本差異。

-   [物件導向程式設計功能](#oopfeatures)會概述這兩個語言之間最重要的物件導向功能差異。

-   [關鍵字差異](#keywords)提供一個表格來說明實用的關鍵字對等項目、僅適用 C# 的關鍵字，以及 C# 關鍵字定義的連結。

C# 會將許多重要功能帶入 Xamarin.Android，Android 上的 Java 開發人員目前仍無法立即使用這些功能。 這些功能可協助您以較少的時間撰寫更好的程式碼：

-   [屬性](#properties) &ndash; 使用 C# 的屬性系統，您可以安全地直接存取成員變數，而不需撰寫 setter 與 getter 方法。

-   [Lambda 運算式](#lambdas) &ndash; 在 C# 中，您可以使用匿名方法 (也稱為 *Lambda*)，以更簡潔且更有效率的方式表達您的功能。 您可以避免必須撰寫一次性使用之物件的額外負荷，並且可在不加入參數的情況下將本機狀態傳遞至方法。

-   [事件處理](#events) &ndash; C# 會針對「事件導向程式設計」提供語言層級的支援，讓物件可以註冊要在發生感興趣的事件時收到通知。 `event` 關鍵字會定義多點傳送廣播機制，讓發行者類別可用來通知事件訂閱者。

-   [非同步程式設計](#async) &ndash; C# 的非同步程式設計功能 (`async`/`await`) 使應用程式能夠持續回應。
    此功能的語言層級支援，可讓非同步程式設計容易實作且較不容易發生錯誤。


最後，Xamarin 可讓您透過名為「繫結」的技術，[運用現有的 Java 資產](#interop)。 您可以使用 Xamarin 的自動繫結產生器，從 C# 呼叫現有的 Java 程式碼、架構和程式庫。 若要這樣做，您只需在 Java 中建立靜態程式庫，並透過繫結公開至 C#。

<a name="fundamentals" />

## <a name="going-from-java-to-c-development"></a>從 Java 轉為 C# 開發

下列各節將概述 C# 和 Java 之間的基本「入門」差異；更後面的小節則會描述這些語言之間的物件導向差異。



### <a name="libraries-vs-assemblies"></a>程式庫與組件

Java 通常會在 **.jar** 檔案中封裝相關的類別。 不過，在 C# 和 .NET 中，會將先行編譯程式碼的可重複使用位元封裝為「組件」，通常會封裝為 *.dll* 檔案。 組件是 C#/.NET 程式碼的一個部署單位，而每個組件通常會與一個 C# 專案相關聯。 組件包含在執行階段進行 Just-In-Time 編譯的中繼程式碼 (IL)。

如需組件的詳細資訊，請參閱 MSDN [組件和全域組件快取](https://msdn.microsoft.com/en-us/library/ms173099.aspx) \(機器翻譯\) 主題。


### <a name="packages-vs-namespaces"></a>封裝與命名空間

C# 會使用 `namespace` 關鍵字來將相關類型群組在一起；這類似於 Java 的 `package` 關鍵字。 一般而言，Xamarin.Android 應用程式將位於針對該應用程式建立的命名空間。 例如，下列 C# 程式碼會針對氣象報告應用程式宣告 `WeatherApp` 命名空間包裝函式：

```csharp
namespace WeatherApp
{
    ...
```


### <a name="importing-types"></a>匯入類型

當您使用外部命名空間中定義的類型時，會使用 `using` 陳述式 (非常類似於 Java `import` 陳述式) 來匯入這些類型。 在 Java 中，您可能使用如下的陳述式來匯入單一類型：

```java
import javax.swing.JButton
```

您可能使用如下的陳述式來匯入整個 Java 套件：

```java
import javax.swing.*
```

C# `using` 陳述式的運作方式非常類似，但它可讓您匯入整個套件，而不需指定萬用字元。 例如，您通常會在 Xamarin.Android 來源檔案開頭看到一連串的 `using` 陳述式，如此範例所示：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Net;
using System.IO;
using System.Json;
using System.Threading.Tasks;
```

這些陳述式會從 `System`、`Android.App`、`Android.Content` 等命名空間匯入功能。



### <a name="generics"></a>泛型

Java 和 C# 都支援「泛型」，其為可讓您在編譯時期插入不同類型的預留位置。 不過，泛型在 C# 中的運作方式稍有不同。 在 Java 中，[類型清除](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html) \(英文\) 讓類型資訊只能於編譯時期使用，而無法在執行階段使用。 相反地，.NET Common Language Runtime (CLR) 提供泛型類型的明確支援，這表示 C# 可在執行階段存取類型資訊。 在日常的 Xamarin.Android 開發中，此區別的重要性通常並不明顯，但如果您使用[反映](https://msdn.microsoft.com/en-us/library/ms173183.aspx) \(機器翻譯\)，將依賴此功能，在執行階段存取類型資訊。

在 Xamarin.Android 中，您通常會看到用來取得版面配置控制項參考的泛型方法 `FindViewById`。 這個方法可接受泛型類型參數來指定要查閱的控制項類型。 例如: 

```csharp
TextView label = FindViewById<TextView> (Resource.Id.Label);
```

在此程式碼範例中，`FindViewById` 會取得 `TextView` 控制項 (定義於版面配置中) 的參考作為**標籤**，然後以 `TextView` 類型傳回它。

如需泛型的詳細資訊，請參閱 MSDN [泛型](https://msdn.microsoft.com/en-us/library/512aeb7t.aspx)主題。
請注意，在對於泛型 C# 類別的 Xamarin.Android 支援中有一些限制；如需詳細資訊，請參閱[限制](~/android/internals/limitations.md)。


<a name="oopfeatures" />

## <a name="object-oriented-programming-features"></a>物件導向程式設計功能

Java 和 C# 所使用的物件導向程式設計慣用語非常類似：

-   所有類別最終都衍生自單一根物件 &ndash; 所有 Java 物件都衍生自 `java.lang.Object`，而所有 C# 物件都衍生自 `System.Object`。

-   類別的執行個體均為參考類型。

-   當您存取執行個體的屬性和方法時，會使用 "<code>.</code>" 運算子。

-   透過 `new` 運算子，在堆積上建立所有類別執行個體。

-   由於這兩種語言都會使用記憶體回收，因此，有一個明確釋放未使用物件的方法 (亦即，沒有 `delete` 關鍵字，因為 C++ 中有此關鍵字)。

-   您可以透過繼承擴充類別，而這兩種語言只允許每個類型具有單一基底類別。

-   您可以定義介面，而類別可以繼承自 (亦即，實作) 多個介面定義。

不過，還有一些重要差異：

-   Java 有兩個功能強大但 C# 不支援的功能：匿名類別和內部類別 (不過，C# 允許巢串類別定義 &ndash; C# 的巢狀類別類似於 Java 的靜態巢狀類別)。

-   C# 支援 C 樣式的結構類型 (`struct`)，但 Java 不支援。

-   在 C# 中，您可以使用 `partial` 關鍵字，在不同的來源檔案中實作類別定義。

-   C# 介面無法宣告欄位。

-   C# 使用 C++ 樣式的解構函式語法來表達完成項。 此語法與 Java 的 `finalize` 方法不同，但語意幾乎完全相同 (請注意，在 C# 中，解構函式會自動呼叫基底類別解構函式 &ndash; 與明確呼叫 `super.finalize` 的 Java 相反)。



### <a name="class-inheritance"></a>類別繼承

若要在 Java 中擴充類別，您可以使用 `extends` 關鍵字。 若要在 C# 中擴充類別，您可以使用冒號 (`:`) 來表示衍生。 例如，在 Xamarin.Android 應用程式中，您通常會看到類似下列程式碼片段的類別衍生：

```csharp
public class MainActivity : Activity
{
    ...
```

在此範例中，`MainActivity` 繼承自 `Activity` 類別。

若要在 Java 中宣告對介面的支援，您可以使用 `implements` 關鍵字。 不過，在 C# 中，您只需將介面名稱加入至要從中繼承之類別的清單即可，如下列程式碼片段所示：

```csharp
public class SensorsActivity : Activity, ISensorEventListener
{
    ...
```

在此範例中，`SensorsActivity` 繼承自 `Activity`，並實作 `ISensorEventListener` 介面中宣告的功能。 請注意，介面清單必須緊跟在基底類別後面 (否則您將收到編譯時期錯誤)。 依照慣例，C# 介面名稱前面會加上大寫 "I"；這樣不需 `implements` 關鍵字，就能判斷哪些類別是介面。

當您想要在 C# 中防止類別進一步成為子類別時，可在類別名稱前面加上 `sealed` &ndash; 在 Java 中，您可以在類別名稱前加上 `final`。

如需 C# 類別定義的詳細資訊，請參閱 MSDN [類別和結構](https://msdn.microsoft.com/en-us/library/x9afc042.aspx)和[繼承](https://msdn.microsoft.com/en-us/library/x9afc042.aspx)主題。


<a name="properties" />

### <a name="properties"></a>屬性

在 Java 中，更動子方法 (setter) 和檢查方法 (getter) 通常可用來控制如何對類別成員進行變更，同時隱藏並保護這些成員，以免受到外部程式碼干擾。 例如，Android `TextView` 類別提供 `getText` 和 `setText` 方法。 C# 提供類似但更直接的機制，也就是「屬性」。
C# 類別的使用者可使用他們存取欄位相同的方式來存取屬性，但每次存取實際上會產生對呼叫端而言是透明的方法呼叫。 這個「隱藏」方法可以實作設定其他值、執行轉換或變更物件狀態之類的副作用。

屬性通常用於存取和修改 UI (使用者介面) 物件成員。 例如: 

```csharp
int width = rulerView.MeasuredWidth;
int height = rulerView.MeasuredHeight;
...
rulerView.DrawingCacheEnabled = true;
```

在此範例中，藉由存取 `rulerView` 物件的 `MeasuredWidth` 和 `MeasuredHeight` 屬性，從該物件中讀取寬度和高度值。 讀取這些屬性時，會在幕後擷取來自其相關聯 (但隱藏) 之欄位的值，然後傳回呼叫端。 `rulerView` 物件可能會以一種測量單位 (例如像素) 來儲存寬度和高度值，並在存取 `MeasuredWidth` 和 `MeasuredHeight` 屬性時，將這些值即時轉換為不同的測量單位 (例如公釐)。

`rulerView` 物件還擁有名為 `DrawingCacheEnabled` 的屬性 &ndash; 範例程式碼會將此屬性設為 `true`，以在 `rulerView` 中啟用繪圖快取。 在幕後，會使用新值來更新相關聯的隱藏欄位，而且可能會修改 `rulerView` 狀態的其他層面。 例如，將 `DrawingCacheEnabled` 設為 `false` 時，`rulerView` 可能也會清除物件中已累積的任何繪圖快取資訊。

屬性的存取權可以是讀取/寫入、唯讀或唯寫的權限。 此外，您還能使用不同的存取修飾詞進行讀取和寫入。 例如，您可以定義具有公用讀取權限但具有私用寫入權限的屬性。

如需 C# 屬性的詳細資訊，請參閱 MSDN [屬性](https://msdn.microsoft.com/en-us/library/x9fsa0sw.aspx)主題。



### <a name="calling-base-class-methods"></a>呼叫基底類別方法

若要在 C# 中呼叫基底類別建構函式，您可以使用冒號 (`:`)，後面接著 `base` 關鍵字和初始設定式清單；這個 `base` 建構函式呼叫會放在衍生的建構函式參數清單正後方。 在進入衍生的建構函式時呼叫基底類別建構函式；編譯器會將對基底建構函式的呼叫插入至方法主體的開頭處。 下列程式碼片段將說明如何從 Xamarin.Android 應用程式中衍生的建構函式呼叫基底建構函式：

```csharp
public class PictureLayout : ViewGroup
{
    ...
    public class PictureLayout (Context context)
           : base (context)
    {
        ...
    }
    ...
}

```

在此範例中，`PictureLayout` 類別衍生自 `ViewGroup` 類別。 範例中所示的 `PictureLayout` 建構函式會接受 `context` 引數，並透過 `base(context)` 呼叫來將其傳遞給 `ViewGroup` 建構函式。

若要在 C# 中呼叫基底類別方法，使用 `base` 關鍵字。 例如，Xamarin.Android 應用程式通常會進行基底方法的呼叫，如下所示：

```csharp
public class MainActivity : Activity
{
    ...
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
```

在此情況下，衍生類別 (`MainActivity`) 所定義的 `OnCreate` 方法會呼叫基底類別 (`Activity`) 的 `OnCreate` 方法。



### <a name="access-modifiers"></a>存取修飾詞

Java 和 C# 均支援 `public`、`private` 和 `protected` 存取修飾詞。 不過，C# 支援兩個額外的存取修飾詞：

-   **`internal`** &ndash; 類別成員只能在目前組件內加以存取。

-   **`protected internal`** &ndash; 類別成員可在定義組件、定義類別及衍生的類別(位於組件內外的衍生類別具有存取權) 內加以存取。

如需 C# 存取修飾詞的詳細資訊，請參閱 MSDN [存取修飾詞](https://msdn.microsoft.com/en-us/library/ms173121.aspx)主題。



### <a name="virtual-and-override-methods"></a>虛擬和覆寫方法

Java 和 C# 均支援「多型」，這是以相同方式處理相關物件的能力。 在這兩種語言中，您可以使用基底類別參考來參考衍生類別的物件，而衍生類別的方法可以覆寫其基底類別的方法。 這兩種語言均具備「虛擬」方法的概念，此為設計來由衍生類別中的方法所取代之基底類別中的方法。
就像 Java，C# 支援 `abstract` 類別和方法。

不過，在 Java 和 C# 之間有一些如何宣告虛擬方法和覆寫它們的差異：

-   在 C# 中，方法預設為非虛擬的。 父類別必須使用 `virtual` 關鍵字，明確地標示要覆寫哪些方法。 相反地，Java 中的所有方法依預設都是虛擬方法。

-   若要在 C# 中避免方法遭到覆寫，只需捨棄 `virtual` 關鍵字即可。 相反地，Java 會使用 `final` 關鍵字，來將方法標示為「不允許覆寫」。

-   C# 衍生類別必須使用 `override` 關鍵字，明確指出要覆寫虛擬基底類別方法。

如需 C# 對於多型的支援詳細資訊，請參閱 MSDN [多型](https://msdn.microsoft.com/en-us/library/ms173152.aspx)主題。


<a name="lambdas" />

## <a name="lambda-expressions"></a>Lambda 運算式

C# 讓您能夠建立「關閉」：內嵌的匿名方法，可存取要將它們封閉在其中的方法狀態。
使用 Lambda 運算式，您可以撰寫較少的程式碼，來實作可能已在 Java 中使用更多行程式碼實作的相同功能。

Lambda 運算式可讓您略過建立一次性使用的類別或匿名類別 (就像您在 Java 中所做的) 時所涉及的額外儀式 &ndash; 相反地，您只需撰寫內嵌方法程式碼的商務邏輯。 此外，由於 Lambda 具備周圍方法中變數的存取權，因此，您不需建立一份很長的參數清單來將狀態傳遞至方法程式碼。

在 C# 中，會使用 `=>` 運算子來建立 Lambda 運算式，如下所示：

```csharp
(arg1, arg2, ...) => {
    // implementation code
};
```

在 Xamarin.Android 中，Lambda 運算式通常會用來定義事件處理常式。 例如: 

```csharp
button.Click += (sender, args) => {
    clickCount += 1;    // access variable in surrounding code
    button.Text = string.Format ("Clicked {0} times.", clickCount);
};
```

在此範例中，Lambda 運算式程式碼 (大括號內的程式碼) 會遞增點按計數，並更新 `button` 文字來顯示點按計數。 這個 Lambda 運算式會使用 `button` 物件註冊，以作為每次點選按鈕時要呼叫的 Click 事件處理常式 (後續內容中將更詳細地說明事件處理常式)。在這個簡單範例中，Lambda 運算式程式碼不會使用 `sender` 和 `args` 參數，但 Lambda 運算式中必須要有它們，才能符合事件註冊的方法簽章需求。 背後的原理是，C# 編譯器會將 Lambda 運算式轉譯為匿名方法，每次發生按鈕 Click 事件時即會呼叫此方法。

如需 C# 和 Lambda 運算式的詳細資訊，請參閱 MSDN [Lambda 運算式](https://msdn.microsoft.com/en-us/library/bb397687.aspx)主題。


<a name="events" />

## <a name="event-handling"></a>事件處理

「事件」是在該物件發生感興趣的事情時，用來通知已註冊訂閱者的方式。 不同於在 Java 中，訂閱者通常會實作包含回呼方法的 `Listener` 介面，C# 會透過「委派」提供事件處理的語言層級支援。 「委派」類似物件導向的類型安全函式指標 &ndash; 它會封裝物件參考和方法語彙基元。 如果用戶端物件想要訂閱事件，它會建立委派，並將委派傳遞至通知物件。
發生事件時，通知物件會叫用委派物件所表示的方法，通知事件的訂閱用戶端物件。 在 C# 中，事件處理常式基本上就是透過委派叫用的方法。

如需委派的詳細資訊，請參閱 MSDN [委派](https://msdn.microsoft.com/en-us/library/ms173171.aspx)主題。

在 C# 中，事件是「多點傳送」；也就是，在事件發生時，可以通知多個接聽程式。 當您考慮 Java 和 C# 事件註冊之間的語法差異時，可觀察到此差異。 在 Java 中，您會呼叫 `SetXXXListener` 來註冊事件通知；在 C# 中，您會使用 `+=` 運算子，藉由將委派「加入」至事件接聽程式清單來註冊事件通知。
在 Java 中，您會呼叫 `SetXXXListener` 來取消註冊，而在 C# 中，您會使用 `-=`，從接聽程式清單中「減去」您的委派。

在 Xamarin.Android 中，當使用者對 UI 控制項執行某個動作時，通常會使用事件來通知物件。 一般來說，UI 控制項必須具備使用 `event` 關鍵字定義的成員；您會將委派連結至這些成員，以訂閱來自該 UI 控制項的事件。

訂閱事件：

1.  建立委派物件，其會參考您想要在事件發生時叫用的方法。

2.  使用 `+=` 運算子，將委派連結至您訂閱的事件。

下列範例會定義委派 (明確使用 `delegate` 關鍵字) 來訂閱按鈕 Click。
這個按鈕 Click 處理常式會啟動新的活動：

```csharp
startActivityButton.Click += delegate {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

不過，您也可以使用 Lambda 運算式來註冊事件，完全略過 `delegate` 關鍵字。 例如: 

```csharp
startActivityButton.Click += (sender, e) => {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

在此範例中，`startActivityButton` 物件的事件會預期含有特定方法簽章的委派：一個接受傳送者和事件引數並傳回 void 的委派。 不過，由於我們不想特意明確地定義這類委派或它的方法，因此，會使用 `(sender, e)` 宣告方法的簽章，並使用 Lambda 運算式來實作事件處理常式的主體。
請注意，我們必須宣告這個參數清單，即使並未使用 `sender` 和 `e` 參數也一樣。

請務必記住，您可以取消訂閱委派 (透過 `-=` 運算子)，但無法取消訂閱 Lambda 運算式 &ndash; 嘗試這樣做，可能會導致記憶體流失。 只有在您的處理常式不會取消訂閱事件時，才能使用 Lambda 形式的事件註冊。

一般而言，Lambda 運算式可用來宣告 Xamarin.Android 程式碼中的事件處理常式。 這個宣告事件處理常式的簡略方式一開始可能看似神秘，不過，它可以在您寫入和讀取程式碼時節省大量時間。 隨著熟悉度的增加，您會變得習慣辨識此模式 (Xamarin.Android 程式碼中經常發生)，而您會花費更多時間來思考應用程式的商務邏輯，並減少費力完成語法額外負荷的時間。


<a name="async" />

## <a name="asynchronous-programming"></a>非同步程式設計

「非同步程式設計」可增進應用程式的整體回應能力。 當應用程式的某個部分遭到漫長作業封鎖時，非同步程式設計功能可讓應用程式程式碼的剩餘部分繼續執行。
存取 Web、處理影像，以及讀取/寫入檔案，都是會導致整個應用程式看起來像凍結般 (如果它不會以非同步方式寫入) 的作業範例。

C# 包含透過 `async` 和 `await` 關鍵字進行非同步程式設計的語言層級支援。 這些語言功能讓您能夠非常容易地撰寫程式碼來執行長時間執行的工作，而不會封鎖應用程式的主執行緒。 簡單來說，您可以在方法上使用 `async` 關鍵字，來指出方法中的程式碼會以非同步方式執行，而且不會封鎖呼叫端的執行緒。 您可以在呼叫使用 `async` 標記的方法時，使用 `await` 關鍵字。 編譯器會將 `await` 解譯為要將您的方法執行移至背景執行緒 (會將工作傳回給呼叫端) 所在的點。 完成此工作時，程式碼的執行會在程式碼中 `await` 點的呼叫端執行緒上繼續執行，並傳回 `async` 呼叫的結果。 依照慣例，以非同步方式執行的方法會在其名稱結尾加上 `Async` 後置字元。

在 Xamarin.Android 應用程式中，當長時間執行的作業在背景工作中執行時，通常會使用 `async` 和 `await` 來釋放 UI 執行緒，讓它可以回應使用者輸入 (例如，點選 [取消] 按鈕)。

在下列範例中，按鈕 Click 事件處理常式會導致非同步作業從 Web 下載影像：


```csharp
downloadButton.Click += downloadAsync;
...
async void downloadAsync(object sender, System.EventArgs e)
{
    webClient = new WebClient ();
    var url = new Uri ("http://photojournal.jpl.nasa.gov/jpeg/PIA15416.jpg");
    byte[] bytes = null;

    bytes = await webClient.DownloadDataTaskAsync(url);

    // display the downloaded image ...
```

在此範例中，當使用者按一下 `downloadButton` 控制項時，`downloadAsync` 事件處理常式會建立 `WebClient` 物件和 `Uri` 物件，以從指定的 URL 擷取影像。 接下來，它會搭配這個 URL 呼叫 `WebClient` 物件的 `DownloadDataTaskAsync` 方法來擷取影像。

請注意，`downloadAsync` 的方法宣告會以 `async` 關鍵字開始，表示它將以非同步方式執行並傳回工作。 也請注意，對 `DownloadDataTaskAsync` 的呼叫會以 `await` 關鍵字開始。 應用程式會將事件處理常式的執行 (從 `await` 出現點開始) 移至背景執行緒，直到 `DownloadDataTaskAsync` 完成並傳回為止。
同時，應用程式的 UI 執行緒仍然可以回應使用者輸入，並引發其他控制項的事件處理常式。 當 `DownloadDataTaskAsync` 完成 (這可能需要幾秒鐘) 時，執行就會從將 `bytes` 變數設為對 `DownloadDataTaskAsync` 之呼叫的結果處繼續，而事件處理常式程式碼的其餘部分會顯示呼叫端 (UI) 執行緒上下載的影像。

如需 C# 中 `async`/`await` 的簡介，請參閱 MSDN [使用 Async 和 Await 進行非同步程式設計](https://msdn.microsoft.com/en-us/library/hh191443.aspx) \(機器翻譯\) 主題。
如需非同步程式設計功能的 Xamarin 支援詳細資訊，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。


<a name="keywords" />

## <a name="keyword-differences"></a>關鍵字差異

Java 中使用的許多語言關鍵字也會在 C# 中使用。 另外有些 Java 關鍵字在 C# 中具有相等但以不同方式命名的對等項目，如下表所示：

|Java|C#|描述|
|---|---|---|
|`boolean`|[bool](https://msdn.microsoft.com/en-us/library/c8f5xwh7.aspx)|用來宣告布林值的真偽。|
|`extends`|`:`|優先要從中繼承的類別和介面。|
|`implements`|`:`|優先要從中繼承的類別和介面。|
|`import`|[using](https://msdn.microsoft.com/en-us/library/zhdeatwt.aspx)|從命名空間匯入類型，也可用來建立命名空間別名。|
|`final`|[sealed](https://msdn.microsoft.com/en-us/library/88c54tsw.aspx)|防止類別衍生；防止方法和屬性在衍生類別中遭到覆寫。|
|`instanceof`|[is](https://msdn.microsoft.com/en-us/library/scekt9xw.aspx)|評估物件是否可與指定的類型相容。|
|`native`|[extern](https://msdn.microsoft.com/en-us/library/e59b22c5.aspx)|宣告在外部實作的方法。|
|`package`|[命名空間](https://msdn.microsoft.com/en-us/library/z2kcy19k.aspx)|宣告一組相關物件的範圍。|
|`T...`|[params T](https://msdn.microsoft.com/en-us/library/w5zay9db.aspx)|指定採用可變數目之引數的方法參數。|
|`super`|[base](https://msdn.microsoft.com/en-us/library/hfw7t1ce.aspx)|用來存取衍生類別中父類別的成員。|
|`synchronized`|[lock](https://msdn.microsoft.com/en-us/library/c5kehkcz.aspx)|包裝含有鎖定取得和釋放的重要程式碼區段。|


此外，還有許多 C# 中特有的關鍵字，它們在在 Java 中沒有對應項目。 Xamarin.Android 程式碼通常會使用下列 C# 關鍵字 (當您透過[範例程式碼](https://developer.xamarin.com/samples/android/all/) \(英文\) 讀取時非常適合參考此表格)：

|C#|描述|
|---|---|
|[as](https://msdn.microsoft.com/en-us/library/cscsdfbt.aspx)|在可相容的參考類型或可為 Null 的類型之間執行轉換。|
|[async](https://msdn.microsoft.com/en-us/library/hh156513.aspx)|指定方法或 Lambda 運算式為非同步。|
|[await](https://msdn.microsoft.com/en-us/library/hh156528.aspx)|暫停執行方法，直到工作完成為止。|
|[byte](https://msdn.microsoft.com/en-us/library/5bdb6693.aspx)|不帶正負號的 8 位元整數類型。|
|[delegate](https://msdn.microsoft.com/en-us/library/900fyy8e.aspx)|用來封裝方法或匿名方法。|
|[enum](https://msdn.microsoft.com/en-us/library/sbbt4032.aspx)|宣告列舉，一組具名常數。|
|[event](https://msdn.microsoft.com/en-us/library/8627sbea.aspx)|宣告發行者類別中的事件。|
|[fixed](https://msdn.microsoft.com/en-us/library/f58wzh21.aspx)|防止變數遭到重新配置。|
|`get`|定義可擷取屬性值的存取子方法。|
|[in](https://msdn.microsoft.com/en-us/library/dd469484.aspx)|讓參數能夠接受泛型介面中較少衍生的類型。|
|[object](https://msdn.microsoft.com/en-us/library/9kkx3h3c.aspx)|.NET Framework 中 Object 型別的別名。|
|[out](https://msdn.microsoft.com/en-us/library/t3c3bfhx.aspx)|參數修飾詞或泛型類型參數宣告。|
|[override](https://msdn.microsoft.com/en-us/library/ebca9ah3.aspx)|擴充或修改繼承成員的實作。|
|[partial](https://msdn.microsoft.com/en-us/library/6b0scde8.aspx)|宣告要分割成多個檔案的定義，或從它的實作分割方法定義。|
|[readonly](https://msdn.microsoft.com/en-us/library/acdd6hb7.aspx)|宣告類別成員只能在宣告期間或透過類別建構函式加以指派。|
|[ref](https://msdn.microsoft.com/en-us/library/14akc2c7.aspx)|導致引數會以傳址方式傳遞，而不是依值傳遞。|
|[set](https://msdn.microsoft.com/en-us/library/ms228368.aspx)|定義可設定屬性值的存取子方法。|
|[string](https://msdn.microsoft.com/en-us/library/362314fe.aspx)|.NET Framework 中 String 型別的別名。|
|[struct](https://msdn.microsoft.com/en-us/library/ah19swz4.aspx)|封裝相關變數群組的實值類型。|
|[typeof](https://msdn.microsoft.com/en-us/library/58918ffs.aspx)|取得物件類型。|
|[var](https://msdn.microsoft.com/en-us/library/bb383973.aspx)|宣告隱含類型的區域變數。|
|[value](https://msdn.microsoft.com/en-us/library/a1khb4f8.aspx)|參考用戶端程式碼想要指派給屬性的值。|
|[virtual](https://msdn.microsoft.com/en-us/library/9fkccyh4.aspx)|允許在衍生類別中覆寫方法。|


<a name="interop" />

## <a name="interoperating-with-existing-java-code"></a>與現有 Java 程式碼交互操作

如果您目前具有不想轉換為 C# 的 Java 功能，您可以透過下列兩種技術，在 Xamarin.Android 應用程式中重複使用現有的 Java 程式庫：

-  **建立 Java 繫結庫** &ndash; 使用這種方法，您可以使用 Xamarin 工具來產生圍繞 Java 類型的 C# 包裝函式。 這些包裝函式稱為「繫結」。 如此一來，Xamarin.Android 應用程式就可藉由呼叫這些包裝函式來使用 *.jar* 檔案。

-  **Java 原生介面** &ndash; *Java 原生介面* (JNI) 是一種可讓 C# 應用程式呼叫或透過 Java 程式碼呼叫的架構。

如需這些技術的詳細資訊，請參閱 [Java 整合概觀](~/android/platform/java-integration/index.md)。



## <a name="for-further-reading"></a>進一步閱讀

MSDN [C# 程式設計手冊](https://msdn.microsoft.com/en-us/library/67ef8sbd.aspx)是開始學習 C# 程式設計語言的絕佳方法，而您可以使用 [C# 參考](https://msdn.microsoft.com/en-us/library/618ayhy6.aspx)來查閱特定的 C# 語言功能。

就像所具備的 Java 知識對於 Java 類別庫的熟悉程度，至少要與了解 Java 語言一樣，對於 C# 的實務知識需要對 .NET Framework 有某種程度的熟悉。 Microsoft 的[移至 C# 和 .NET Framework，適用於 Java 開發人員](https://www.microsoft.com/en-us/download/details.aspx?id=6073) \(英文\) 學習封包是從 Java 觀點深入了解 .NET Framework (同時還能更深入了解 C#) 的好方法。

當您準備好要在 C# 中處理第一個 Xamarin.Android 專案時，我們的 [Hello, Android](~/android/get-started/hello-android/index.md) 系列可協助您建置第一個 Xamarin.Android 應用程式，並進一步了解使用 Xamarin 進行 Android 應用程式開發的基本知識。



## <a name="summary"></a>總結

本文從 Java 開發人員的觀點介紹了 Xamarin.Android C# 程式設計環境。 它指出 C# 和 Java 之間的相似性，同時說明它們的實際差異。 它介紹了組件和命名空間、說明如何匯入外部類型，並提供存取修飾詞、泛型、類別衍生、呼叫基底類別方法、方法覆寫，以及事件處理中差異的概觀。 它介紹了無法在 Java 中使用的 C# 功能，例如，屬性、`async`/`await` 非同步程式設計、Lambda、C# 委派，以及 C# 事件處理系統。 其中包含重要 C# 關鍵字的表格、說明如何與現有的 Java 程式庫相互操作，並提供相關文件的連結以取得進一步研究。


## <a name="related-links"></a>相關連結

- [Java 整合概觀](~/android/platform/java-integration/index.md)
- [C# 程式設計指南](https://msdn.microsoft.com/en-us/library/67ef8sbd.aspx)
- [C# 參考](https://msdn.microsoft.com/en-us/library/618ayhy6.aspx)
- [移至 C# 和 .NET Framework，適用於 Java 開發人員](https://www.microsoft.com/en-us/download/details.aspx?id=6073)
