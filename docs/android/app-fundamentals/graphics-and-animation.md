---
title: 圖形和動畫
description: Android 提供非常豐富且多樣化的架構，可支援2D 圖形和動畫。 本主題將介紹這些架構，並討論如何建立自訂的圖形和動畫，以用於 Xamarin. Android 應用程式。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: eeee9b7d694d9380c653fb87c24171bcaf79389d
ms.sourcegitcommit: 9ab907e053c57fc96419149f83187bc3e8983a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75655279"
---
# <a name="android-graphics-and-animation"></a>Android 圖形和動畫

_Android 提供非常豐富且多樣化的架構，可支援2D 圖形和動畫。本主題將介紹這些架構，並討論如何建立自訂的圖形和動畫，以用於 Xamarin. Android 應用程式。_

## <a name="overview"></a>概觀

儘管在傳統上有電力限制的裝置上執行，最高等級的行動應用程式通常會有精密的使用者體驗（UX），並以高品質的圖形和動畫（提供直覺且快速的動態風格）來完成。 隨著行動應用程式變得更複雜，使用者也開始期待應用程式的越來越多。

幸運的是，現代化的行動平臺具有非常強大的架構，可建立精密的動畫和自訂圖形，同時保有方便使用。 這可讓開發人員輕鬆地加入豐富的互動。

Android 中的 UI API 架構大致可以分成兩個類別：圖形和動畫。

圖形會進一步分割成不同的方法來執行2D 和3D 圖形。 3D 圖形可透過多個內建的架構（例如 OpenGL ES）和協力廠商架構（例如，MonoGame （與同型工具組相容的跨平臺工具組）來取得。 雖然3D 圖形不在本文的討論範圍內，但我們將會探討內建的2D 繪圖技術。

Android 提供兩個不同的 API 來建立2D 圖形。 其中一個是高階宣告式方法，另一個是程式設計低層級 API：

- 可**繪製的資源**&ndash; 這些是用來以程式設計方式建立自訂圖形，或藉由在 XML 檔案中內嵌繪製指令，以進行更常見的選擇 可繪製的資源通常會定義為 XML 檔案，其中包含 Android 呈現2D 圖形的指示或動作。 

- **畫布**&ndash; 這是一個低層級的 API，其中牽涉到直接在基礎點陣圖上繪製。 它可以對顯示的內容提供非常細微的控制。 

除了這些2D 圖形技術，Android 也提供數種不同的方式來建立動畫：

- &ndash; Android 的可**繪製動畫**也支援以框架為依據的動畫，稱為「可*繪製動畫*」。 這是最簡單的動畫 API。 Android 會依序載入和顯示依序排列的繪製資源（非常類似于卡通）。

- **觀看** *動畫 &ndash; 視圖動畫是*android 中的原始動畫 API，並可在所有版本的 android 中使用。 這個 API 的限制在於，它只會使用 View 物件，而且只能對那些視圖執行簡單的轉換。
    視圖動畫通常會定義在 [`/Resources/anim`] 資料夾中找到的 XML 檔案中。

- &ndash; Android 3.0 的**屬性動畫**引進了一組新的動畫 API，稱為*屬性動畫*。 這些新的 API 引進了可擴充且彈性的系統，可用來以動畫顯示任何物件的屬性，而不只是 View 物件。 這種彈性可讓動畫封裝在不同的類別中，使程式碼共用變得更容易。

視圖動畫更適合必須支援舊版 Android 3.0 API 的應用程式（API 層級11）。 否則，應用程式應該使用較新的屬性動畫 API，原因如下所述。

所有這些架構都是可行的選項，但在可能的情況下，應該將喜好設定提供給屬性動畫，因為這是更有彈性的 API 可供使用。 屬性動畫允許將動畫邏輯封裝在不同的類別中，使程式碼共用變得更容易，並簡化程式碼維護。

## <a name="accessibility"></a>協助工具

