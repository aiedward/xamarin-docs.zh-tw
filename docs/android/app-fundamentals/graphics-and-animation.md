---
title: 圖形和動畫
description: Android 會提供一個非常豐富且多樣化的架構，以支援 2D 圖形和動畫。 本主題介紹這些架構，並討論如何建立自訂圖形和動畫用於 Xamarin.Android 應用程式中。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f4f7fd3af1e90307a84037f01ddf8e52b1ee030
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020015"
---
# <a name="graphics-and-animation"></a>圖形和動畫

_Android 會提供一個非常豐富且多樣化的架構，以支援 2D 圖形和動畫。本主題介紹這些架構，並討論如何建立自訂圖形和動畫用於 Xamarin.Android 應用程式中。_


## <a name="overview"></a>總覽

雖然傳統的有限電源的裝置上執行，最高的評等行動裝置應用程式通常具有複雜使用者體驗 (UX)，完整的高品質的圖形和提供直覺式、 回應、 動態的風格的動畫。 因為行動裝置應用程式取得更多，而且更複雜，使用者已開始從應用程式發生越來越多的情況。

幸運的是，現代的行動平台會有非常強大的架構，用於建立複雜的動畫和自訂圖形，同時保留容易使用。 這可讓開發人員將豐富的互動，事半功倍。

在 Android 中的 UI API 架構大致上可以分成兩個類別：圖形和動畫。

圖形會進一步分成不同的方法，以執行 2D 和 3D 圖形。 3D 圖形可透過數個內建架構，例如 OpenGL ES （行動裝置特定版本的 OpenGL），以及協力廠商架構，例如 MonoGame （跨平台工具組與 XNA 工具組相容）。 雖然這篇文章的範圍內，3D 圖形不是，我們將檢視的內建的 2D 繪圖技術。

Android 提供兩個不同 API，以建立 2D 圖形。 其中一個是高層級的宣告式方法和其他程式設計的低階 API:

-   **可繪製資源**&ndash;這些可用來建立自訂圖形 （通常） 或以程式設計方式內嵌在 XML 檔案中的繪製指令。 可繪製資源通常被定義為包含指示或動作來轉譯 2D 圖形的 android 版的 XML 檔案。 

-   **畫布**&ndash;這是牽涉到直接在基礎的點陣圖上繪製的最低層級 API。 它提供非常精確控制所顯示的內容。 

除了這些 2D 圖形技術，Android 也會提供數種建立動畫的不同方式：

-   **可繪製動畫** &ndash; Android 也支援稱為的畫面格的畫面格動畫*可繪製動畫*。 這是最簡單的動畫 API。 Android 循序載入和顯示 （很像卡通） 的序列中的可繪製資源。

-   **檢視動畫** &ndash; *檢視動畫*原始動畫 API 的 Android 中，而且僅可用於所有版本的 Android。 此 API 僅限於，因為它只適用於檢視物件，且只能在這些檢視表上執行簡單的轉換。
    檢視動畫通常定義於 XML 檔案中找到`/Resources/anim`資料夾。

-   **屬性動畫** &ndash; Android 3.0 引進了一組新的動畫 API 稱為*屬性動畫*。 這些新的 API 引進了可擴充且彈性的系統，可用來以動畫顯示任何物件的屬性，不只是檢視的物件。 這種彈性可讓封裝在不同的類別，讓程式碼更容易共用的動畫。


檢視動畫是更適合必須支援舊版前 Android 3.0 API （API 層級 11） 的應用程式。 否則應用程式應該使用較新屬性動畫的 API，如上方所述的原因。

一種架構都是基本的可行選項，不過可能的話，喜好設定應該提供給屬性的動畫，因為它是一個更有彈性的 API，可使用。 屬性動畫，讓動畫邏輯封裝在不同的類別，可更輕鬆地共用程式碼，並簡化程式碼維護工作。


## <a name="accessibility"></a>協助工具選項

