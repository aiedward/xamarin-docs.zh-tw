---
title: 圖形和動畫
description: Android 會提供一個非常豐富，且不同的架構，以支援 2D 圖形和動畫。 本主題將介紹這些架構，並討論如何建立自訂圖形和動畫用於 Xamarin.Android 應用程式中。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 85a7cd2ac5094a4760c5465bd099ce2e3beeae79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="graphics-and-animation"></a>圖形和動畫

_Android 會提供一個非常豐富，且不同的架構，以支援 2D 圖形和動畫。本主題將介紹這些架構，並討論如何建立自訂圖形和動畫用於 Xamarin.Android 應用程式中。_


## <a name="overview"></a>總覽

雖然在傳統上的有限電源的裝置上執行，最高評等的行動應用程式通常具有複雜使用者經驗 (UX)，完整的高品質的圖形和動畫，提供直覺式、 回應、 動態的操作。 例如行動應用程式取得更多更複雜，使用者就可以開始從應用程式所預期更多。

幸運的是，現代的行動平台有非常強大的架構，同時保留使用方便建立複雜的動畫和自訂圖形。 這可讓開發人員將豐富的互動與少量的工作。

在 Android 的 UI API 架構大致可以分成兩種類別： 圖形和動畫。

圖形會進一步分割成不同的方法，以達成 2D 和 3D 圖形中。 3D 圖形可透過許多內建架構，例如 OpenGL ES （行動特定版本的 OpenGL） 和協力廠商架構，例如 MonoGame （跨平台工具組和 XNA 工具組相容）。 雖然 3D 圖形不是本文章的範圍內，我們將檢驗的內建的 2D 繪圖技術。

Android 提供兩個不同應用程式開發介面的建立 2D 圖形。 其中一個是高層級的宣告式方法和其他程式設計的低階 API:

-   **Drawable 資源**&ndash;這些項目用於繪製指示內嵌在 XML 檔案中 （通常） 或是以程式設計方式建立自訂圖形。 Drawable 資源通常會定義為包含指示或動作，以便 Android 轉譯 2D 圖形的 XML 檔案。 

-   **畫布**&ndash;這是需要直接在基礎的點陣圖上繪製的低階 API。 它提供非常精細的控制顯示的內容。 

除了這些 2D 圖形技術，Android 也會提供幾種不同的方式建立動畫：

-   **Drawable 動畫** &ndash; Android 也支援依框架動畫稱為*Drawable 動畫*。 這是最簡單的動畫 API。 Android 依序載入，並顯示順序 （如同卡通） 中的 Drawable 資源。

-   **以檢視動畫** &ndash; *檢視動畫*Android 中原始的動畫 API，而適用於 Android 的所有版本。 這個 API 已限制，也就是說，它只適用於檢視表物件，並且只能在這些檢視表上執行簡單的轉換。
    檢視動畫通常定義在 XML 檔案中找到`/Resources/anim`資料夾。

-   **屬性動畫** &ndash; Android 3.0 引進了一組新的動畫 API 稱為*屬性動畫*。 這些新的 API 導入的擴充性及彈性的系統，可用來建立任何物件的屬性的動畫，不只是檢視的物件。 這種彈性可讓封裝在不同的類別，可讓程式碼更容易共用的動畫。


檢視動畫是更適合應用程式必須支援舊版前 Android 3.0 API 的 （API 層級 11）。 否則應用程式應該使用較新屬性動畫的 API 上面所提的原因。

所有這些架構是可行的選項，不過有可能，喜好設定應該授予屬性動畫，因為它是更具彈性的 API，來使用。 動畫邏輯封裝在不同的類別，可讓程式碼更容易共用，並簡化程式碼維護工作，讓屬性動畫。


## <a name="accessibility"></a>協助工具選項

圖形和動畫有助於讓 Android 應用程式更能吸引和有趣使用;不過，務必記住的一些互動發生透過 screenreaders，替代輸入裝置，或協助縮放。
此外，且不含音訊功能可能會發生的一些互動。

