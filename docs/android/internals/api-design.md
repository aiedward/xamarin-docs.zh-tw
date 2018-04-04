---
title: 應用程式開發介面設計
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: a9c0b02457f006f75dc5b6f0a52e68865d620f67
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="api-design"></a>應用程式開發介面設計


## <a name="overview"></a>總覽

除了基底類別庫屬於 Mono 核心 Xamarin.Android 隨附的各種 Android 應用程式開發介面可讓開發人員建立單聲道的原生 Android 應用程式繫結。

核心 Xamarin.Android 有 interop 的引擎與 Java 世界該橋接器的 C# 世界，提供開發人員存取 Java api 從 C# 或其他.NET 語言。


## <a name="design-principles"></a>設計原則

以下是一些 Xamarin.Android 繫結我們設計原則

-  符合[.NET Framework 設計方針](http://msdn.microsoft.com/en-us/library/ms229042.aspx)。

-  讓子類別的 Java 類別的開發人員。

-  子類別應該使用 C# 標準建構。

-  衍生自現有的類別。

-  呼叫鏈結的基底建構函式。

-  覆寫方法應該使用 C# 的覆寫系統。

-  實現一般 Java 工作很簡單和硬 Java 工作。

-  將 JavaBean 屬性公開為 C# 屬性。

-  強類型的應用程式開發介面公開 （expose):

    - 增加型別安全。

    - 最小化執行階段錯誤。

    - 您可以使用 IDE intellisense 傳回型別。

    - 允許快顯 IDE 的文件。

-  鼓勵 IDE 中瀏覽的 Api:

    - 利用風險降到最低的 Java 類別庫的架構替代方案。

    - 當適當且適用於，而不是單一方法的介面公開 C# 委派 (lambda，匿名方法和 System.Delegate)。

    - 提供機制來呼叫任意的 Java 程式庫 ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/))。



## <a name="assemblies"></a>組件

Xamarin.Android 包含組成組件數*MonoMobile 設定檔*。 [組件](~/cross-platform/internals/available-assemblies.md)頁面具有的詳細資訊。

中所包含的繫結以 Android 平台`Mono.Android.dll`組件。 這個組件包含使用 Android 應用程式開發介面的整個繫結以及與 Android 的執行階段 VM 通訊。


## <a name="binding-design"></a>繫結設計


### <a name="collections"></a>集合