圖形和動畫有助於讓 Android 應用程式更具吸引力且更有趣的使用;不過，請務必記住，某些互動會透過螢幕助讀程式、替代輸入裝置或輔助縮放來進行。
此外，某些互動可能會在沒有音訊功能的情況下發生。

在這些情況下，如果應用程式已設計為具有協助工具，就更能使用：在使用者介面中提供提示和導覽協助，並確保 UI 的圖形元素有文字內容或描述。

如需如何使用 Android 協助工具 Api 的詳細資訊，請參閱[Google 的協助工具指南](https://developer.android.com/guide/topics/ui/accessibility/)。

## <a name="2d-graphics"></a>2D 圖形

可繪製資源是 Android 應用程式中常用的技術。 如同其他資源，可繪製的資源是在 XML 檔案中定義的宣告式 &ndash;。 這種方法可讓您清楚區分程式碼與資源。 這可以簡化開發和維護，因為不需要變更程式碼來更新或變更 Android 應用程式中的圖形。 不過，雖然可繪製資源適用于許多簡單和一般的圖形需求，但它們缺乏畫布 API 的強大功能和控制權。

另一種使用[Canvas](xref:Android.Graphics.Canvas)物件的技巧，非常類似于其他傳統 API 架構，例如 System. 繪圖或 IOS 的核心繪圖。 使用 Canvas 物件可讓您充分掌控2D 圖形的建立方式。 這適用于可繪製資源無法使用或很難以處理的情況。 例如，您可能必須繪製自訂滑杆控制項，其外觀會根據與滑杆值相關的計算而變更。

讓我們先檢查可繪製的資源。 它們比較簡單，並涵蓋最常見的自訂繪圖案例。

### <a name="drawable-resources"></a>繪製資源

可繪製資源會在目錄 `/Resources/drawable`的 XML 檔案中定義。 與內嵌 PNG 或 JPEG 不同的是，不需要提供可繪製資源的密度特定版本。
在執行時間，Android 應用程式會載入這些資源，並使用這些 XML 檔案中包含的指示來建立2D 圖形。
Android 定義數種不同類型的繪製資源：

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; 這是可繪製基本幾何形狀並對該圖形套用一組有限圖形效果的可繪製物件。 它們非常適合用於自訂按鈕或設定 Textview 的背景等專案。 我們會在本文稍後看到如何使用 `ShapeDrawable` 的範例。

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; 這是可繪製的資源，會根據 widget/控制項的狀態變更外觀。 例如，按鈕可能會根據是否按下而變更其外觀。

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; 此可繪製資源，這會將多個其他可繪製資源堆疊在另一個上。 *LayerDrawable*的範例如下列螢幕擷取畫面所示：

    ![LayerDrawable 範例](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; 這是*LayerDrawable* ，但有一項差異。 *TransitionDrawable*能夠以動畫顯示在另一個圖層上方。

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; 這非常類似*StateListDrawable* ，因為它會根據特定條件來顯示影像。 不過，不同于*StateListDrawable*， *LevelListDrawable*會根據整數值來顯示影像。 *LevelListDrawable*的範例是顯示 WiFi 信號的強度。 當 WiFi 信號的強度變更時，顯示的可繪製會隨之改變。

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale) / [ ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip)正&ndash;如其名, 這些可繪製資源同時提供縮放和裁剪功能。 *ScaleDrawable*會調整另一個可繪製的，而*ClipDrawable*會裁剪另一個可繪製的。

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; 此可繪製會將內凹套用至另一個可繪製資源的側邊。 當視圖需要的背景小於此視圖的實際界限時，就會使用它。

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; 此檔案是在實際點陣圖上執行的一組指令，以 xml 表示。 Android 可執行檔某些動作包括並排、抖動和消除鋸齒。 其中一個非常常見的用法是在版面配置的背景並排顯示點陣圖。

#### <a name="drawable-example"></a>繪製範例

讓我們看一下如何使用 `ShapeDrawable`建立2D 圖形的快速範例。 `ShapeDrawable` 可以定義四種基本圖形的其中一個：矩形、橢圓形、線條和環形。 也可以套用基本效果，例如漸層、色彩和大小。 下列 XML 是可以在*AnimationsDemo*附屬專案（在檔案 `Resources/drawable/shape_rounded_blue_rect.xml`中）中找到的 `ShapeDrawable`。
它會定義具有紫色漸層背景和圓角的矩形：

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

我們可以在版面配置或其他繪製中以宣告方式參考此可繪製資源，如下列 XML 所示：

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

可繪製的資源也可以透過程式設計方式套用。 下列程式碼片段顯示如何以程式設計方式設定 TextView 的背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看這看起來的樣子，請執行*AnimationsDemo*專案，然後從主功能表中選取 [圖形可繪製] 專案。 我們應該會看到類似下列螢幕擷取畫面的內容：

[![具有自訂背景的 Textview，並以漸層和圓角繪製](graphics-and-animation-images/image2-sml.png)](graphics-and-animation-images/image2.png#lightbox)

如需有關可繪製資源的 XML 元素和語法的詳細資訊，請參閱[Google 的檔](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。

### <a name="using-the-canvas-drawing-api"></a>使用畫布繪製 API

可繪製資源功能強大，但有其限制。 某些事項可能不可行或非常複雜（例如：將篩選套用至裝置上的相機所拍攝的圖片）。 藉由使用可繪製的資源，將會非常難以縮減紅眼。
取而代之的是，畫布 API 可讓應用程式有非常細微的控制，以選擇性地變更圖片特定部分的色彩。

通常與畫布搭配使用的一個類別是[油漆](xref:Android.Graphics.Paint)類別。 這個類別會保存如何繪製的色彩和樣式資訊。 它是用來提供色彩和透明度等專案。

畫布 API 會使用此*油漆模式*來繪製2d 圖形。
作業會在彼此之上的後續層中套用。 每項作業都會涵蓋基礎點陣圖的某個區域。 當區域與先前繪製的區域重迭時，新的繪製會部分或完全遮蔽舊的。 這與其他許多繪圖 Api （例如，System. 繪圖和 iOS 的核心圖形）的使用方式相同。

有兩種方式可取得 `Canvas` 物件。 第一種方式包括定義[點陣圖](xref:Android.Graphics.Bitmap)物件，然後使用它來具現化 `Canvas` 物件。 例如，下列程式碼片段會建立具有基礎點陣圖的新畫布：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

取得 `Canvas` 物件的另一種方式是透過提供[View](xref:Android.Views.View)基類的[OnDraw](xref:Android.Views.View.OnDraw*)回呼方法。 Android 會在決定視圖需要自行繪製，並傳入 `Canvas` 物件，以供視圖使用時呼叫此方法。

Canvas 類別會公開方法，以程式設計方式提供繪製指示。 例如：

- [Canvas.DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*)&ndash;會以指定的繪製填滿整個畫布的點陣圖。

- [Canvas.DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash;使用指定的繪製繪製指定的幾何形狀。

- [Canvas.DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash;會以指定的色彩繪製畫布上的文字。 文字會在位置 `x,y` 繪製。

#### <a name="drawing-with-the-canvas-api"></a>使用畫布 API 繪製

以下是作用中畫布 API 的範例。 下列程式碼片段顯示如何繪製視圖：

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

上述程式碼會先建立紅色繪製和綠色繪製物件。 它會以紅色填滿畫布的內容，然後指示畫布繪製一個綠色矩形，其為畫布寬度的25%。 如需這項工作的範例，請參閱本文的原始程式碼所包含的 `AnimationsDemo` 專案。 藉由啟動應用程式，然後從主功能表中選取繪製專案，我們應該會顯示類似下面的畫面：

[具有紅色油漆和綠色繪製物件的 ![畫面](graphics-and-animation-images/image3-sml.png)](graphics-and-animation-images/image3.png#lightbox)

## <a name="animation"></a>動畫

使用者喜歡在其應用程式中移動的專案。 動畫是改善應用程式使用者體驗並協助它脫穎而出的絕佳方式。最佳動畫是使用者不會注意到的效果，因為他們覺得自然。 Android 為動畫提供下列三個 API：

- **View 動畫**&ndash; 這是原始 API。 這些動畫會系結至特定的視圖，而且可以對視圖內容執行簡單的轉換。 因為這是簡單的，此 API 仍然適用于 Alpha 動畫、旋轉等專案。

- 屬性**動畫**&ndash; 在 Android 3.0 中引進屬性動畫。 它們可讓應用程式建立幾乎任何專案的動畫。 屬性動畫可以用來變更任何物件的任何屬性，即使該物件在螢幕上看不到也一樣。

- 可**繪製的動畫**&ndash; 這個特殊的可繪製資源，用來將非常簡單的動畫效果套用至配置。

一般而言，屬性動畫是慣用的系統，因為它較有彈性，並提供更多功能。

### <a name="view-animations"></a>視圖動畫

視圖動畫僅限於視圖，而且只能對起點和終點、大小、旋轉和透明度等值執行動畫。
這些類型的動畫通常稱為「*補間動畫*」。 您可以用程式設計方式或使用 XML 檔案，以兩種方式來定義視圖動畫 &ndash;。 XML 檔案是宣告視圖動畫的慣用方式，因為它們更容易閱讀且更易於維護。

動畫 XML 檔案會儲存在 Xamarin Android 專案的 `/Resources/anim` 目錄中。 此檔案必須具有下列其中一個元素做為根項目：

- `alpha` &ndash; 淡入或淡出動畫。

- `rotate` &ndash; 旋轉動畫。

- `scale` &ndash; 調整大小動畫。

- `translate` &ndash; 水準和/或垂直動作。

- `set` &ndash; 可能保留一或多個其他動畫元素的容器。

根據預設，會同時套用 XML 檔案中的所有動畫。 若要依序執行動畫，請在上面定義的其中一個元素上設定 `android:startOffset` 屬性。

您可以使用*插*轉器來影響動畫中變更的速率。 插即用可以加速、重複或 decelerated 動畫效果。 Android framework 提供幾個現成可用的 interpolators，例如（但不限於）：

- `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; 這些 interpolators 會增加或減少動畫中的變更率。

- `BounceInterpolator` &ndash; 結束時的變更。

- `LinearInterpolator` &ndash; 變更的速率是固定的。

下列 XML 顯示結合下列部分元素的動畫檔案範例：

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

這個動畫會同時執行所有的動畫。 第一個尺規動畫會水準延展影像並垂直縮小，然後影像會以順時針和縮小的方式同時旋轉45度（從螢幕中消失）。

動畫可以透過因而誇大動畫，然後套用至視圖，以程式設計方式套用至視圖。 Android 提供的 helper 類別 `Android.Views.Animations.AnimationUtils` 將會擴充動畫資源，並傳回 `Android.Views.Animations.Animation`的實例。 這個物件會藉由呼叫 `StartAnimation` 並傳遞 `Animation` 物件，套用至視圖。 下列程式碼片段顯示這種情況的範例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

既然我們已經對觀看動畫的工作方式有基本瞭解，讓我們移至屬性動畫。

### <a name="property-animations"></a>屬性動畫

屬性 animators 是在 Android 3.0 中引進的新 API。
它們提供更可擴充的 API，可以用來建立任何物件上任何屬性的動畫。

所有屬性動畫都是由[Animator](xref:Android.Animation.Animator)子類別的實例所建立。 應用程式不會直接使用此類別，而是使用其中一個子類別：

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; 此類別是整個屬性動畫 API 中最重要的類別。 它會計算需要變更之屬性的值。 `ViewAnimator` 不會直接更新那些值;相反地，它會引發可用於更新動畫物件的事件。

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) &ndash; 此類別是 `ValueAnimator` 的子類別。 它的目的是要藉由接受目標物件和要更新的屬性，來簡化物件的動畫處理常式。

- [AnimationSet](xref:Android.Animation.AnimatorSet) &ndash; 這個類別會負責協調動畫的相互關聯方式。 動畫可能會依序執行，或在兩者之間有指定的延遲。

*評估*工具是 animators 在動畫期間用來計算新值的特殊類別。 Android 提供下列評估工具：

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; 會計算整數屬性的值。

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; 會計算 float 屬性的值。

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; 會計算色彩屬性的值。

如果要繪製動畫的屬性不是 `float`、`int` 或色彩，應用程式可以藉由執行 `ITypeEvaluator` 介面來建立自己的評估工具。 （執行自訂評估工具已超出本主題的範圍）。

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

任何動畫都有兩個部分：計算動畫值，然後在某個物件的屬性上設定這些值。 
[ValueAnimator](xref:Android.Animation.ValueAnimator)只會計算值，但不會直接在物件上操作。 相反地，物件將會在動畫存留期間所叫用的事件處理常式內更新。 此設計可讓您從一個動畫值更新數個屬性。

您可以藉由呼叫下列其中一個 factory 方法來取得 `ValueAnimator` 的實例：

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

完成後，`ValueAnimator` 實例必須設定其持續時間，然後可以啟動。 下列範例示範如何在1000毫秒的範圍內，以動畫顯示0到1之間的值：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

但是，上述程式碼片段本身並不太實用 &ndash; animator 將會執行，但是更新的值沒有目標。 當 `Animator` 類別決定必須通知新值的接聽程式時，就會引發 Update 事件。 應用程式可以提供事件處理常式來回應此事件，如下列程式碼片段所示：

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

既然我們已經瞭解 `ValueAnimator`，讓我們深入瞭解 `ObjectAnimator`。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator)是 `ViewAnimator` 的子類別，可結合 `ValueAnimator` 的計時引擎和值計算與連接事件處理常式所需的邏輯。 `ValueAnimator` 需要應用程式明確地連接事件處理常式 &ndash; `ObjectAnimator` 會為我們處理此步驟。

`ObjectAnimator` 的 API 與適用于 `ViewAnimator`的 API 非常類似，但您需要提供物件和要更新的屬性名稱。 下列範例顯示使用 `ObjectAnimator`的範例：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

如您在先前的程式碼片段中所見，`ObjectAnimator` 可以減少和簡化以動畫顯示物件所需的程式碼。

### <a name="drawable-animations"></a>繪製動畫

最後的動畫 API 是可繪製的動畫 API。 可繪製的動畫會逐一載入一系列的可繪製資源，並依序顯示它們，類似于「翻轉-it」卡通。

可繪製資源是在 XML 檔案中定義，該檔案具有做為根項目的 `<animation-list>` 專案，以及定義動畫中每個框架的一系列 `<item>` 元素。 這個 XML 檔案會儲存在應用程式的 [`/Resource/drawable`] 資料夾中。 下列 XML 是可繪製動畫的範例：

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

這個動畫將會執行六個畫面格。 `android:duration` 屬性會宣告每個畫面格的顯示時間長度。 下一個程式碼片段顯示一個範例，示範如何建立可繪製的動畫，並在使用者按一下螢幕上的按鈕時啟動它：

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

此時，我們已涵蓋 Android 應用程式中可用動畫 Api 的基礎。

## <a name="summary"></a>總結

本文引進了許多新概念和 API，以協助將一些圖形新增至 Android 應用程式。 首先，它討論了各種2D 圖形 API，並示範了 Android 如何讓應用程式使用 Canvas 物件直接繪製到螢幕上。 我們也看到一些替代的技術，可讓您使用 XML 檔案以宣告方式建立圖形。 然後我們會討論如何在 Android 中建立動畫的新舊 API。

## <a name="related-links"></a>相關連結

- [動畫示範（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/animationdemo)
- [動畫和圖形](https://developer.android.com/guide/topics/graphics/index.html)
- [使用動畫讓您的 Mobile Apps 生活](https://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [Canvas](xref:Android.Graphics.Canvas)
- [物件 Animator](xref:Android.Animation.ObjectAnimator)
- [值 Animator](xref:Android.Animation.ValueAnimator)
