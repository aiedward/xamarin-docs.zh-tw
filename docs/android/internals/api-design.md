---
title: Xamarin.Android API 設計原則
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 54479a7ed66c83d1d97d51cc93e3df3241ec740f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207930"
---
# <a name="xamarinandroid-api-design-principles"></a>Xamarin.Android API 設計原則


## <a name="overview"></a>總覽

除了核心屬於 Mono 的基底類別庫，Xamarin.Android 會隨附針對各種 Android Api，可讓開發人員建立與 Mono 的原生 Android 應用程式的繫結。

Xamarin.Android 的核心有是 interop 引擎與 Java 全世界該橋接器的 C# 世界，從 C# 或其他.NET 語言撰寫 Java api 提供開發人員存取。


## <a name="design-principles"></a>設計原則

以下是一些我們 Xamarin.Android 繫結的設計原則

-  符合[.NET Framework 設計方針](https://docs.microsoft.com/dotnet/standard/design-guidelines/)。

-  讓開發人員子類別化的 Java 類別。

-  子類別應該使用 C# 標準建構。

-  從現有的類別衍生。

-  呼叫基底建構函式鏈結。

-  覆寫方法應該使用 C# 的覆寫系統。

-  讓通用的 Java 工作更容易，且硬碟的 Java 工作的可能。

-  將 JavaBean 屬性公開為 C# 屬性。

-  公開強類型的 API:

    - 增加型別安全。

    - 執行階段錯誤降至最低。

    - 取得 IDE intellisense 傳回型別。

    - 允許 IDE 快顯視窗的文件。

-  建議 Api 的 IDE 中瀏的覽：

    - 利用最小化的 Java Classlib 曝光的 Framework 替代方案。

    - 當適當且適用於會公開 C# 委派 (lambda、 匿名方法和 System.Delegate) 而不使用單一方法的介面。

    - 提供機制來呼叫任意的 Java 程式庫 ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/))。


## <a name="assemblies"></a>組件

Xamarin.Android 包含組成組件數*MonoMobile 設定檔*。 [組件](~/cross-platform/internals/available-assemblies.md)頁面具有的詳細資訊。

中所包含的繫結以 Android 平台`Mono.Android.dll`組件。 這個組件包含使用 Android Api 的整個繫結，而且與 Android 執行階段的虛擬機器通訊。


## <a name="binding-design"></a>繫結設計


### <a name="collections"></a>集合

Android Api 會利用廣泛用來提供清單、 集合和對應的 java.util 集合。 我們將使用這些元素時，公開[System.Collections.Generic](xref:System.Collections.Generic)我們繫結中的介面。 基本的對應如下：

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html)對應至系統型別[ICollection<T>](xref:System.Collections.Generic.ICollection`1)，協助程式類別[Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/)。

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html)對應至系統型別[IList<T>](xref:System.Collections.Generic.IList`1)，協助程式類別[Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/)。

-   [< K，V > java.util.Map](http://developer.android.com/reference/java/util/Map.html)對應至系統型別[IDictionary < TKey，TValue >](xref:System.Collections.Generic.IDictionary`2)，協助程式類別[Android.Runtime.JavaDictionary < K，V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/)。

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html)對應至系統型別[ICollection<T>](xref:System.Collections.Generic.ICollection`1)，協助程式類別[Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/)。

我們提供協助程式類別，以便更快 copyless 封送處理這些型別。 如果可能的話，我們建議使用這些提供而不是提供架構實作的集合，像是[ `List<T>` ](xref:System.Collections.Generic.List`1)或是[ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2)。 [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)實作在內部利用原生 Java 集合，因此不需要往返複製原生集合傳遞給 Android API 成員時。