Android Api 利用 java.util 集合廣泛提供清單、 集合和對應。 我們已公開使用這些項目[System.Collections.Generic](https://developer.xamarin.com/api/namespace/System.Collections.Generic/)我們的繫結中的介面。 基本對應如下：

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html)對應至系統類型[ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx)，協助程式類別[Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/)。

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html)對應至系統類型[IList<T>](http://msdn.microsoft.com/en-us/library/5y536ey6.aspx)，協助程式類別[Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/)。

-   [java.util.Map < K，V >](http://developer.android.com/reference/java/util/Map.html)對應至系統類型[< Dictionary<tkey，Tvalue> >](http://msdn.microsoft.com/en-us/library/s4ys34ea.aspx)，協助程式類別[Android.Runtime.JavaDictionary < K，V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/)。

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html)對應至系統類型[ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx)，協助程式類別[Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/)。

我們已提供 helper 類別來加速速度 copyless 封送處理這些類型。 如果可能的話，我們建議使用這些提供集合，而不是提供架構實作，例如[ `List<T>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/)或[ `Dictionary<TKey, TValue>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%602/)。 [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)實作在內部利用原生 Java 集合，因此不需要在傳遞至 Android API 成員時複製與原生集合。

您可以傳遞至 Android 方法接受該介面的任何介面實作，例如傳遞`List<int>`至[ArrayAdapter&lt;int&gt;(內容、 int、 IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)建構函式。 *不過*，為所有實作*除了*Android.Runtime 實作中，這牽涉到*複製*從 Android 的執行階段 VM Mono VM 清單。 如果清單是更新 Android 的執行階段內的變更 (例如藉由叫用[ArrayAdapter&lt;T&gt;。Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/)方法)，這些變更*則不會*會顯示在 managed 程式碼。 如果`JavaList<int>`所使用，這些變更會顯示。

解析、 集合介面的實作*不*上述其中一項列出**協助程式類別**es 只封送處理 [In]:

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

-  Java 方法組`T getFoo()`和`void setFoo(T)`轉換成`Foo`屬性。 範例： [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/)。

-  Java 方法`getFoo()`轉換成唯讀 Foo 屬性。 範例： [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/)。

-  不會產生設定專用的屬性。

-  屬性是*不*產生如果屬性型別陣列。



### <a name="events-and-listeners"></a>事件與接聽程式

Android 應用程式開發介面建置在 Java 之上，及其元件，請遵循連結事件接聽程式的 Java 模式。 此模式通常會相當繁雜，因為它需要使用者建立匿名類別，並宣告覆寫的方法，例如，這是項目如何執行 Android 的 Java 中：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

會在 C# 中使用事件對等的程式碼：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

請注意，這兩個以上的機制適用於 Xamarin.Android。 您可以實作接聽程式介面，並將它與 View.SetOnClickListener，您可以附加或透過任何一般 C# 開發架構的 Click 事件建立委派。

當接聽程式回呼方法具有 void 傳回時，我們會建立為基礎的應用程式開發介面項目[EventHandler&lt;TEventArgs&gt; ](https://developer.xamarin.com/api/type/System.EventHandler%601/)委派。 我們會產生事件，例如上述範例中為這些接聽程式類型。 不過，如果接聽程式回撥會傳回非 void 的和非-**布林**不會使用值、 事件和事件處理常式。 相反地，我們會產生特定的委派回呼的簽章，新增屬性，而非事件。 原因是處理委派引動過程順序，並傳回處理。 這個方法會反映 API Xamarin.iOS 有何用途。

如果 C# 事件或屬性將會只會自動產生的 Android 事件註冊方法：

1. 具有`set`前置詞，例如[*設定*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/)。

1. 具有`void`傳回型別。

1. 只接受一個參數，參數類型是介面、 此介面有只有一個方法，介面名稱結尾`Listener`，例如[View.OnClick*接聽程式*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/)。


此外，如果接聽程式介面的方法具有傳回型別**布林**而不是**void**，然後產生*EventArgs*子類別將包含*Handled*屬性。 值*Handled*屬性做為傳回值*接聽程式*方法，而且它會預設為`true`。

例如，Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/)方法會接受[View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener)介面，而[View.OnKeyListener.onKey （檢視、 int、 KeyEvent）](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/)方法有布林的傳回型別。 Xamarin.Android 會產生對應[View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)事件，這是[EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/)。
*KeyEventArgs*類別接著具有[View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)屬性，可做為傳回值*View.OnKeyListener.onKey()*方法。

我們想要新增的其他方法和公開的委派型連線 ctors 多載。 此外，具有多個回呼的接聽程式需要一些額外檢查以判斷實作個別的回撥時，可合理，因此我們想要轉換這些所識別。 如果沒有對應的事件，接聽程式必須使用在 C# 中，但請將任何您認為可能會有委派我們注意到的使用方式。 清除獲益委派替代方案時，我們也已完成介面沒有 「 接聽程式 」 後置詞的某些轉換。

所有接聽程式介面實作[ `Android.Runtime.IJavaObject` ](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)介面，因為繫結，讓接聽程式類別必須實作這個介面的實作詳細資料。 這可藉由接聽程式介面實作上的子類別[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)或任何其他包裝 Java 物件，例如 Android 的活動。


### <a name="runnables"></a>Runnables

會利用 Java [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/)介面，以提供委派機制。 [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/)類別是值得注意的取用者，此介面。 Android 採用以及 API 中的介面。
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/)和[View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable)是值得注意的範例。

`Runnable`介面包含一個單一的 void 方法[run （)](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/)。 它因此本身以 C# 做為繫結[System.Action](http://msdn.microsoft.com/en-us/library/system.action.aspx)委派。 我們已提供的繫結中接受的多載`Action`適用的所有應用程式開發介面成員的參數`Runnable`在原生 API 中，例如[Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action))和[View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

我們留[IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/)直接傳遞為 runnables 備妥，而不是取代它們，因為數種類型實作介面，因此可以多載。


### <a name="inner-classes"></a>內部類別

Java 有兩個不同類型的[巢狀類別](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html)： 靜態巢狀類別和非靜態類別。

Java 靜態巢狀的類別是與 C# 巢狀型別相同。

非靜態巢狀類別，也稱為*內部類別*，明顯不同。 包含其封入類型的執行個體的隱含參考，並不能包含靜態成員 （在此概觀的範圍外的其他差異）。

當談到繫結和使用 C# 時，靜態的巢狀的類別會視為一般巢狀型別。 內部的類別，同時，具有兩個重大差異：

1. 包含類型的隱含參考必須明確提供，做為建構函式參數。

1. 從內部的類別內部的類別繼承時*必須*巢狀型別繼承自基底的內部類別，包含型別與衍生型別必須提供 C# 與相同類型的建構函式包含型別。


