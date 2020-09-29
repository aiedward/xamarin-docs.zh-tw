---
title: Xamarin. Android API 設計原則
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: bb9dadd388a514f18abdaf6e6db6d76f0be99f86
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454243"
---
# <a name="xamarinandroid-api-design-principles"></a>Xamarin. Android API 設計原則

除了屬於 Mono 的核心基類庫之外，Xamarin 還隨附各種 Android Api 的系結，可讓開發人員使用 Mono 建立原生 Android 應用程式。

就 Xamarin 的核心而言，有一個 interop 引擎可將 c # 世界與 JAVA 世界橋接，讓開發人員可以從 c # 或其他 .NET 語言存取 JAVA Api。

## <a name="design-principles"></a>設計原則

以下是一些適用于 Xamarin. Android 系結的設計原則

- 符合 .NET Framework 的  [設計指導方針](/dotnet/standard/design-guidelines/)。

- 允許開發人員將 JAVA 類別設為子類別。

- 子類別應使用 c # 標準結構。

- 衍生自現有的類別。

- 呼叫基底的函式以進行鏈。

- 覆寫方法應該使用 c # 的覆寫系統來完成。

- 讓一般的 JAVA 工作變得容易，並可讓您進行難以進行的 JAVA 工作。

- 將 JavaBean 屬性公開為 c # 屬性。

- 公開強型別 API：

  - 提高型別安全。

  - 將執行階段錯誤降至最低。

  - 在傳回類型上取得 IDE intellisense。

  - 允許 IDE 快顯視窗檔。

- 鼓勵以 IDE 探索 Api：

  - 利用架構替代方案來將 JAVA Classlib 曝光降至最低。

  - 公開 c # 委派 (lambda、匿名方法和 System. 委派) ，而不是單一方法介面（如果適用）。

  - 提供一種機制， ( [JNIEnv](xref:Android.Runtime.JNIEnv)) 呼叫任意的 JAVA 程式庫。

## <a name="assemblies"></a>組件

Xamarin 包含許多組成 *MonoMobile 設定檔*的元件。 [ [元件](~/cross-platform/internals/available-assemblies.md) ] 頁面有詳細資訊。

Android 平臺的系結會包含在元件中 `Mono.Android.dll` 。 此元件包含用來取用 Android Api 以及與 Android runtime VM 通訊的整個系結。

## <a name="binding-design"></a>系結設計

### <a name="collections"></a>集合

Android Api 廣泛使用 util 集合來提供清單、集合和地圖。 我們會使用系結中的 [泛型](xref:System.Collections.Generic) 介面公開這些元素。 基本對應如下：