圖形和動畫進行 Android 應用程式更吸引人的協助和使用起來樂趣不過，務必記住，某些互動會透過助讀，替代輸入裝置，或透過輔助縮放。
此外，不含音訊功能的一些互動可能會發生。

應用程式是更能在這些情況下，如果它們有障礙的設計： 提供提示，而在使用者介面中，瀏覽協助，並確保沒有文字內容或圖形 UI 元素的描述。

請參閱[Google 的協助工具指南](https://developer.android.com/guide/topics/ui/accessibility/)如需有關如何使用 Android 的協助工具的 Api。



## <a name="2d-graphics"></a>2D 圖形

可繪製資源是在 Android 應用程式中的常用技巧。 因為其他資源，可繪製資源是宣告式&ndash;定義 XML 檔案中。 這種方法可讓程式碼的資源從清楚的分隔。 這可簡化開發和維護，因為它不需要變更程式碼來更新或變更 Android 應用程式中的圖形。 不過，適用於許多簡單且常見的圖形需求可繪製資源時，他們沒有足夠的強大功能與控制的畫布 API。

其他技術，使用[畫布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)物件，非常類似於其他傳統的 API 架構，例如 System.Drawing 或 iOS 的核心繪圖。 使用畫布物件可提供建立如何 2D 圖形的最多的控制權。 它是適用於可繪製資源將無法運作，或將很難使用。 比方說，它可能需要繪製自訂滑桿控制項的外觀會變更滑桿的值與相關的計算基礎。

我們先看看可繪製資源。 它們會簡單，而且會涵蓋最常見的自訂繪圖案例。


### <a name="drawable-resources"></a>可繪製資源

可繪製資源會定義在目錄中的 XML 檔案`/Resources/drawable`。 不同於內嵌 PNG 或 JPEG 的不需要提供的可繪製資源的密度的特定版本。
在執行階段，Android 應用程式會載入這些資源，並使用包含在這些 XML 檔案中的指示來建立 2D 圖形。
Android 會定義許多不同類型的可繪製資源：

-   [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash;這是繪製基本的幾何圖形，且適用於一組有限的圖形化的效果，該圖形上的可繪製物件。 它們非常適合用於自訂按鈕，或設定 TextViews 背景等項目。 我們會看到如何使用範例`ShapeDrawable`本文稍後。

-   [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash;這是會變更外觀的小工具/控制項的狀態為基礎的可繪製資源。 例如，按鈕可能會變更其外觀取決於是否被按下或未。

-   [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; ，就會在另一個之上堆疊數個其他可繪製資源來一本可繪製資源。 舉例*LayerDrawable*以下的螢幕擷取畫面所示：

    ![LayerDrawable 範例](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash;這是第*LayerDrawable*但有一項差異。 A *TransitionDrawable*能夠顯示之上另一個圖層以動畫顯示。

-   [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash;這是非常類似於*StateListDrawable* ，因為它會顯示特定條件為基礎的映像。 不過，不同於*StateListDrawable*，則*LevelListDrawable*顯示整數值為基礎的映像。 舉例*LevelListDrawable*是顯示 WiFi 訊號強度。 WiFi 訊號變更程度，作為可繪製顯示也會跟著變更。

-   [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;正如其名，這些可繪製資源來提供調整和裁剪功能。 *ScaleDrawable*擴充另一個可繪製，while *ClipDrawable*會裁剪其他 Drawable。

-   [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash;可繪製這將會套用至內凹另一個可繪製資源側邊。 它是用來檢視必須小於檢視的實際界限的背景。

-   XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash;這個檔案是一組指示，在 XML 中，包含要在實際的點陣圖上執行。 Android 可以執行某些動作是 tiling、 抖色處理，並消除鋸齒。 這個非常常見的用途之一是磚的版面配置背景的點陣圖。


#### <a name="drawable-example"></a>可繪製的範例

讓我們看看如何使用下列方法建立 2D 圖形的快速範例`ShapeDrawable`。 A`ShapeDrawable`可以定義四種基本圖形的其中一個： 矩形、 橢圓形、 線條與信號。 它也可套用的基本的效果，例如漸層、 圓形及大小。 下列 XML 是`ShapeDrawable`，可能會出現在*AnimationsDemo*隨附的專案 (檔案中`Resources/drawable/shape_rounded_blue_rect.xml`)。
它會定義具有紫色的漸層背景的矩形，並圓角：

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

我們可以參考此可繪製資源，以宣告方式在版面配置或其他 Drawable，如下列 XML 所示：

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

可繪製資源也可以在以程式設計方式套用。 下列程式碼片段示範如何以程式設計方式設定的 TextView 背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看這會看起來，執行*AnimationsDemo*專案，然後從主功能表中選取的圖形可繪製的項目。 我們應該會看到類似下列螢幕擷取畫面：

![具有自訂背景，可繪製具有漸層停駐和圓角的 Textview](graphics-and-animation-images/image1.png)

如需進一步瞭解 XML 項目和可繪製資源的語法的詳細資訊，請參閱[Google 的文件](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。


### <a name="using-the-canvas-drawing-api"></a>使用畫布繪圖 API

可繪製資源來強大，但的確有其限制。 某些項目都不是可能的或者是以非常複雜 (例如： 將篩選套用至裝置上的相機所拍攝的圖片)。 它會很難使用可繪製資源來套用消除紅眼而減少。
相反地，畫布 API 可讓應用程式會有極精細的控制，若要選擇性地變更圖片的特定組件中的色彩。

通常，用於在畫布上的一個類別是[小畫家](https://developer.xamarin.com/api/type/Android.Graphics.Paint/)類別。 這個類別包含有關如何繪製色彩和樣式資訊。 它用來提供項目，這類的色彩和透明度。

畫布 API 會使用*繪製器的模型*繪製 2D 圖形。
因為連續數層彼此以套用作業。 每個作業將涵蓋一些基礎點陣圖的區域。 當區域重疊先前繪製的區域時，新的 [小畫家] 將部分或難理解舊。 這是相同的方式，例如 System.Drawing 和 iOS 的核心圖形的其他許多繪圖 Api 運作。

有兩種方式可取得`Canvas`物件。 第一種方法牽涉到定義[點陣圖](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)物件，然後具現化`Canvas`與它的物件。 比方說，下列程式碼片段會建立新的畫布與基礎的點陣圖：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

若要取得的其他方式`Canvas`物件是由[OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/)提供的回呼方法[檢視](https://developer.xamarin.com/api/type/Android.Views.View/)基底類別。 Android 會呼叫這個方法，決定檢視需要繪製本身，並傳入`Canvas`來使用檢視的物件。

畫布類別會公開方法，以程式設計的方式提供的繪製指示。 例如: 

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash;使用指定的小畫家填滿整個畫布的點陣圖。

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash;繪製指定的幾何形狀，使用指定的 [小畫家]。

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash;滿指定的圓形畫布上繪製的文字。 在位置繪製文字`x,y`。



#### <a name="drawing-with-the-canvas-api"></a>繪製與 Canvas API

我們來看看作用中的畫布 API 的範例。 下列程式碼片段示範如何繪製檢視：

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

上面這個程式碼會先建立紅色小畫家和綠色的 [小畫家] 的物件。 它使用紅色，填滿畫布的內容，然後指示繪製綠色矩形的 25%，畫布的寬度為畫布。 這個範例中可以看到由`AnimationsDemo`隨附於本文的原始程式碼的專案。 正在啟動應用程式，並從主要功能表中選取 繪製項目，我們應該類似下面的畫面：

![紅色小畫家和綠色的 [小畫家] 物件的畫面](graphics-and-animation-images/image3.png)


## <a name="animation"></a>動畫

使用者會喜歡在其應用程式中移動的項目。 動畫是適合用來改善使用者體驗的應用程式，且有助於突顯出來。最佳的動畫是使用者未注意到，因為他們認為自然的項目。 Android 的動畫，提供下列三個 API:

-   **檢視動畫**&ndash;這是原始的 API。 這些動畫會繫結到特定的檢視，而且可以對檢視的內容執行簡單的轉換。 因為它的簡單起見，此 API 仍適用於項目，例如 alpha 動畫、 旋轉和其他等等。

-   **屬性的動畫** &ndash; Android 3.0 中導入屬性的動畫。 它們可讓應用程式以動畫顯示絶大部分項目。 屬性動畫可用來變更任何屬性的任何物件，即使該物件不在畫面上看到。

-   **可繪製動畫**&ndash;這用來將非常簡單的動畫套用特殊的可繪製資源影響配置。

一般情況下，屬性動畫是慣用的系統使用，因為它會更有彈性，並提供更多的功能。


### <a name="view-animations"></a>檢視動畫

檢視動畫僅限於檢視，並且只能執行動畫值，例如開始和結束點、 大小、 旋轉和透明度。
這種動畫通常稱為*tween 動畫*。 兩種方式可以定義檢視動畫&ndash;以程式設計方式在程式碼，或藉由使用 XML 檔案。 XML 檔案會宣告檢視動畫的慣用的方法，因為它們更容易理解且容易維護。

動畫的 XML 檔案會儲存在`/Resources/anim`Xamarin.Android 專案的目錄。 這個檔案必須有一個下列項目當做根項目：

-   `alpha` &ndash; 淡入或淡出動畫。

-   `rotate` &ndash; 旋轉的動畫。

-   `scale` &ndash; 調整大小的動畫。

-   `translate` &ndash; 水平及/或垂直的動作。

-   `set` &ndash; 可能有一或多個其他的動畫元素的容器。

根據預設，XML 檔案中的所有動畫都將會同時都套用。 若要讓動畫都會循序進行，請設定`android:startOffset`其中一項以上定義的項目上的屬性。

很可能會影響使用的動畫中的變動率*interpolator*。 Interpolator 能夠加速、 重複，或 decelerated 動畫效果。 Android 架構會提供數個 interpolators 內建的例如 （但不是限於）：

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; 這些 interpolators 增加或減少的動畫中的變動率。

-   `BounceInterpolator` &ndash; 變更不斷彈跳直到結尾。

-   `LinearInterpolator` &ndash; 變更的速率是常數。


下列 XML 顯示動畫檔案則結合這些元素的一些的範例：

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

這個動畫會執行所有的動畫同時。 第一個刻度動畫會水平伸展影像，並以垂直方式，將它縮小，然後將映像會同時以逆時針方向旋轉 45 度和壓縮，請從畫面消失。

動畫可以以程式設計方式套用到檢視擴張動畫，並將它套用至檢視。 Android 提供的協助程式類別`Android.Views.Animations.AnimationUtils`，將會擴充動畫資源，並傳回的執行個體`Android.Views.Animations.Animation`。 這個物件時，會套用至檢視上，藉由呼叫`StartAnimation`並傳遞`Animation`物件。 下列程式碼片段會顯示這個範例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

既然我們已經有基本了解檢視動畫的運作方式，可讓將移至屬性的動畫。


### <a name="property-animations"></a>屬性動畫

屬性揮灑是 Android 3.0 中引進的新 API。
它們提供了更具擴充性的 API，可用來以動畫顯示任何物件上的任何屬性。

所有的屬性動畫所建立的執行個體[動畫](https://developer.xamarin.com/api/type/Android.Animation.Animator/)子類別。 應用程式不直接使用這個類別，改為使用其中的子類別：

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash;這個類別是在整個屬性動畫 API 中最重要的類別。 它會計算需要變更的屬性的值。 `ViewAnimator`不會直接更新這些值; 相反地，它會引發事件，可用來更新動畫的物件。

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash;這個類別是子類別的`ValueAnimator`。 它是用來簡化物件製作動畫的程序接受目標物件和更新的屬性。

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash;這個類別是負責協調相對於動畫執行至另一個方式。 動畫可能會執行同時、 循序或指定的延遲，兩者之間。


*評估工具*揮灑用來計算新值的動畫播放期間的特殊類別。 根據預設，Android 會提供下列評估：

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash;計算整數屬性的值。

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash;計算浮點數屬性值。

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash;計算圓形的屬性值。

如果正在顯示動畫的屬性不是`float`，`int`的圓形，應用程式可以建立自己的評估工具藉由實作`ITypeEvaluator`介面。 （實作自訂的評估工具已超出本主題的範圍。）

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

有任何動畫的兩個部分： 計算動畫的值，然後在某個物件上的屬性上設定這些值。 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)只會計算值，但它不會運作直接物件上。 相反地，會在動畫存留期間將會叫用的事件處理常式內更新的物件。 此設計可讓數個屬性，從一個動畫的值更新。

取得的執行個體`ValueAnimator`藉由呼叫其中一個下列的 factory 方法：

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

完成後完成的動作，`ValueAnimator`執行個體必須具有其持續時間設定，並再加以啟動。 下列範例顯示如何以動畫顯示 1000年毫秒的範圍內的從 0 到 1 的值：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

本身，上述程式碼片段不是很有用，但&ndash;動畫會執行，但沒有任何目標更新值。 `Animator`類別將會引發更新事件，當它判定這是必要通知接聽程式有新的值。 應用程式可能會提供事件處理常式來回應此事件，如下列程式碼片段所示：

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

既然我們已經了解`ValueAnimator`，可讓您深入了解`ObjectAnimator`。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)是的子類別`ViewAnimator`，結合的計時引擎和值計算`ValueAnimator`與連接事件處理常式所需的邏輯。 `ValueAnimator`明確地將傳送的事件處理常式的應用程式必須&ndash;`ObjectAnimator`此步驟會為我們處理。

適用於 API`ObjectAnimator`非常類似的 API `ViewAnimator`，但會要求您提供的物件及要更新之屬性的名稱。 下列範例顯示使用範例`ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

您可以從先前的程式碼片段中，看到`ObjectAnimator`可以減少並簡化所需以動畫顯示物件的程式碼。


### <a name="drawable-animations"></a>可繪製動畫

最後一個動畫 API 是可繪製動畫 API。 載入的一系列可繪製資源一個接著一個可繪製動畫，並循序顯示類似 flip it 草圖。

可繪製資源會定義在 XML 檔案具有`<animation-list>`項目當做根項目和一系列的`<item>`定義每個畫面格動畫中的項目。 此 XML 檔案會儲存在`/Resource/drawable`應用程式的資料夾。 下列 XML 是可繪製動畫的範例：

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

這個動畫會執行六個畫面格。 `android:duration`屬性會宣告將會顯示每個畫面格的時間長度。 下一步 的程式碼片段示範建立可繪製動畫，然後啟動它，當使用者按一下螢幕上的按鈕：

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

此時，我們已涵蓋動畫中的 Android 應用程式提供 Api 的基礎。


## <a name="summary"></a>總結

本文介紹許多新概念及 API，協助將某些圖片新增到 Android 應用程式。 第一次它討論各種 2D 圖形 API，並示範如何 Android 可讓應用程式直接繪製畫面使用的畫布物件。 我們也會發現一些替代的方法可以讓使用 XML 檔案以宣告方式建立的圖形。 然後我們出在討論的舊和新 API 的 Android 中建立動畫。



## <a name="related-links"></a>相關連結

- [動畫示範 （範例）](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [動畫和圖形](https://developer.android.com/guide/topics/graphics/index.html)
- [若要將您的行動裝置應用程式融入生活使用動畫](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [物件的動畫](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [值的動畫](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
