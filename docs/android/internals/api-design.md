---
title: Xamarin. Android API 設計原則
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2958e456aeb25ba39697ad82500d574907e963e4
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510754"
---
# <a name="xamarinandroid-api-design-principles"></a>Xamarin. Android API 設計原則

除了屬於 Mono 的核心基類庫, Xamarin 也隨附各種 Android Api 的系結, 可讓開發人員使用 Mono 建立原生 Android 應用程式。

就 Xamarin 的核心而言, 有一個 interop 引擎可將C#世界與 java 世界橋接, 讓開發人員可以從C#或其他 .Net 語言存取 java api。


## <a name="design-principles"></a>設計原則

這些是我們的一些 Xamarin Android 系結設計原則

-  符合 .NET Framework 的[設計方針](https://docs.microsoft.com/dotnet/standard/design-guidelines/)。

-  允許開發人員將 JAVA 類別設為子類別。

-  子類別應該搭配C#標準結構使用。

-  衍生自現有的類別。

-  呼叫基底函式來進行連鎖。

-  覆寫方法應使用C#的覆寫系統來完成。

-  讓常見的 JAVA 工作變得簡單, 而且可能會有困難的 JAVA 工作。

-  將 JavaBean 屬性公開C#為屬性。

-  公開強型別 API:

    - 增加型別安全。

    - 將執行階段錯誤降至最低。

    - 取得傳回類型的 IDE intellisense。

    - 允許 IDE 快顯視窗檔。

-  鼓勵在 IDE 中探索 Api:

    - 利用架構替代專案, 將 JAVA Classlib 的風險降到最低。

    - 適當C#和適用時, 公開委派 (lambda、匿名方法和 system.string), 而不是單一方法介面。

    - 提供呼叫任意 JAVA 程式庫 ( [JNIEnv](xref:Android.Runtime.JNIEnv)) 的機制。


## <a name="assemblies"></a>組件

Xamarin 包含許多構成*MonoMobile 設定檔*的元件。 [[元件](~/cross-platform/internals/available-assemblies.md)] 頁面包含詳細資訊。

Android 平臺的系結會包含在`Mono.Android.dll`元件中。 此元件包含用來使用 Android Api 和與 Android 執行時間 VM 通訊的整個系結。


## <a name="binding-design"></a>系結設計


### <a name="collections"></a>集合

Android Api 會廣泛利用 util 集合來提供清單、集合和對應。 我們會使用系結中的[system.object](xref:System.Collections.Generic)介面來公開這些元素。 基本對應如下:

-   [ util<E> ](https://developer.android.com/reference/java/util/Set.html)會對應到系統類型[<T>ICollection](xref:System.Collections.Generic.ICollection`1), helper 類別[JAVASet<T> ](xref:Android.Runtime.JavaSet`1)。

-   [ util<E> ](https://developer.android.com/reference/java/util/List.html)會對應至系統類型[<T>IList](xref:System.Collections.Generic.IList`1)、helper 類別[JAVAList<T> ](xref:Android.Runtime.JavaList`1)。

-   [util < K, v >](https://developer.android.com/reference/java/util/Map.html)對應到系統類型[IDictionary < TKey、TValue >](xref:System.Collections.Generic.IDictionary`2)、Helper class [Android. JAVADictionary < K、V >](xref:Android.Runtime.JavaDictionary`2)。

-   [ util<E> ](https://developer.android.com/reference/java/util/Collection.html)會對應到系統類型[<T>ICollection](xref:System.Collections.Generic.ICollection`1), helper 類別[JAVACollection<T> ](xref:Android.Runtime.JavaCollection`1)。

我們已提供 helper 類別, 以加速 copyless 這些類型的封送處理。 可能的話, 建議使用這些提供的集合, 而不是架構所提供的[`List<T>`](xref:System.Collections.Generic.List`1)實[`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2)作為, 例如或。 [Android. 運行](xref:Android.Runtime)時間會在內部利用原生 JAVA 集合, 因此在傳遞至 Android API 成員時, 不需要在原生集合中進行複製。

您可以將任何介面執行傳遞至接受該介面的 Android 方法, 例如將傳遞`List<int>`至[ArrayAdapter&lt; &gt;int (CoNtext, int, IList&lt; &gt;int)](xref:Android.Widget.ArrayAdapter`1)函數。 *不過*, 針對所有的執行, 除了 Android. 執行時間的部署*之外*, 這牽涉到將清單從 Mono vm*複製*到 android 執行時間 vm。 如果清單稍後在 Android 執行時間中變更 (例如, 藉由叫用[ArrayAdapter&lt;T&gt;)。Add (T)](xref:Android.Widget.ArrayAdapter`1.Add*)方法), 這些變更*將不*會顯示在 managed 程式碼中。 如果使用`JavaList<int>`了, 則會顯示這些變更。

改換措辭,*不*是上述**Helper 類別**之一的集合介面, 只會封送處理 [In]:

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

JAVA 方法會在適當時轉換成屬性:

-  JAVA 方法配對`T getFoo()`和`void setFoo(T)`會轉換為`Foo`屬性。 範例：[Activity. 意圖](xref:Android.App.Activity.Intent)。

-  JAVA 方法`getFoo()`會轉換成隻讀的 Foo 屬性。 範例：[PackageName](xref:Android.Content.Context.PackageName)。

-  不會產生僅限設定的屬性。

-  如果屬性類型會是陣列, 則*不*會產生屬性。



### <a name="events-and-listeners"></a>事件和接聽程式

Android Api 建置於 JAVA 之上, 而其元件遵循 JAVA 模式來連結事件接聽程式。 此模式通常很麻煩, 因為它需要使用者建立匿名類別並宣告要覆寫的方法, 例如, 這就是在 Android 中使用 JAVA 進行的動作:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

使用事件中C#的對等程式碼為:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

請注意, 這兩種機制都適用于 Xamarin。 您可以執行接聽程式介面, 並將它與 SetOnClickListener 連結, 或者您可以將透過任何一般C#架構建立的委派附加到 Click 事件。

當接聽程式回呼方法具有 void 傳回時, 我們會根據[EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1)委派來建立 API 元素。 我們會針對這些接聽程式類型產生類似上述範例的事件。 不過, 如果接聽程式回呼傳回非 void 和非**布林**值, 則不會使用事件和 EventHandlers。 我們會改為產生回呼簽章的特定委派, 並加入屬性, 而不是事件。 其原因是要處理委派調用順序和傳回處理。 這種方法會反映使用 Xamarin. iOS API 完成的作業。

C#只有在 Android 事件註冊方法時, 才會自動產生事件或屬性:

1. 具有前置詞, 例如[ *set*OnClickListener。](xref:Android.Views.View.SetOnClickListener*) `set`

1. `void`具有傳回類型。

1. 只接受一個參數, 參數類型為介面, 介面只有一個方法, 而介面名稱以結尾`Listener` , 例如[View OnClick ](xref:Android.Views.View.IOnClickListener)接聽程式。


此外, 如果接聽程式介面方法的傳回型別是**布林值**, 而不是**void**, 則產生的*EventArgs*子類別會包含已*處理*的屬性。 [已*處理* *]* 屬性的值會當做接聽程式方法的傳回值使用, 而且預設為`true`。

例如, Android [View. setOnKeyListener ()](xref:Android.Views.View.SetOnKeyListener*)方法會接受[OnKeyListener](xref:Android.Views.View.IOnKeyListener)介面, 而[OnKeyListener. onKey (view, int, KeyEvent)](xref:Android.Views.View.IOnKeyListener.OnKey*)方法則具有布林值傳回型別。 Xamarin 會產生對應的[&lt;EventHandler 視圖&gt; ](xref:Android.Views.View.KeyEventArgs)。 [KeyPress](xref:Android.Views.View.KeyPress)事件, 也就是 system.windows.forms.keyeventargs.handled。
*System.windows.forms.keyeventargs.handled*類別接著具有[System.windows.forms.keyeventargs.handled. 已處理](xref:Android.Views.View.KeyEventArgs.Handled)的屬性, 它會用來當做*OnKeyListener onKey ()* 方法的傳回值。

我們想要為其他方法和 ctor 新增多載, 以公開以委派為基礎的連接。 此外, 具有多個回呼的接聽程式需要進行一些額外的檢查, 以判斷是否可合理執行個別的回呼, 因此我們會將它們轉換為已識別。 如果沒有對應的事件, 則必須在中使用接聽C#程式, 但請將您認為可能會有委派使用方式的任何人都帶到我們的注意力。 我們也在不使用 "接聽程式" 後置詞的情況下完成了某些介面的轉換, 因為這會讓委派替代方案受益。

所有接聽程式介面都會執行[`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
介面, 因為系結的執行詳細資料, 所以接聽程式類別必須執行此介面。 這可以藉由在[java](xref:Java.Lang.Object)的子類別或任何其他已包裝的 java 物件 (例如 Android 活動) 上執行接聽程式介面來完成。


### <a name="runnables"></a>Runnables

JAVA 會利用[java](xref:Java.Lang.Runnable)可執行介面來提供委派機制。 這個介面的重要取用者是由[java. Thread](xref:Java.Lang.Thread)類別所組成。 Android 也在 API 中採用介面。
[RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*)和[View.post ()](xref:Android.Views.View.Post*)都是值得注意的範例。

介面包含單一 void 方法, [run ()。](xref:Java.Lang.Runnable.Run) `Runnable` 因此, 它會在中C#以[Action](xref:System.Action)委派的形式來系結。 我們在系結中提供多載, 此`Action`系結會接受在原生 API `Runnable`中使用的所有 API 成員的參數, 例如[RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*)和[View.Post ()](xref:Android.Views.View.Post*)。

我們會保留[IRunnable](xref:Java.Lang.IRunnable)多載, 而不是取代它們, 因為有數個類型會實作為介面, 因此可以直接傳遞為 runnables。


### <a name="inner-classes"></a>內部類別

JAVA 有兩種不同類型的[嵌套類別](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): 靜態的嵌套類別和非靜態類別。

JAVA 靜態嵌套類別與C#巢狀型別相同。

非靜態的嵌套類別 (也稱為*內部類別*) 的差異很大。 它們包含其封入類型之實例的隱含參考, 而且不能包含靜態成員 (在此總覽的範圍以外的其他差異)。

當系結和C#使用時, 會將靜態的嵌套類別視為一般的巢狀型別。 同時, 內部類別有兩個顯著的差異:

1. 包含類型的隱含參考必須明確地提供做為函數參數。

1. 從內部類別繼承時, 內部類別*必須*嵌套在繼承自基底內部類別之包含類型的類型內, 而且衍生類型必須提供與C#包含類型相同之類型的函式。

例如, 請考慮[WallpaperService. Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine)內部類別。 因為它是內部類別, 所以[WallpaperService](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor)會使用[WallpaperService](xref:Android.Service.Wallpaper.WallpaperService)實例的參考 (比較和與不採用任何參數的 JAVA WallpaperService 函式)。

內部類別的範例衍生是 CubeWallpaper. CubeEngine:

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

請注意`CubeWallpaper.CubeEngine` , 如何在`CubeWallpaper`中`CubeWallpaper`嵌套、繼承自的包含`WallpaperService.Engine`類別, `CubeWallpaper.CubeEngine` `CubeWallpaper`以及具有接受宣告類型的函式--在此案例中為--全部如上面所指定。

### <a name="interfaces"></a>介面

JAVA 介面可以包含三組成員, 其中兩個會造成的問題C#:

1. 方法

1. 型別

1. 欄位

JAVA 介面會轉譯成兩種類型:

1. 包含方法宣告的 (選擇性) 介面。 這個介面與 JAVA 介面具有相同的名稱,*但*它也有 ' *I* ' 前置詞。

1. (選擇性) 靜態類別, 其中包含在 JAVA 介面中宣告的任何欄位。

嵌套的類型會「重新置放」為封閉介面的同級, 而不是巢狀型別, 並以內含的介面名稱作為前置詞。

例如, 請考慮[Parcelable](xref:Android.OS.Parcelable)介面。
*Parcelable*介面包含方法、巢狀型別和常數。 *Parcelable*介面方法會放入[IParcelable](xref:Android.OS.IParcelable)介面中。
*Parcelable*介面常數會放入[ParcelableConsts](xref:Android.OS.ParcelableConsts)類型中。 因為泛型支援的限制, 所以 Parcelable 目前未系結[&lt;](https://developer.android.com/reference/android/os/Parcelable.Creator.html)的 nested [ &lt;ClassLoaderCreator > t](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) > 類型; 如果支援, 則為, 否則為。會以*IParcelableClassLoaderCreator*和*IParcelableCreator*介面的形式呈現。 例如, 嵌套的[IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html)介面會系結為[IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient)介面。

> [!NOTE]
> 從 Xamarin 1.9 開始, JAVA 介面常數是為了簡化移植 JAVA 程式碼而_複製_的。 這有助於改善依賴[android 提供者](https://developer.android.com/reference/android/provider/package-summary.html)介面常數的 JAVA 程式碼移植。

除了上述類型之外, 還有四項進一步的變更:

1. 會產生與 JAVA 介面名稱相同的類型, 以包含常數。

1. 包含介面常數的類型也會包含來自已實作為 JAVA 介面的所有常數。

1. 所有實作為包含常數之 JAVA 介面的類別都會取得新的 nested InterfaceConsts 型別, 其中包含所有實作為介面的常數。

1. *成本*類型現在已過時。


就*Parcelable*介面而言, 這表示現在會有[*Parcelable*](xref:Android.OS.Parcelable)類型可包含常數的型別。 例如, [Parcelable. CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR)常數將系結為[*Parcelable. ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor)常數, 而非*ParcelableConsts*常數。

如果是包含常數的介面, 而這些常數會執行包含更多常數的其他介面, 則現在會產生所有常數的聯集。 例如, MediaStore 介面會實作為[MediaStore MediaColumns](xref:Android.Provider.MediaStore.MediaColumns)介面的範例。 [VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html)介面。 不過, 在1.9 之前, [VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts)類型無法存取在 MediaStore 上宣告的常數[。 MediaStore. MediaColumnsConsts.](xref:Android.Provider.MediaStore.MediaColumnsConsts)。
因此, JAVA 運算式*MediaStore*必須系結至 MediaStore 的C#運算式, 而不需閱讀許多 JAVA 檔, 就難以探索該*標題*的 VideoColumns。 在1.9 中, 對C#等的運算式會是[MediaStore。 VideoColumns. Title](xref:Android.Provider.MediaStore.Video.VideoColumns.Title)。

此外, 請考慮用來執行 JAVA *Parcelable*介面的「 [android.](xref:Android.OS.Bundle) 」組合類型。 由於它會實作為介面, 因此該介面上的所有常數都可以透過組合類型 (例如組合) 來存取。 *CONTENTS_FILE_DESCRIPTOR*是完全有效的 JAVA 運算式。
之前, 若要將此運算式C#移植至, 您必須查看所有介面, 這些介面會實作為從*CONTENTS_FILE_DESCRIPTOR*來源的類型來查看。 從 Xamarin. Android 1.9 開始, 實作為包含常數的 JAVA 介面的類別將會有一個嵌套的*InterfaceConsts*類型, 其中包含所有繼承的介面常數。 這可讓您將組合*CONTENTS_FILE_DESCRIPTOR*到[*InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor)。

最後, 具有*成本*尾碼 (如*ParcelableConsts* ) 的類型現在已過時, 但新引進的 InterfaceConsts 巢狀型別除外。 它們將會在 Xamarin. Android 3.0 中移除。


## <a name="resources"></a>資源

影像、版面配置描述、二進位 blob 和字串字典可以包含在應用程式中做為[資源檔](https://developer.android.com/guide/topics/resources/providing-resources.html)。
各種 Android Api 的設計目的是要[在資源識別碼上運作](https://developer.android.com/guide/topics/resources/accessing-resources.html), 而不是直接處理影像、字串或二進位 blob。

例如, 包含使用者介面配置 ( `main.axml`)、國際化資料表字串 ( `strings.xml`) 和一些圖示 ( `drawable-*/icon.png`) 的 Android 應用程式範例, 會將其資源保留在應用程式的 "resources" 目錄中:

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

原生 Android Api 不會直接與檔案名一起運作, 而是會在資源識別碼上操作。 當您編譯使用資源的 Android 應用程式時, 組建系統會封裝要散發的資源, 並產生名`Resource`為的類別, 其中包含每個所含資源的權杖。 例如, 針對上述資源版面配置, R 類別會公開:

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

接著, 您可以`Resource.Drawable.icon`使用來`drawable/icon.png`參考檔案`layout/main.xml` , 或`Resource.Layout.main`參考檔案`values/strings.xml`, 或`Resource.String.first_string`參考字典檔案中的第一個字串。


## <a name="constants-and-enumerations"></a>常數和列舉

原生 Android 應用程式開發介面具有許多方法, 可接受或傳回必須對應至常數位段的 int, 以判斷 int 所代表的意義。 若要使用這些方法, 使用者必須查閱檔, 以查看哪些常數是適當的值, 這不是理想的選擇。

例如, 請考慮[requestWindowFeature (Int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在這些情況下, 我們會努力將相關的常陣列成一個 .NET 列舉, 然後重新對應方法以改為採用列舉。
如此一來, 我們就能夠提供 IntelliSense 選取可能的值。

上述範例會變成:[RequestWindowFeature (WindowFeatures featureId)](xref:Android.App.Activity.RequestWindowFeature*)。

請注意, 這是非常手動的程式, 用來找出哪些常數是在一起, 以及哪些 Api 會使用這些常數。 請針對 API 中使用的任何常數提出 bug, 這會更好地表示為列舉型別。