您可以傳遞至 Android 方法接受該介面的任何介面實作，例如傳遞`List<int>`要[ArrayAdapter&lt;int&gt;(內容、 int、 IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)建構函式。 *不過*，為所有實作*除了*Android.Runtime 實作，這牽涉到*複製*Android 執行階段的虛擬機器將 Mono VM 的清單。 如果清單是更新 Android 執行階段中的變更 (例如，藉由叫用[ArrayAdapter&lt;T&gt;。Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/)方法)，這些變更*不會*會顯示在 managed 程式碼。 如果`JavaList<int>`所使用，這些變更都能看見。

改換措辭，集合介面的實作會*未*上述其中一項列出**協助程式類別**es 只封送處理 [In]:

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

Java 方法會轉換成時適當的屬性：

-  Java 方法配對`T getFoo()`並`void setFoo(T)`轉換成`Foo`屬性。 範例：[Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/)。

-  下列 Java 方法`getFoo()`轉換成 [唯讀] Foo 屬性。 範例：[Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/)。

-  僅限集合屬性不會產生。

-  屬性是*不*產生如果屬性型別陣列。



### <a name="events-and-listeners"></a>事件和接聽程式

Android Api 以 Java 為基礎和其元件會遵循連結事件接聽程式的 Java 模式。 此模式通常會很麻煩，因為它需要使用者建立匿名類別，並將覆寫方法宣告，比方說，這是如何項目會在 Android 中使用 Java 完成：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

會使用事件以 C# 對等的程式碼：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

請注意，這兩個以上的機制適用於 Xamarin.Android。 您可以實作接聽程式介面，並將它附加與 View.SetOnClickListener，或者您可以附加建立透過任何一般 C# 範例的 Click 事件的委派。

當接聽程式回呼方法具有 void 傳回時，我們會建立為基礎的 API 元素[事件處理常式&lt;TEventArgs&gt; ](xref:System.EventHandler`1)委派。 我們產生的事件，例如上述範例中為這些接聽程式類型。 不過，如果接聽程式回撥會傳回非 void 的和非位**布林**不會使用值、 事件和事件處理常式。 相反地，我們會產生特定的委派簽章的回呼，新增屬性，而不是事件。 原因是為了應付委派引動過程順序，並傳回處理。 這個方法會反映使用 Xamarin.iOS API 做些什麼。

如果 C# 事件或屬性將會只會自動產生的 Android 的事件註冊方法：

1. 已`set`前置詞，例如[*設定*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/)。

1. 具有`void`傳回型別。

1. 只接受一個參數，參數類型是介面、 此介面有只有一個方法，和介面名稱結尾`Listener`，例如[View.OnClick*接聽程式*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/)。


此外，如果接聽程式介面的方法有傳回型別**布林**而不是**void**，然後產生*EventArgs*子類別將包含*處理*屬性。 值*Handled*屬性做為傳回值，如*接聽程式*方法，且其預設值為`true`。

例如，Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/)方法接受[View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener)介面，而[View.OnKeyListener.onKey （檢視、 int、 KeyEvent）](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/)方法有布林的傳回型別。 Xamarin.Android 會產生相對應[View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)事件，這是[事件處理常式&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/)。
*KeyEventArgs*反過來有個類別[View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)屬性，用來作為傳回值*View.OnKeyListener.onKey()* 方法。

我們想要新增的其他方法和公開的委派基礎連接的 ctor 多載。 此外，多個回呼的接聽程式需要一些額外的檢驗，以決定是否實作個別的回撥是合理的因此我們會將轉換成這些所識別。 如果沒有任何對應的事件，接聽程式必須使用在 C# 中，但請將任何您認為可能會有委派的使用量，對我們的注意。 清除獲益的委派替代項目時，我們也已完成介面，而不需要 「 接聽程式 」 後置詞的某些的轉換。

所有接聽程式的介面實作 [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
「 介面 」，因為繫結，因此接聽程式的類別必須實作此介面的實作詳細資料。 這可以藉由實作的子類別上的接聽程式介面[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)或任何其他包裝 Java 物件，例如 Android 活動。


### <a name="runnables"></a>Runnables

Java 會運用[java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/)介面，以提供委派的機制。 [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/)類別是值得注意的取用者，此介面。 Android 採用 API 以及中的介面。
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/)並[View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable)是值得注意的範例。

`Runnable`介面會包含單一的 void 方法[run （)](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/)。 它因此本身即可在 C# 做為繫結[System.Action](xref:System.Action)委派。 我們提供的繫結中可接受的多載`Action`所有的 API 成員使用的參數`Runnable`在原生 API 中，例如[Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action))和[View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

講[IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/)直接多載，而不是取代它們，因為數種類型實作介面，因此可以就地傳遞為 runnables。


### <a name="inner-classes"></a>內部類別

Java 有兩種不同[巢狀類別](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html)： 靜態巢狀類別和非靜態類別。

Java 靜態巢狀的類別完全相同的 C# 的巢狀類型。

非靜態巢狀類別，也稱為*內部的類別*，明顯不同。 它們包含其封入類型的執行個體的隱含參考，而且不能包含靜態成員 （在此概觀的範圍之外的其他差異）。

談到繫結和 C# 使用時，靜態的巢狀的類別會被視為一般的巢狀型別。 內部類別，同時，有兩個顯著的差異：

1. 包含類型的隱含參考必須明確提供，做為建構函式參數。

1. 繼承自內部類別，內部的類別時*必須*巢狀型別中繼承自包含類型的基底的內部類別，而且衍生的型別必須提供 C# 與相同類型的建構函式包含型別。


例如，請考慮[Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)內部類別。 因為它是內部的類別， [WallpaperService.Engine() 建構函式](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/)會參考[WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/)執行個體 (比較和對照於 Java [WallpaperService.Engine (） 建構函式，](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)後者會採用任何參數)。

內部類別的範例衍生是 CubeWallpaper.CubeEngine:

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

請注意如何`CubeWallpaper.CubeEngine`巢狀`CubeWallpaper`，`CubeWallpaper`繼承自包含類別的`WallpaperService.Engine`，和`CubeWallpaper.CubeEngine`的建構函式會宣告的型別-`CubeWallpaper`全部都是在此情況下，上述指定。


### <a name="interfaces"></a>介面

Java 介面可以包含三個集合的成員，其中兩個從 C# 會造成問題：

1. 方法

1. 型別

1. 欄位


Java 介面會轉譯成兩種類型：

1. （選擇性） 的介面，其中包含方法宣告。 這個介面具有相同名稱做為 Java 介面，*除了*也有 '*我*' 前置詞。

1. （選擇性） 包含的任何欄位的靜態類別宣告內的 Java 介面。

巢狀的類型 」 重新放置 」 是同層級的封入的介面，而不是巢狀類型，封入的介面名稱，做為前置詞。

例如，請考慮[android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)介面。
*Parcelable*介面包含方法、 巢狀的類型和常數。 *Parcelable*介面方法會放入[Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/)介面。
*Parcelable*介面的常數會放入[Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/)型別。 巢狀[android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)並[android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html)類型目前不是由於我們泛型的支援; 的限制繫結如果它們都支援，它們會呈現為*Android.OS.IParcelableClassLoaderCreator*並*Android.OS.IParcelableCreator*介面。 例如，巢狀[android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html)介面做為繫結[Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/)介面。


> [!NOTE]
> 從 Xamarin.Android 1.9，就 Java 介面常數<em>重複</em>為了簡化將 Java 移植程式碼。 這有助於改善移植的 Java 程式碼依賴[android 的提供者](http://developer.android.com/reference/android/provider/package-summary.html)常數的介面。

除了上述的類型，有四個進一步的變更：

1. 包含常數，會產生具有相同名稱的 Java 介面的型別。

1. 也包含介面常數的類型包含來自實作的 Java 介面的所有常數。

1. 實作包含常數的 Java 介面的所有類別都取得新的巢狀的 InterfaceConsts 類型其中包含所有實作的介面中的常數。

1. *個月的成本*類型現在已過時。


針對*android.os.Parcelable*介面，這表示會立即會有[ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)包含常數的類型。 例如， [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR)常數會當做繫結[ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/)常數的而不是做為*ParcelableConsts.ContentsFileDescriptor*常數。

包含實作包含其他介面的常數，但這些常數的介面，現在會產生聯集的所有常數。 例如， [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html)介面會實作[android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/)介面。 不過，在 1.9 之前, [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/)型別具有無法存取上宣告的常數[Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/)。
如此一來，Java 運算式*MediaStore.Video.VideoColumns.TITLE*繫結至 C# 運算式必須*MediaStore.Video.MediaColumnsConsts.Title*即難以探索而不閱讀許多 Java 文件。 1.9，對等的 C# 運算式都[ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/)。

此外，請考慮[android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/)實作的 Java 型別*Parcelable*介面。 由於它會實作介面，該介面上的所有常數都會存取 「 透過 「 套件組合類型，例如*Bundle.CONTENTS_FILE_DESCRIPTOR*是完全有效的 Java 運算式。
先前，這個運算式的連接埠C#想要查看以查看從哪一個型別所實作的所有介面*CONTENTS_FILE_DESCRIPTOR*來自。 開始在 Xamarin.Android 1.9 版中，實作包含常數的 Java 介面的類別會有巢狀*InterfaceConsts*型別，其中會包含所有繼承的介面常數。 這可讓翻譯*Bundle.CONTENTS_FILE_DESCRIPTOR*要[ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/)。

最後，使用類型*個月的成本*這類尾碼*Android.OS.ParcelableConsts*現在已淘汰，而非新引入的 InterfaceConsts 巢狀型別。 在 Xamarin.Android 3.0 中，將移除它們。


## <a name="resources"></a>資源

在您的應用程式，則可以包含影像、 版面配置描述、 二進位 blob 和字串的字典[資源檔](http://developer.android.com/guide/topics/resources/providing-resources.html)。
各種 Android Api 旨在[操作上的資源識別碼](http://developer.android.com/guide/topics/resources/accessing-resources.html)而不是映像處理、 字串或二進位 blob 直接。

例如，範例 Android 應用程式，其中包含使用者介面版面配置 ( `main.axml`)，國際化資料表的字串 ( `strings.xml`) 和一些圖示 ( `drawable-*/icon.png`) 會保留其資源的應用程式的 [資源] 目錄中：

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

原生 Android Api 直接與檔案名稱，不會運作，但改為對資源識別碼。 當您編譯使用資源的 Android 應用程式時，建置系統將會封裝散發的資源，並產生類別，稱為`Resource`包含針對每個包含的資源權杖。 例如，上述的資源配置，這是 R 類別會公開：

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

然後，您會使用`Resource.Drawable.icon`參考`drawable/icon.png`檔案，或`Resource.Layout.main`參考`layout/main.xml`檔案，或`Resource.String.first_string`參考第一個檔案中的字串字典`values/strings.xml`。


## <a name="constants-and-enumerations"></a>常數和列舉

原生 Android Api 有許多方法會接受或傳回整數必須對應到常數的欄位，以判斷 int 所代表的意義。 若要使用這些方法，使用者才可參考說明文件，請參閱 的常數是適當的值，這是不盡理想。

例如，請考慮[Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在這些情況下，我們儘可能相關的常數群組到.NET 的列舉型別，並重新對應的方法，以改為讓列舉型別。
如此一來，我們就能夠提供 IntelliSense 的可能值的選取項目。

上述範例中會變成：[Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)。

請注意，這是非常手動的程序，找出哪些常數在一起，屬於哪些 Api 使用這些常數。 請提出 bug 會進一步列舉型別，以在 API 中使用的任何常數。
