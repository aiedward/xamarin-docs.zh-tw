---
title: 圖形和動畫
description: Android 提供非常豐富且多樣化的架構，可支援2D 圖形和動畫。 本主題介紹這些架構，並討論如何建立自訂的圖形和動畫以用於 Xamarin Android 應用程式。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 41ebc8b1f89d814fa15fc3157ae497e1e8fb32b4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456131"
---
# <a name="android-graphics-and-animation"></a>Android 圖形和動畫

_Android 提供非常豐富且多樣化的架構，可支援2D 圖形和動畫。本主題介紹這些架構，並討論如何建立自訂的圖形和動畫以用於 Xamarin Android 應用程式。_

## <a name="overview"></a>概觀

儘管是在傳統上受限制的裝置上執行，但最高評等的行動應用程式通常會有精密的使用者體驗 (UX) ，以高品質的圖形和動畫來完成，以提供直覺、具回應性、動態的感覺。 當行動應用程式變得越來越複雜時，使用者已開始預期更多應用程式。

幸運的是，現代化的行動平臺具有非常強大的架構，可用來建立複雜的動畫和自訂圖形，同時保留易用性。 這可讓開發人員輕鬆地加入豐富的互動性。

Android 中的 UI API 架構大致可以分為兩類：圖形和動畫。

圖形會進一步分割成不同的方法來進行2D 和3D 圖形。 3D 圖形可透過數個內建的架構（例如 OpenGL ES） (特定的 OpenGL) 版本，以及協力廠商架構（例如 MonoGame） (跨平臺工具組，) 。 雖然3D 圖形不在本文的討論範圍內，但我們會檢查內建2D 繪圖技術。

Android 提供兩種不同的 API 來建立2D 圖形。 其中一個是高階宣告式方法，另一個是程式設計低層級 API：

- **繪製資源** &ndash; 這些是用來建立以程式設計方式建立的自訂圖形，或是藉由在 XML 檔案中內嵌繪圖指令來 (更常見的) 。 可繪製的資源通常會定義為 XML 檔案，其中包含 Android 轉譯2D 圖形的指示或動作。 

- **Canvas** &ndash; 這是低層級的 API，牽涉到直接在基礎點陣圖上進行繪製。 它會對顯示的內容提供非常細微的控制。 

除了這些2D 圖形技術之外，Android 也提供數種不同的方式來建立動畫：

- **繪製動畫** &ndash; Android 也支援框架逐幀動畫，稱為可 *繪製動畫*。 這是最簡單的動畫 API。 Android 依序載入和顯示依序 (的繪製資源，就像卡通) 一樣。

- **視圖動畫** &ndash;*視圖動畫*是原始的動畫 API，在 android 的所有版本中都有提供。 此 API 僅適用于 View 物件，且只能在這些視圖上執行簡單的轉換。
    視圖動畫通常是在資料夾中找到的 XML 檔案中定義 `/Resources/anim` 。

- **屬性動畫** &ndash; Android 3.0 引進一組新的動畫 API，稱為 *屬性動畫*。 這些新的 API 引進了可擴充且彈性的系統，可用來建立任何物件的屬性動畫，而不只是 View 物件。 這種彈性可將動畫封裝在不同的類別中，讓程式碼共用更容易。

視圖動畫更適合必須支援舊版 Android 之前的 3.0 API (API 層級 11) 的應用程式。 否則，應用程式應該使用較新的屬性動畫 API，以取得上述的原因。

所有這些架構都是可行的選項，不過，如果可能的話，應該將喜好設定提供給屬性動畫，因為這是更有彈性的 API 可使用。 屬性動畫可讓動畫邏輯封裝在不同的類別中，讓程式碼共用更加容易，並簡化程式碼維護。

## <a name="accessibility"></a>協助工具選項

圖形和動畫可協助讓 Android 應用程式具吸引力且有趣的使用;不過，請務必記住，某些互動是透過螢幕助讀程式、替代輸入裝置或輔助顯示進行。
此外，某些互動可能會在沒有音訊功能的情況下發生。