應用程式已經設計具有 family-or-assembly 存取範圍，請注意，如果是在這些情況下更有用： 提供提示，並在使用者介面中，瀏覽協助，並確定沒有文字內容或圖示的 ui 元素的描述。

請參閱[Google 的協助工具輔助線](http://developer.android.com/guide/topics/ui/accessibility/)如需有關如何使用 Android 的協助工具應用程式開發介面。



## <a name="2d-graphics"></a>2D 圖形

Drawable 資源是常用的技巧，在 Android 應用程式中。 與其他資源，Drawable 資源是宣告式&ndash;它們 XML 檔案中定義。 這個方法可讓資源從程式碼的清楚地分隔。 因為它不需要變更來更新或變更圖形的 Android 應用程式中的程式碼，這可簡化開發與維護。 不過，適用於許多簡單且常見的圖形需求 Drawable 資源時，他們缺乏電源和 Canvas 應用程式開發介面的控制項。

其他技術，使用[畫布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)物件，非常類似於其他傳統的應用程式開發介面架構，例如 System.Drawing 或 iOS 的核心繪圖。 使用畫布物件可提供最佳的方式 2D 圖形控制權會建立。 這適合 Drawable 資源將無法運作，或難以使用。 例如，可能需要繪製其外觀會變更與滑桿的值計算為基礎的自訂滑桿控制項。

讓我們先檢查 Drawable 資源。 它們更簡單，而且涵蓋最常見的自訂繪圖案例。


### <a name="drawable-resources"></a>Drawable 資源

Drawable 資源會定義在目錄中的 XML 檔案`/Resources/drawable`。 不同於內嵌 PNG 或 JPEG 的不需要提供 Drawable 資源密度特定版本。
在執行階段，將會載入這些資源的 Android 應用程式，並將其用於這些 XML 檔案中所包含的指示建立 2D 圖形中。
Android 定義多種不同類型的 Drawable 資源：

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash;這是否為 Drawable 物件繪製幾何基本圖形，並套用一組有限的圖形化該圖形上的效果。 它們是針對像是自訂按鈕，或設定背景 TextViews 的非常有用。 我們會看到如何使用的範例`ShapeDrawable`本文稍後。

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash;這是會變更外觀 widget/控制項的狀態為基礎的 Drawable 資源。 例如，按鈕可能會變更它的外觀，根據是否按下或不。

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash;此 Drawable 資源，就會在另一個頂端堆疊數個其他 drawables 其中一個。 舉例來說， *LayerDrawable*下列螢幕擷取畫面所示：

    ![LayerDrawable 範例](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash;這是*LayerDrawable*但有一點。 A *TransitionDrawable*可以動畫顯示頂端透過另一個圖層。

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash;這是非常類似於*StateListDrawable* ，它會顯示根據特定條件的映像。 不過，不同於*StateListDrawable*、 *LevelListDrawable*顯示映像為基礎的整數值。 舉例來說， *LevelListDrawable* ，可顯示 WiFi 訊號強度。 WiFi 訊號變更的強度，為顯示的 drawable 也會隨著變更。

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;這些 Drawables 正如其名，提供縮放比例和結束時間裁剪功能。 *ScaleDrawable*會調整其他 Drawable，while *ClipDrawable*會裁剪 Drawable 另一個。

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; Drawable 這將會套用至內凹 Drawable 的另一個資源的側邊。 它用於檢視需要比檢視的實際界限小的背景。

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash;這個檔案是一組指示，在 XML 中，會執行實際的點陣圖。 Android 可以執行某些動作會 tiling、 遞色，以及消除鋸齒。 這個非常常見的用途是磚的版面配置背景的點陣圖。


#### <a name="drawable-example"></a>Drawable 範例

讓我們看看如何建立 2D 圖形使用簡單的範例`ShapeDrawable`。 A`ShapeDrawable`可以定義四個基本圖形的其中一個： 矩形、 橢圓形、 線條與信號。 它也可套用的基本的效果，例如漸層、 色彩及大小。 下列 XML 會`ShapeDrawable`，可能會出現在*AnimationsDemo*附屬專案 (檔案中`Resources/drawable/shape_rounded_blue_rect.xml`)。
它會定義具有紫色漸層背景的矩形，並圓角：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

我們可以參考此 Drawable 資源以宣告方式在版面配置頁或其他 Drawable，如下列 XML 所示：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

Drawable 資源也會以程式設計的方式套用。 下列程式碼片段示範如何以程式設計方式設定的 TextView 背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看這會看起來像，執行*AnimationsDemo*專案，然後從主要功能表中選取圖形 Drawable 項目。 我們應該會看到類似下列的螢幕擷取畫面：

![Textview 與使用自訂背景，drawable 漸層和圓角](graphics-and-animation-images/image1.png)

如需詳細資訊的 XML 元素和 Drawable 資源的語法，請參閱[Google 的文件](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。


### <a name="using-the-canvas-drawing-api"></a>使用畫布繪圖應用程式開發介面

Drawables 功能強大，但有其限制。 某些項目是不可能，也可能很複雜 (例如： 將篩選套用到裝置上的相機拍攝的圖片)。 將很難使用 Drawable 資源套用紅眼減少。
相反地，畫布 API 可讓應用程式非常精細的控制，若要選擇性地變更圖片的特定組件中的色彩。

通常用於在畫布上的一個類別是[小畫家](https://developer.xamarin.com/api/type/Android.Graphics.Paint/)類別。 這個類別會保存有關如何繪製色彩和樣式資訊。 它用來提供項目，這類的色彩和透明度。

畫布 API 會使用*複製的模型*繪製 2D 圖形。
作業會套用在後續的圖層，在彼此的上方。 每一項作業將討論一些基礎點陣圖的區域。 當區域重疊先前繪製的區域時，新的 [小畫家] 將部分或完全遮住舊。 這是 iOS 的核心圖形 System.Drawing 等許多其他繪圖 Api 運作的方式相同。

有兩種方式取得`Canvas`物件。 第一種方法牽涉到定義[點陣圖](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)物件，並接著具現化`Canvas`與它的物件。 比方說，下列程式碼片段會建立新的畫布與基礎的點陣圖：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

若要取得的其他方式`Canvas`物件是由[OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/)所提供的回呼方法[檢視](https://developer.xamarin.com/api/type/Android.Views.View/)基底類別。 Android 呼叫這個方法，當它決定檢視需要繪製本身，並傳入`Canvas`要使用之檢視的物件。

畫布類別會公開方法，以程式設計的方式提供繪製指示。 例如: 

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash;使用指定的小畫家填滿整個畫布的點陣圖。

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash;繪製指定的幾何形狀，使用指定的 [小畫家]。

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash;與指定的色彩在畫布上繪製的文字。 在位置繪製文字`x,y`。



#### <a name="drawing-with-the-canvas-api"></a>繪製與 Canvas 應用程式開發介面

我們來看看畫布 API 在作用中的範例。 下列程式碼片段顯示如何畫檢視：

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

上述這段程式碼會先建立紅色小畫家和綠色的 [小畫家] 的物件。 它使用紅色，填滿畫布的內容，然後指示畫布，繪製綠色矩形的寬度為畫布的 25%。 這個範例中可以看到依`AnimationsDemo`隨附於此發行項的原始碼的專案。 正在啟動應用程式，並從主要功能表中選取繪圖項目，我們應該如下所示的畫面：

![小畫家紅色和綠色小畫家物件](graphics-and-animation-images/image3.png)


## <a name="animation"></a>動畫

使用者希望在其應用程式移動的項目。 動畫是改善應用程式的使用者體驗，並協助突出它的好方法。最佳的動畫是復原的使用者未注意到，因為他們認為自然。 Android 的動畫，提供下列三個 API:

-   **檢視動畫**&ndash;這是原始的 API。 這些動畫會繫結至特定的檢視，並且可以在檢視的內容上執行簡單的轉換。 因為它的簡單起見，此 API 也適用於項目，例如 alpha 動畫、 旋轉和其他等等。

-   **屬性的動畫** &ndash; Android 3.0 中引進屬性動畫。 它們可讓應用程式以動畫方式顯示幾乎所有項目。 屬性動畫可用來變更任何屬性的任何物件，即使該物件不是在螢幕上看見。

-   **Drawable 動畫**&ndash;這可用來套用非常簡單的動畫特殊 Drawable 資源影響到版面配置。

一般情況下，屬性動畫是慣用的系統使用更有彈性，並提供更多的功能。


### <a name="view-animations"></a>檢視動畫

檢視動畫限於檢視，且只能執行動畫值，例如開始和結束點、 大小、 旋轉和透明度。
這些類型的動畫通常稱為*tween 動畫*。 兩個方式可以定義檢視動畫&ndash;以程式設計方式在程式碼或使用 XML 檔案。 XML 檔案是較好的方法宣告檢視動畫，因為它們是更容易讀取與更輕鬆地維護。

動畫 XML 檔案會儲存在`/Resources/anim`Xamarin.Android 專案的目錄。 這個檔案必須有一個下列項目當做根項目：

-   `alpha` &ndash; 淡入或淡的動畫。

-   `rotate` &ndash; 旋轉動畫。

-   `scale` &ndash; 調整大小的動畫。

-   `translate` &ndash; 水平和/或垂直的影片。

-   `set` &ndash; 可能有一或多個其他動畫元素的容器。

根據預設，會同時套用 XML 檔案中的所有動畫。 若要製作動畫都會循序進行，設定`android:startOffset`其中一個以上定義的項目上的屬性。

可以使用會影響在動畫中的變動率*interpolator*。 Interpolator 讓加速、 重複，或 decelerated 動畫效果。 Android 的架構會提供數個 interpolators 根據預設，例如 （但不是限於）：

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; 這些 interpolators 增加或減少的動畫的變動率。

-   `BounceInterpolator` &ndash; 變更退結尾。

-   `LinearInterpolator` &ndash; 變更的速率是常數。


下列 XML 顯示動畫檔案，包括結合一些這些元素的範例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

這個動畫會所有動畫的同時執行。 第一個標尺動畫會水平伸展影像和垂直縮小，然後將映像會同時逆時針旋轉 45 度和壓縮，請從畫面消失不見。

動畫可以透過程式設計方式套用到檢視因而誇大動畫，並將它套用到檢視。 Android 提供協助程式類別`Android.Views.Animations.AnimationUtils`，以便增加動畫資源，並且傳回的執行個體`Android.Views.Animations.Animation`。 此物件時，會套用至檢視上，藉由呼叫`StartAnimation`和傳遞`Animation`物件。 下列程式碼片段會顯示此動作的範例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

現在，我們有基本了解檢視動畫的運作方式，可讓將移到屬性的動畫。


### <a name="property-animations"></a>屬性的動畫

屬性師是 Android 3.0 中引進了新的 API。
它們提供了更具擴充性的 API，可用來建立任何物件上的任何屬性的動畫。

所有屬性的動畫所都建立的執行個體[動畫](https://developer.xamarin.com/api/type/Android.Animation.Animator/)子類別。 應用程式不直接使用這個類別，而是使用它的子類別的其中一個：

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash;這個類別是在整個屬性動畫 API 中最重要的類別。 它會計算需要變更的屬性值。 `ViewAnimator`不會直接更新這些值; 相反地，它會引發事件，可以用來更新動畫的物件。

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash;的子類別，這個類別會`ValueAnimator`。 它是用來接受目標物件與更新的屬性，以簡化建立動畫物件的程序。

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash;這個類別會負責相對於動畫執行另一台方式來協調。 動畫可能兩者之間執行同時、 依序或在指定的延遲。


*評估工具*是特殊師用來計算新的值在動畫中的類別。 根據預設，Android 會提供下列評估：

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash;計算整數屬性的值。

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash;計算浮點數屬性的值。

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash;計算色彩屬性的值。

如果正在顯示動畫屬性不是`float`，`int`色彩，應用程式可以建立自己的評估工具藉由實作`ITypeEvaluator`介面。 （實作自訂的評估工具已超出本主題的範圍。）

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

有兩個部分任何動畫： 計算動畫的值，然後在某個物件上的屬性上設定這些值。 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)只會計算值，但它不會運作直接在物件上。 相反地，將更新的物件在動畫存留期間會叫用的事件處理常式內部。 此設計可讓數個屬性，從一個動畫的值進行更新。

取得執行個體`ValueAnimator`藉由呼叫下列的 factory 方法的其中一個：

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

完成後，便`ValueAnimator`執行個體必須具有其持續時間設定，並接著會啟動。 下列範例會示範如何繪製 1000年毫秒的範圍從 0 到 1 的值：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

本身，上述程式碼片段不是很有用，但&ndash;動畫會執行，但沒有目標的更新值。 `Animator`類別將會引發更新事件，當它決定它是為了通知接聽程式有新的值。 應用程式可能會提供事件處理常式來回應此事件，如下列程式碼片段所示：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

現在，我們已經了解`ValueAnimator`，可讓您深入了解`ObjectAnimator`。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)是子類別的`ViewAnimator`，結合的計時引擎和值計算`ValueAnimator`與連接事件處理常式所需的邏輯。 `ValueAnimator`明確呼叫事件處理常式的應用程式必須&ndash;`ObjectAnimator`會為我們負責的這個步驟。

API`ObjectAnimator`非常類似的 API `ViewAnimator`，但會要求您提供的物件及要更新之屬性的名稱。 下列範例顯示使用的範例`ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

您可以從先前的程式碼片段中，看到`ObjectAnimator`可減少並簡化程式碼所需製作物件動畫。


### <a name="drawable-animations"></a>Drawable 動畫

最終的動畫 API 是 Drawable 動畫 API。 Drawable 動畫載入一系列的 Drawable 資源一個接著一個，並循序顯示翻轉 it 卡通類似。

Drawable 資源具有的 XML 檔案中定義`<animation-list>`項目做為根項目及一系列的`<item>`定義每個畫面格在動畫中的項目。 這個 XML 檔案會儲存在`/Resource/drawable`的應用程式資料夾。 下列 XML 是 drawable 動畫的範例：

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

這個動畫會執行到六個框架。 `android:duration`屬性宣告將會顯示每個畫面格的時間長度。 下一步的程式碼片段會示範建立 Drawable 動畫，然後啟動它，當使用者按一下螢幕上的按鈕的範例：

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

此時，我們已涵蓋動畫 Api Android 的應用程式中提供的基礎。


## <a name="summary"></a>總結

這篇文章導入許多新的概念，以協助將某些圖形加入至 Android 應用程式的 API。 第一次它討論各種 2D 圖形 API，並示範如何 Android 可讓應用程式直接繪製畫面使用畫布物件。 我們也會看到一些替代技術可讓以宣告方式建立使用 XML 檔案的圖形。 然後我們已去討論舊的和新 API 的 Android 中建立動畫。



## <a name="related-links"></a>相關連結

- [動畫示範 （範例）](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [動畫和圖形](http://developer.android.com/guide/topics/graphics/index.html)
- [若要將您的行動裝置應用程式使用動畫](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [物件動畫](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [值的動畫](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