- [util \<E> ](https://developer.android.com/reference/java/util/Set.html)會對應到系統類型[ICollection \<T> ](xref:System.Collections.Generic.ICollection`1)，helper 類別[JAVASet \<T> ](xref:Android.Runtime.JavaSet`1)。

- [util \<E> ](https://developer.android.com/reference/java/util/List.html)會對應到系統類型[IList \<T> ](xref:System.Collections.Generic.IList`1)，helper 類別[JAVAList \<T> ](xref:Android.Runtime.JavaList`1)。

- [util 對應<K，v>](https://developer.android.com/reference/java/util/Map.html) 對應至系統型別 [IDictionary<TKey、tvalue>>](xref:System.Collections.Generic.IDictionary`2)、Helper 類別 [JAVADictionary<K、V>](xref:Android.Runtime.JavaDictionary`2)。

- [util \<E> ](https://developer.android.com/reference/java/util/Collection.html)會對應到系統類型[ICollection \<T> ](xref:System.Collections.Generic.ICollection`1)，helper 類別[JAVACollection \<T> ](xref:Android.Runtime.JavaCollection`1)。

我們已提供協助程式類別，有助於更快速地 copyless 這些類型的封送處理。 可能的話，我們建議使用這些提供的集合，而不是架構所提供的實作為 [`List<T>`](xref:System.Collections.Generic.List`1) 或 [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2) 。 [Android. 運行](xref:Android.Runtime)時間會在內部使用原生 JAVA 集合，因此在傳遞至 Android API 成員時，不需要從原生集合進行複製。

您可以將任何介面實作為接受該介面的 Android 方法傳遞，例如將傳遞 `List<int>` 給 [>arrayadapter &lt; int &gt; (CoNtext、int、IList &lt; int &gt;) ](xref:Android.Widget.ArrayAdapter`1) 的函式。 *不過*，對於 Android. 執行時間執行 *以外* 的所有執行作業，這牽涉到將清單從 Mono vm *複製* 到 Android runtime vm。 如果清單稍後在 Android 執行時間中變更 (例如叫用 [>arrayadapter &lt; T &gt; 。新增 (T) ](xref:Android.Widget.ArrayAdapter`1.Add*) 方法) ，這些變更 *將不* 會顯示在 managed 程式碼中。 如果 `JavaList<int>` 已使用，則會顯示這些變更。

改換措辭， *不* 屬於上述任何一個 **Helper 類別**的集合介面實址只能封送處理 [In]：

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```

### <a name="properties"></a>屬性

JAVA 方法會在適當時轉換成屬性：

- JAVA 方法配對 `T getFoo()` 和 `void setFoo(T)` 會轉換成 `Foo` 屬性。 範例： [Activity. 意圖](xref:Android.App.Activity.Intent)。

- JAVA 方法 `getFoo()` 會轉換成隻讀 Foo 屬性。 範例： [PackageName](xref:Android.Content.Context.PackageName)。

- 不會產生僅限設定的屬性。

- 如果屬性型別是陣列，則 *不* 會產生屬性。

### <a name="events-and-listeners"></a>事件和接聽程式

Android Api 是以 JAVA 為基礎建立的，其元件遵循 JAVA 模式來連結事件接聽程式。 這種模式通常很麻煩，因為它需要使用者建立匿名類別，並宣告要覆寫的方法，例如，這是在 Android 中使用 JAVA 完成作業的方式：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

使用事件的 c # 中的對等程式碼為：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

請注意，上述兩種機制都可搭配 Xamarin 使用。 您可以執行接聽程式介面，並將它附加至 SetOnClickListener，或者您可以將透過任何一般 c # 架構建立的委派附加至 Click 事件。

當接聽程式回呼方法有 void 傳回時，我們會根據[EventHandler &lt; TEventArgs &gt; ](xref:System.EventHandler`1)委派來建立 API 元素。 我們會針對這些接聽程式類型產生類似上述範例的事件。 但是，如果接聽程式回呼傳回非 void 和非 **布林** 值，就不會使用事件和 EventHandlers。 相反地，我們會針對回呼的簽章產生特定的委派，並新增屬性而不是事件。 原因是要處理委派調用順序和傳回處理。 這種方法會反映對 Xamarin API 所做的工作。

C # 事件或屬性只有在 Android 事件註冊方法時才會自動產生：

1. 具有 `set` 前置詞，例如[ *set*OnClickListener](xref:Android.Views.View.SetOnClickListener*)。

1. 具有傳回 `void` 型別。

1. 只接受一個參數，參數類型是介面，介面只有一個方法，而介面名稱結尾為 `Listener` ，例如 [View. OnClick * *](xref:Android.Views.View.IOnClickListener)接聽程式。

此外，如果接聽程式介面方法的傳回類型為 **布林值** ，而不是 **void**，則產生的 *EventArgs* 子類別將包含已 *處理* 的屬性。 已 *處理* 的屬性值會用來做為接聽 *程式方法的* 傳回值，並預設為 `true` 。

例如，Android [setOnKeyListener ( # B1 ](xref:Android.Views.View.SetOnKeyListener*) 方法接受 [OnKeyListener](xref:Android.Views.View.IOnKeyListener) 介面，而 [OnKeyListener. onKey (View，int，KeyEvent) ](xref:Android.Views.View.IOnKeyListener.OnKey*) 方法則具有布林值傳回型別。 Xamarin 會產生對應的[view。 KeyPress](xref:Android.Views.View.KeyPress)事件，也就是[EventHandler &lt; View. system.windows.forms.keyeventargs.handled &gt; ](xref:Android.Views.View.KeyEventArgs)。
*System.windows.forms.keyeventargs.handled*類別接著有[system.windows.forms.keyeventargs.handled. 處理](xref:Android.Views.View.KeyEventArgs.Handled)的屬性，它是用來做為*OnKeyListener. onKey ( # B1*方法的傳回值。

我們打算新增其他方法和 ctor 的多載，以公開以委派為基礎的連接。 此外，具有多個回呼的接聽程式需要進行一些額外的檢查，以判斷執行個別回呼是否合理，因此我們會在識別這些回呼時進行轉換。 如果沒有對應的事件，就必須在 c # 中使用接聽程式，但請將您認為可能會有的任何專案委派給我們的注意。 我們也會在沒有「接聽程式」尾碼的情況下，對介面進行一些轉換，因為它們很清楚，可從委派替代方法獲益。

所有接聽程式介面都會執行 [`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
介面，因為系結的實作為詳細資料，所以接聽程式類別必須執行這個介面。 這可以藉由在 [JAVA](xref:Java.Lang.Object) 的子類別或任何其他包裝的 java 物件（例如 Android 活動）上執行接聽程式介面來完成。

### <a name="runnables"></a>Runnables

JAVA 會利用 [java.](xref:Java.Lang.Runnable) 可執行檔介面來提供委派機制。 這個介面有很 [明顯的取用](xref:Java.Lang.Thread) 者。 Android 在 API 中也採用了介面。
[RunOnUiThread ( # B1 ](xref:Android.App.Activity.RunOnUiThread*) 和 [View.post ( # B3 ](xref:Android.Views.View.Post*) 是值得注意的範例。

`Runnable`介面包含單一 void 方法， [ ( # B1 執行](xref:Java.Lang.Runnable.Run)。 因此，它本身就可以在 c # 中以 [Action](xref:System.Action) 委派的方式進行系結。 我們已在系結中提供多載，以接受 `Action` 在原生 API 中使用的所有 API 成員的參數 `Runnable` ，例如 [RunOnUiThread ( # B1 ](xref:Android.App.Activity.RunOnUiThread*) 和 [View.Post ( # B3 ](xref:Android.Views.View.Post*)。

我們已保留 [IRunnable](xref:Java.Lang.IRunnable) 多載，而不是取代它們，因為許多型別會實作為介面，因此可以直接傳遞為 runnables。

### <a name="inner-classes"></a>內部類別

JAVA 有兩種不同類型的 [嵌套類別](https://download.oracle.com/javase/tutorial/java/javaOO/nested.html)：靜態的嵌套類別和非靜態類別。

JAVA 靜態嵌套類別與 c # 巢狀型別相同。

非靜態的嵌套類別（也稱為 *內部類別*）明顯不同。 它們包含其封入類型之實例的隱含參考，且不能包含靜態成員， (在此總覽) 範圍之外的其他差異。

當系結和 c # 使用時，會將靜態的嵌套類別視為一般的巢狀型別。 同時，內部類別具有兩個顯著的差異：

1. 包含類型的隱含參考必須明確提供為函式參數。

1. 從內部類別繼承時，內部類別 *必須* 在繼承自基底內部類別之包含型別的型別內，而且衍生型別必須提供與 c # 包含型別相同型別的函式。

例如，請考慮 [WallpaperService Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine) 內部類別。 因為它是內部類別，所以 [WallpaperService ( # A1](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) 函式會採用 [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) 實例的參考， (比較和對比 JAVA WallpaperService ( # A4 的函式，) 不接受任何參數。

內部類別的衍生範例為 CubeWallpaper. CubeEngine：

```csharp
class CubeWallpaper : WallpaperService {
    public override WallpaperService.Engine OnCreateEngine ()
    {
        return new CubeEngine (this);
    }

    class CubeEngine : WallpaperService.Engine {
        public CubeEngine (CubeWallpaper s)
                : base (s)
        {
        }
    }
}
```

請注意，在 `CubeWallpaper.CubeEngine` 中 `CubeWallpaper` 的內嵌方式、 `CubeWallpaper` 繼承自的包含類別 `WallpaperService.Engine` 、以及具有接受宣告類型的函式（ `CubeWallpaper.CubeEngine` `CubeWallpaper` 在此案例中為），如上面所指定。

### <a name="interfaces"></a>介面

JAVA 介面可以包含三組成員，其中兩個會造成 c # 的問題：

1. 方法

1. 類型

1. 欄位

JAVA 介面會轉譯成兩種類型：

1.  (選用的) 介面，其中包含方法聲明。 此介面與 JAVA 介面具有相同的名稱， *但* 它也有 ' *I* ' 前置詞。

1.  (選擇性) 靜態類別，其中包含在 JAVA 介面內宣告的任何欄位。

巢狀型別會「重新置放」為封閉介面的同級（而不是巢狀型別），並以封入介面名稱作為前置詞。

例如，請考慮 [Parcelable](xref:Android.OS.Parcelable) 介面。
*Parcelable*介面包含方法、巢狀型別和常數。 *Parcelable*介面方法會放入[IParcelable](xref:Android.OS.IParcelable)介面中。
*Parcelable*介面常數會放入[ParcelableConsts](xref:Android.OS.ParcelableConsts)類型中。 由於泛型支援的限制， [Parcelable 和 ClassLoaderCreator \<T> ](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)和[ \<T> Parcelable](https://developer.android.com/reference/android/os/Parcelable.Creator.html)的巢狀型別目前未系結; 如果支援，則會以*IParcelableClassLoaderCreator*和*android. IParcelableCreator*介面的形式出現在這些型別中。 例如， [IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) 介面會系結為 [IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) 介面（.）。

> [!NOTE]
> 從 Xamarin. Android 1.9 開始，JAVA 介面常數會不斷 _複製_ 以簡化 java 程式碼的移植。 這有助於改善依賴 [android 提供者](https://developer.android.com/reference/android/provider/package-summary.html) 介面常數的移植 JAVA 程式碼。

除了上述類型之外，還有四項進一步的變更：

1. 會產生與 JAVA 介面同名的型別，以包含常數。

1. 包含介面常數的型別也包含來自已實 JAVA 介面的所有常數。

1. 所有實作為包含常數之 JAVA 介面的類別，都會得到新的嵌套 InterfaceConsts 型別，其中包含所有實介面中的常數。

1. *成本*類型現在已過時。

若為 *Parcelable* 介面，這表示現在將會有 [*Parcelable*](xref:Android.OS.Parcelable) 類型可包含這些常數。 例如， [Parcelable.CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) 常數將會系結為 [*Parcelable. ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor) 常數，而不是 *ParcelableConsts. ContentsFileDescriptor* 常數。

如果介面包含的常數會實作為其他包含更多常數的介面，現在就會產生所有常數的聯集。 例如， [MediaStore](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) 介面會實作為 [MediaStore. MediaColumns 介面的](xref:Android.Provider.MediaStore.MediaColumns) 介面。 不過，在1.9 之前， [MediaStore 的 VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) 類型無法存取在 MediaStore 上宣告的常數。 [. MediaColumnsConsts](xref:Android.Provider.MediaStore.MediaColumnsConsts)。
如此一來，JAVA 運算式 *MediaStore* 就必須系結至 c # 運算式 *MediaStore* ，而不需要讀取許多 JAVA 檔，就很難探索。 在1.9 中，對等的 c # 運算式將會是 [*MediaStore。 VideoColumns. Title*](xref:Android.Provider.MediaStore.Video.VideoColumns.Title)。

此外，請考慮用來實行 JAVA *Parcelable*介面的[android.](xref:Android.OS.Bundle)組合類型。 由於它會執行介面，因此該介面上的所有常數都可透過套件組合類型來存取，例如 *Bundle.CONTENTS_FILE_DESCRIPTOR* 是完全有效的 JAVA 運算式。
之前，若要將此運算式移植到 c #，您需要查看所有實作為的介面，以查看 *CONTENTS_FILE_DESCRIPTOR* 來自哪個型別。 從 Xamarin. Android 1.9 開始，實作為包含常數之 JAVA 介面的類別會有一個嵌套 *InterfaceConsts* 型別，其中將包含所有繼承的介面常數。 這會允許將 *Bundle.CONTENTS_FILE_DESCRIPTOR* 轉譯為 [*InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor)。

最後， *成本* 後置詞（例如 *ParcelableConsts* ）的型別現在已過時，除了新引進的 InterfaceConsts 巢狀型別之外。 它們將會在 Xamarin. Android 3.0 中移除。

## <a name="resources"></a>資源

影像、配置描述、二進位 blob 和字串字典可以作為 [資源檔](https://developer.android.com/guide/topics/resources/providing-resources.html)包含在您的應用程式中。
不同的 Android Api 是設計來 [在資源識別碼上操作](https://developer.android.com/guide/topics/resources/accessing-resources.html) ，而不是直接處理影像、字串或二進位 blob。

例如，包含使用者介面配置的範例 Android 應用程式 ( `main.axml`) 、國際化資料表字串 ( `strings.xml`) 以及某些圖示 ( `drawable-*/icon.png`) 會將其資源保留在應用程式的「資源」目錄中：

```
Resources/
    drawable-hdpi/
        icon.png

    drawable-ldpi/
        icon.png

    drawable-mdpi/
        icon.png

    layout/
        main.axml

    values/
        strings.xml
```

原生 Android Api 不會直接操作檔案名，而是會在資源識別碼上運作。 當您編譯使用資源的 Android 應用程式時，組建系統會封裝資源以供散發，並產生名 `Resource` 為的類別，其中包含每個包含之資源的權杖。 例如，針對上述資源版面配置，R 類別會公開：

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

然後，您可以使用 `Resource.Drawable.icon` 參考檔案 `drawable/icon.png` ，或參考檔案 `Resource.Layout.main` `layout/main.xml` ，或 `Resource.String.first_string` 參考字典檔案中的第一個字串 `values/strings.xml` 。

## <a name="constants-and-enumerations"></a>常數和列舉

原生 Android Api 有許多方法，這些方法會採用或傳回必須對應至常數位段的 int，以判斷 int 代表的意義。 若要使用這些方法，使用者必須參考檔，以查看哪些常數是適當的值（小於理想的值）。

例如，請考慮 [requestWindowFeature (Int featureID) ](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在這些情況下，我們會努力將相關的常陣列合成 .NET 列舉，然後重新對應方法以改為採用列舉。
如此一來，我們就能夠提供 IntelliSense 選取可能的值。

上述範例會變成： [RequestWindowFeature (WindowFeatures featureId) ](xref:Android.App.Activity.RequestWindowFeature*)。

請注意，這是一個非常手動的程式，可找出哪些常數是在一起，而哪些 Api 會使用這些常數。 請針對 API 中使用的任何常數提出 bug，以做為列舉。