例如，請考慮[Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)內部類別。 因為它是內部的類別， [WallpaperService.Engine() 建構函式](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/)接受以參考[WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/)執行個體 (比較與對照 java [WallpaperService.Engine (） 建構函式，](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)其可接受任何參數)。

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

請注意如何`CubeWallpaper.CubeEngine`內變成巢狀`CubeWallpaper`，`CubeWallpaper`包含的類別會繼承`WallpaperService.Engine`，和`CubeWallpaper.CubeEngine`已宣告的型別-的建構函式`CubeWallpaper`all 視為本範例中使用上述指定。


### <a name="interfaces"></a>介面

Java 介面可以包含三個集合的成員，其中兩個從 C# 會造成問題：

1. 方法

1. 類型

1. 欄位


Java 介面會轉譯成兩種類型：

1. （選擇性） 包含方法宣告介面。 這個介面具有相同的名稱為的 Java 介面，*除了*也有 '*我*' 前置詞。

1. 包含所有的欄位 （選擇性） 靜態類別宣告內的 Java 介面。

巢狀的類型是 「 移至"是同層級的封入的介面，而不是巢狀類型，封入的介面名稱，做為前置詞。

例如，請考慮[android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)介面。
*Parcelable*介面包含方法、 巢狀的類型和常數。 *Parcelable*介面方法會放入[Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/)介面。
*Parcelable*介面常數會放入[Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/)型別。 巢狀[android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)和[android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html)類型目前不是由於泛型支援; 繫結如果它們不受支援，其方式是呈現為*Android.OS.IParcelableClassLoaderCreator*和*Android.OS.IParcelableCreator*介面。 例如，巢狀[android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html)介面繫結為[Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/)介面。


> [!NOTE]
> 從開始 Xamarin.Android 1.9，Java 介面常數是<em>重複</em>為了簡化移植 Java 程式碼。 這有助於改善 Java 程式碼移植依賴[android 的提供者](http://developer.android.com/reference/android/provider/package-summary.html)常數的介面。

除了上述的類型，有四個所做的變更：

1. 包含常數，會產生具有相同名稱的 Java 介面的型別。

1. 也包含介面常數的類型包含來自實作的 Java 介面的所有常數。

1. 實作包含常數的 Java 介面的所有類別都取得新的巢狀的 InterfaceConsts 類型其中包含所有實作的介面中的常數。

1. *常數*型別現在已過時。


如*android.os.Parcelable*介面，這表示，有現在會[ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)類型可包含常數。 例如， [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR)常數會當做繫結[ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/)常數的而不是為*ParcelableConsts.ContentsFileDescriptor*常數。

包含常數實作包含其他介面，但多個常數的介面，現在會產生聯集的所有常數。 例如， [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html)介面會實作[android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/)介面。 但是之前 1.9， [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/)類型便無法存取常數上宣告[Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/)。
如此一來，Java 運算式*MediaStore.Video.VideoColumns.TITLE*需要繫結至 C# 運算式*MediaStore.Video.MediaColumnsConsts.Title*這很難讀取未探索到許多 Java 文件。 1.9，在對等的 C# 運算式會[ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/)。

此外，請考慮[android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/)類型，實作 Java *Parcelable*介面。 由於它會實作介面，該介面上的所有常數 」 可以通過加以存取 」 組合類型，例如*Bundle.CONTENTS_FILE_DESCRIPTOR*是正常的 Java 運算式。
先前，移植到 C# 此運算式您需要查看再看見的哪種類型會實作所有介面*CONTENTS_FILE_DESCRIPTOR*的來源。 從開始 Xamarin.Android 1.9，實作包含常數的 Java 介面的類別會有巢狀*InterfaceConsts*型別，其中將包含所有繼承的介面常數。 這可讓轉譯*Bundle.CONTENTS_FILE_DESCRIPTOR*至[ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/)。

最後，類型*常數*例如尾碼*Android.OS.ParcelableConsts*現在已過時，而不是新引入 InterfaceConsts 巢狀型別。 在 Xamarin.Android 3.0 中，將移除它們。


## <a name="resources"></a>資源

可以做為應用程式中包含影像、 版面配置描述、 二進位 blob 和字串字典[資源檔](http://developer.android.com/guide/topics/resources/providing-resources.html)。
各種 Android Api 專為[操作上的資源 Id](http://developer.android.com/guide/topics/resources/accessing-resources.html)而映像處理時，不是字串或二進位 blob 直接。

例如，範例 Android 應用程式，其中包含使用者介面版面配置 ( `main.axml`)，國際化資料表字串 ( `strings.xml`) 和一些圖示 ( `drawable-*/icon.png`) 會保留其資源，應用程式的 [資源] 目錄中：

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

原生 Android 應用程式開發介面直接與檔名，不會運作，但是改為處理資源識別碼。 當您編譯使用資源的 Android 應用程式時，建置系統將會封裝發佈資源，並產生一種類別稱為`Resource`，其中包含針對每個包含的資源權杖。 例如，上述的資源配置，這是 R 類別會公開：

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

您可使用`Resource.Drawable.icon`參考`drawable/icon.png`檔案，或`Resource.Layout.main`參考`layout/main.xml`檔案，或`Resource.String.first_string`參考字典檔中的第一個字串`values/strings.xml`。


## <a name="constants-and-enumerations"></a>常數和列舉

原生 Android Api 有許多方法會接受或傳回整數必須對應到常數欄位來判斷 int 的表示。 若要使用這些方法，使用者無須參考說明文件，請參閱哪些常數是適當的值，這是不盡理想。

例如，請考慮[Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在這些情況下，我們儘量相關的常數群組組成.NET 列舉型別，再重新對應方法，以改為讓列舉型別。
這樣做，我們便能夠提供 IntelliSense 選項的可能值。

上述範例中會變成： [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/))。

請注意，這是非常手動程序，找出哪些常數在一起，屬於哪個應用程式開發介面使用這些常數。 請在 API 中會更好表示列舉型別為檔案常數用於任何 bug。