在這些情況下，如果應用程式已設計為可存取協助工具，應用程式會更有用：在使用者介面中提供提示和導覽協助，以及確保 UI 的圖解元素有文字內容或描述。

如需如何利用 Android 協助工具 Api 的詳細資訊，請參閱 [Google 的協助工具指南](https://developer.android.com/guide/topics/ui/accessibility/) 。

## <a name="2d-graphics"></a>2D 圖形

可繪製的資源是 Android 應用程式中常用的技巧。 如同其他資源，可繪製的資源是 &ndash; 以 XML 檔案定義的。 這種方法可讓您從資源中清楚分隔程式碼。 這可以簡化開發和維護，因為不需要變更程式碼來更新或變更 Android 應用程式中的圖形。 不過，雖然可繪製資源適用于許多簡單且常見的圖形需求，但卻缺乏畫布 API 的電源和控制能力。

使用 [Canvas](xref:Android.Graphics.Canvas) 物件的其他技巧，與其他傳統 API 架構（例如 System. 繪圖或 IOS 的核心繪圖）非常類似。 使用 Canvas 物件可充分掌控2D 圖形的建立方式。 它適用于可繪製資源無法運作或不容易使用的情況。 例如，可能需要繪製自訂滑杆控制項，其外觀會根據與滑杆值相關的計算而變更。

讓我們先檢查可繪製的資源。 它們更簡單，且涵蓋最常見的自訂繪圖案例。

### <a name="drawable-resources"></a>繪製資源

可繪製的資源會定義在目錄中的 XML 檔案中 `/Resources/drawable` 。 與內嵌 PNG 或 JPEG 不同的是，不需要提供可繪製資源的密度特定版本。
在執行時間，Android 應用程式將會載入這些資源，並使用這些 XML 檔案中包含的指示來建立2D 圖形。
Android 定義數種不同類型的繪製資源：

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; 這是可繪製基本幾何圖形的可繪製物件，並在該圖形上套用一組有限的圖形效果。 它們對於自訂按鈕或設定 Textview 背景很有用。 我們將在本文稍後看到如何使用的範例 `ShapeDrawable` 。

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; 這是可繪製的資源，會根據 widget/控制項的狀態變更外觀。 例如，按鈕可能會根據是否已按下來變更其外觀。

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; 這項可繪製的資源會將其他可繪製資源堆疊在另一個。 下列螢幕擷取畫面顯示 *LayerDrawable* 的範例：

    ![LayerDrawable 範例](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; 這是 *LayerDrawable* ，但有一項差異。 *TransitionDrawable*能夠以動畫顯示一個圖層。

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; 這非常類似于 *StateListDrawable* ，因為它會根據特定條件顯示影像。 不過，不同于 *StateListDrawable*， *LevelListDrawable* 會根據整數值顯示影像。 *LevelListDrawable*的範例是顯示 WiFi 信號的強度。 當 WiFi 信號的強度變更時，顯示的可繪製將會隨之變更。

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale) /[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;顧名思義，這些可繪製資源會提供調整和裁剪功能。 *ScaleDrawable*會擴充另一個可繪製的，而*ClipDrawable*則會裁剪另一個可繪製的。

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; 此可繪製會將內凹套用至另一個可繪製資源的側邊。 當視圖需要的背景小於視圖的實際範圍時，就會使用它。

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; 此檔案是一組以 XML 表示的指令，可在實際的點陣圖上執行。 Android 可以執行的某些動作包括平鋪、遞色和消除鋸齒。 其中一個很常見的用途，就是在版面配置的背景上並排顯示點陣圖。

#### <a name="drawable-example"></a>繪製範例

讓我們來看看如何使用建立2D 圖形的簡單範例 `ShapeDrawable` 。 `ShapeDrawable`可以定義四個基本圖形中的其中一個：矩形、橢圓形、線條和環形。 您也可以套用基本效果，例如漸層、色彩和大小。 下列 XML 是可 `ShapeDrawable` 在 *AnimationsDemo* 附屬專案 (的檔案) 中找到的 `Resources/drawable/shape_rounded_blue_rect.xml` 。
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

我們可以在版面配置或其他可繪製的方式中，以宣告方式參考此可繪製資源，如下列 XML 所示：

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

若要查看這看起來的樣子，請執行 *AnimationsDemo* 專案，然後從主功能表中選取圖形可繪製的專案。 我們應該會看到類似下列螢幕擷取畫面的內容：

[![具有自訂背景的 Textview，以漸層和圓角繪製](graphics-and-animation-images/image2-sml.png)](graphics-and-animation-images/image2.png#lightbox)

如需有關 XML 元素和可繪製資源之語法的詳細資訊，請參閱 [Google 的檔](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。

### <a name="using-the-canvas-drawing-api"></a>使用畫布繪圖 API

可繪製資源功能強大，但是有其限制。 某些事物可能不可能或非常複雜 (例如：將篩選套用到裝置上的相機拍攝的圖片) 。 使用可繪製的資源來減少紅眼，會很難。
取而代之的是，畫布 API 可讓應用程式有很細微的控制，以選擇性地變更圖片特定部分的色彩。

通常與畫布搭配使用的一個類別是 [油漆](xref:Android.Graphics.Paint) 類別。 這個類別會保存如何繪製的色彩和樣式資訊。 它是用來提供色彩和透明度等專案。

畫布 API 會使用 *油漆的模型* 來繪製2d 圖形。
作業會在彼此的後續層級中套用。 每項作業都會涵蓋基礎點陣圖的某個區域。 當區域與先前繪製的區域重迭時，新的繪圖將會部分或完全遮蓋舊的。 這與其他許多繪圖 Api （例如，System. Drawing 和 iOS 的核心圖形）的使用方式相同。

有兩種方式可以取得 `Canvas` 物件。 第一種方式包含定義 [點陣圖](xref:Android.Graphics.Bitmap) 物件，然後 `Canvas` 使用它來具現化物件。 例如，下列程式碼片段會使用基礎點陣圖來建立新的畫布：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

取得物件的另一種方式 `Canvas` 是透過提供[View](xref:Android.Views.View)基類的[OnDraw](xref:Android.Views.View.OnDraw*)回呼方法。 當 Android 決定視圖需要自行繪製，並在物件中傳遞要使用的視圖時，Android 會呼叫這個方法 `Canvas` 。

Canvas 類別會公開方法，以程式設計方式提供繪製指示。 例如：

- [DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*)會 &ndash; 以指定的繪圖填滿整個畫布的點陣圖。

- [DrawPath](xref:Android.Graphics.Canvas.DrawPath*)會 &ndash; 使用指定的繪圖繪製指定的幾何形狀。

- [DrawText](xref:Android.Graphics.Canvas.DrawText*)會 &ndash; 以指定的色彩在畫布上繪製文字。 文字會在位置繪製 `x,y` 。

#### <a name="drawing-with-the-canvas-api"></a>使用畫布 API 進行繪製

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

上述程式碼會先建立紅色油漆和綠色油漆物件。 它會以紅色填滿畫布的內容，然後指示畫布繪製一個綠色矩形，其為畫布寬度的25%。 您可以在本文的原始程式碼隨附的專案中看到這種情況的範例 `AnimationsDemo` 。 藉由啟動應用程式，然後從主功能表中選取繪圖專案，我們應該會有類似下列的畫面：

[![具有紅色油漆和綠色油漆物件的螢幕](graphics-and-animation-images/image3-sml.png)](graphics-and-animation-images/image3.png#lightbox)

## <a name="animation"></a>動畫

使用者就像是在其應用程式中移動的東西。 動畫是改善應用程式使用者體驗的絕佳方法，並協助它脫穎而出。最佳的動畫是使用者不會注意到的動畫，因為它們感覺很自然。 Android 提供下列三個用於動畫的 API：

- **觀看動畫** &ndash; 這是原始的 API。 這些動畫會系結至特定的視圖，而且可以在視圖的內容上執行簡單的轉換。 因為很簡單，所以此 API 仍適用于 Alpha 動畫、旋轉等等等專案。

- **屬性動畫** &ndash; 屬性動畫是在 Android 3.0 中引進。 它們可讓應用程式幾乎都能建立動畫。 您可以使用屬性動畫來變更任何物件的任何屬性，即使該物件在螢幕上看不見。

- **繪製動畫** &ndash; 這是特殊的可繪製資源，可用來將非常簡單的動畫效果套用至版面配置。

一般而言，屬性動畫是慣用的系統，因為它更有彈性，並提供更多功能。

### <a name="view-animations"></a>視圖動畫

視圖動畫僅限於視圖，而且只能在開始和結束點、大小、旋轉和透明度等值上執行動畫。
這些類型的動畫通常稱為「 *補間動畫*」。 您可以 &ndash; 在程式碼中以程式設計方式或使用 XML 檔案，以程式設計方式定義 View 動畫。 XML 檔案是宣告視圖動畫的慣用方式，因為它們更容易閱讀且更容易維護。

動畫 XML 檔案將儲存在 `/Resources/anim` Xamarin. Android 專案的目錄中。 這個檔案必須有下列其中一個元素做為根項目：

- `alpha`&ndash;淡入或淡出動畫。

- `rotate`&ndash;旋轉動畫。

- `scale`&ndash;調整動畫的大小。

- `translate`&ndash;水準和/或垂直動作。

- `set`&ndash;可能保存一或多個其他動畫元素的容器。

根據預設，XML 檔案中的所有動畫都會同時套用。 若要依序執行動畫，請 `android:startOffset` 在上面定義的其中一個元素上設定屬性。

您可以使用 *插*即用來影響動畫的變化率。 插即用可以加速、重複或 decelerated 動畫效果。 Android framework 提供數個現成的 interpolators，例如 (但不限於) ：

- `AccelerateInterpolator`/ `DecelerateInterpolator` &ndash;這些 interpolators 會增加或減少動畫的變化率。

- `BounceInterpolator`&ndash;變更會在結束時彈跳。

- `LinearInterpolator`&ndash;變更的速率是常數。

下列 XML 會顯示結合其中一些元素的動畫檔案範例：

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

此動畫會同時執行所有動畫。 第一個調整動畫會水準延展影像，並垂直將它縮小，然後影像會同時旋轉45度計數器，並從螢幕中消失。

您可以藉由因而誇大動畫，然後將動畫套用至視圖，以程式設計方式將動畫套用至視圖。 Android 提供 helper 類別，可擴充 `Android.Views.Animations.AnimationUtils` 動畫資源並傳回的實例 `Android.Views.Animations.Animation` 。 這個物件會藉由呼叫和傳遞物件來套用至 View `StartAnimation` `Animation` 。 下列程式碼片段顯示此範例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

既然我們已對 View 動畫的運作方式有基本瞭解，讓我們移至屬性動畫。

### <a name="property-animations"></a>屬性動畫

屬性 animators 是在 Android 3.0 中引進的新 API。
它們提供更具擴充性的 API，可用來建立任何物件上任何屬性的動畫。

所有屬性動畫都是由 [Animator](xref:Android.Animation.Animator) 子類別的實例所建立。 應用程式不會直接使用這個類別，而是使用它的其中一個子類別：

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; 這個類別是整個屬性動畫 API 中最重要的類別。 它會計算需要變更的屬性值。 不 `ViewAnimator` 會直接更新這些值; 相反地，它會引發可用於更新動畫物件的事件。

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) &ndash; 這個類別是的子類別 `ValueAnimator` 。 它的目的是要藉由接受目標物件和屬性來更新，以簡化物件的動畫處理常式。

- [AnimationSet](xref:Android.Animation.AnimatorSet) &ndash; 這個類別負責協調動畫彼此之間的關聯性。 動畫可能會以順序方式同時執行，或在兩者之間有指定的延遲。

*評估* 工具是 animators 在動畫期間用來計算新值的特殊類別。 Android 提供下列評估工具：

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; 計算整數屬性的值。

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; 計算 float 屬性的值。

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; 計算色彩屬性的值。

如果要製作動畫的屬性不是 `float` `int` 或色彩，應用程式可能會藉由執行介面來建立自己的評估工具 `ITypeEvaluator` 。 執行自訂評估工具的 (已超出本主題的範圍。 ) 

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

任何動畫都有兩個部分：計算動畫值，然後在某個物件上設定這些屬性的值。 
[ValueAnimator](xref:Android.Animation.ValueAnimator) 只會計算值，但不會直接在物件上運作。 相反地，物件將會在動畫生命週期期間叫用的事件處理常式內更新。 這項設計可讓您從某個動畫值更新數個屬性。

您可以藉 `ValueAnimator` 由呼叫下列其中一個 factory 方法來取得的實例：

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

完成之後， `ValueAnimator` 實例必須設定其持續時間，然後可以啟動。 下列範例示範如何在1000毫秒的範圍內，以動畫顯示0到1的值：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

但是，上述程式碼片段並不太實用， &ndash; 因為 animator 會執行，但沒有更新值的目標。 `Animator`當此類別決定必須通知接聽程式有新值時，就會引發 Update 事件。 應用程式可能會提供事件處理常式來回應此事件，如下列程式碼片段所示：

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

既然我們已瞭解 `ValueAnimator` ，讓我們進一步瞭解 `ObjectAnimator` 。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) 是的子類別 `ViewAnimator` ，它會結合的計時引擎和值計算 `ValueAnimator` 與連接事件處理常式所需的邏輯。 `ValueAnimator`需要應用程式明確地連接事件處理常式，才能 &ndash; `ObjectAnimator` 為我們處理這個步驟。

的 API 與 `ObjectAnimator` 的 api 非常類似 `ViewAnimator` ，但需要您提供物件和要更新之屬性的名稱。 下列範例顯示使用的範例 `ObjectAnimator` ：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

如您在先前的程式碼片段中所見， `ObjectAnimator` 可以減少和簡化將物件製作成動畫所需的程式碼。

### <a name="drawable-animations"></a>繪製動畫

最終的動畫 API 是可繪製的動畫 API。 可繪製的動畫會依序載入一連串可繪製的資源，並依序顯示，類似于翻轉 it 的卡通。

可繪製的資源是在 XML 檔案中定義，其專案 `<animation-list>` 為根項目，以及 `<item>` 定義動畫中每個框架的一系列元素。 這個 XML 檔案會儲存在 `/Resource/drawable` 應用程式的資料夾中。 下列 XML 是可繪製動畫的範例：

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

此動畫將會執行六個畫面格。 `android:duration`屬性會宣告每個畫面格的顯示時間長度。 下一個程式碼片段示範如何建立可繪製的動畫，並在使用者按一下螢幕上的按鈕時啟動它：

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

至此，我們已討論過 Android 應用程式中可用的動畫 Api 基礎。

## <a name="summary"></a>摘要

本文引進了許多新概念和 API，可協助您將某些圖形新增至 Android 應用程式。 首先，它會討論各種2D 圖形 API，並示範 Android 如何讓應用程式使用 Canvas 物件直接繪製到螢幕。 我們也看到一些替代技術，可讓您使用 XML 檔案以宣告方式建立圖形。 接著，我們會討論在 Android 中建立動畫的舊 API 和新 API。

## <a name="related-links"></a>相關連結

- [動畫示範 (範例) ](/samples/xamarin/monodroid-samples/animationdemo)
- [動畫和圖形](https://developer.android.com/guide/topics/graphics/index.html)
- [使用動畫讓您的 Mobile Apps 生活](https://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [畫布](xref:Android.Graphics.Canvas)
- [物件 Animator](xref:Android.Animation.ObjectAnimator)
- [值 Animator](xref:Android.Animation.ValueAnimator)