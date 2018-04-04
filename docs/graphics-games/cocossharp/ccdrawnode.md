---
title: 繪製與 CCDrawNode 幾何
description: CCDrawNode 提供繪圖基本的物件，例如線條、 圓形和三角形的方法。
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 2a4278f97a69ef9eb705c6fedf1a5c7ec98875b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="drawing-geometry-with-ccdrawnode"></a>繪製與 CCDrawNode 幾何

_`CCDrawNode` 提供方法來繪圖基本的物件，例如線條、 圓形和三角形。_

`CCDrawNode`類別 CocosSharp 中的提供多種方法來繪製一般幾何圖案。 它繼承自`CCNode`類別，並通常會加入至`CCLayer`執行個體。 本指南涵蓋如何使用`CCDrawNode`執行自訂轉譯的執行個體。 它也提供可繪製函式的完整清單，螢幕擷取畫面和程式碼範例。


## <a name="creating-a-ccdrawnode"></a>建立 CCDrawNode

`CCDrawNode`類別可以用來繪製幾何物件，例如圓形、 矩形和線條。 例如，下列程式碼範例示範如何建立`CCDrawNode`執行個體中繪製圓形`CCLayer`實作類別：


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

此程式碼會產生下列的圓形，在執行階段：

![](ccdrawnode-images/image1.png "此程式碼會產生此在執行階段的圓形")


## <a name="draw-method-details"></a>Draw 方法詳細資料

讓我們看看一些詳細資訊與繪圖相關`CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>繪製位置是相對於 CCDrawNode 方法

所有繪製方法繪圖都需要至少一個位置值。 此位置的值是相對於`CCDrawNode`執行個體。 這表示`CCDrawNode`本身具有位置，以及所有繪製呼叫對`CCDrawNode`也接受一或多個位置的值。 若要協助您了解如何將這些值結合，讓我們看看一些範例。

首先我們會探討`DrawCircle`上述範例：


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

在此情況下，`CCDrawNode`位於 (100100)，而繪製的圓形是在 (0，0)，相對於`CCDrawNode`，並產生最多和右邊的 [遊戲] 畫面的左下角的置中對齊的 100 個像素的圓形。

`CCDrawNode`也位於來源 （左下方畫面），依賴圓形的位移：


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

在 50 個單位為圓形的中心會導致上方的程式碼 (`drawNode.PositionX` + `CCPoint.X`) 右邊的左下方畫面中和 60 (`drawNode.PositionY` + `CCPoint.Y`) 上方畫面底部的單位。

除非，一旦繪製方法呼叫之後不修改繪製的物件`CCDrawNode.Clear`方法呼叫，所以任何重新調整位置必須進行的`CCDrawNode`本身。

物件所繪製的`CCNodes`也受到`CCNode`執行個體的`Rotation`和`Scale`屬性。


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>不需要呼叫每個畫面格繪製方法

Draw 方法需要一次呼叫，以建立持續性的視覺效果。 在呼叫上述範例`DrawCircle`的建構函式中`GameLayer`–`DrawCircle`不需要呼叫每個畫面格時重新整理畫面，重新繪製圓形。

這不同於繪製 MonoGame，這通常會呈現某個動作，只有一個畫面格，螢幕，而且這必須在呼叫每個框架中的方法。

如果繪製呼叫的每個畫面格，則物件最終將會累積內呼叫`CCDrawNode`執行個體，導致畫面播放速率下降會繪製多個物件。


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>每個 CCDrawNode 支援多個繪製呼叫

`CCDrawNode` 執行個體可以用來繪製多個圖形。 這可讓複雜抓住單一物件中的視覺物件。 例如，下列程式碼可以用來呈現多個包含一個圓形`CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

這會導致下列圖形：

![](ccdrawnode-images/image2.png "這會導致這個圖形")


## <a name="draw-call-examples"></a>繪製呼叫範例

下列的繪製呼叫位於`CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` 建立曲線透過變動數目的點。 

`config`參數會定義會通過曲線的點。 下列範例會示範這四個點所通過的曲線。

`tension`參數會控制如何尖銳或 round 曲線的點會出現。 A`tension`值為 0 會導致的曲線的曲線和`tension`1 的值將會導致繪製的直線和硬邊緣的曲線。

雖然曲線曲線，但 CocosSharp 繪製直線與曲線。 `segments`參數控制多少區段用來繪製曲線。 但要付出效能的順暢曲線的曲線會產生較大的數目。 

程式碼範例：


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "區段參數用於控制多少區段用來繪製曲線")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` 建立曲線變動數目的點，類似於透過`DrawCardinalLine`。 這個方法不包含張力參數。

程式碼範例：

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom 建立透過變動數目的點，類似於 DrawCardinalLine 曲線")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` 建立的圓形的周邊給定`radius`。

程式碼範例：

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle 建立周邊的指定半徑的圓形")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` 若要設定兩個點之間路徑使用控點的兩個點之間繪製曲線。

程式碼範例：

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier 兩點之間繪製曲線")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` 建立的大綱*橢圓形*，這通常稱為橢圓形 （雖然這兩個不形成的幾何相同）。 可以由定義橢圓形的圖形`CCRect`執行個體。

程式碼範例：


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse 建立外框的橢圓形，通常稱為橢圓形")


### <a name="drawline"></a>DrawLine

`DrawLine` 連接點一行則為指定的寬度。 這個方法是類似於`DrawSegment`，只不過它會建立一般的端點，而不是圓角端點。

程式碼範例：


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine 連接點一行則為指定的寬度")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` 藉由連接點所指定的每個配對建立多行`CCV3F_C4B`陣列。 `CCV3F_C4B`結構包含的位置和色彩值。 `verts`參數一律應包含偶數個點，每一行是由兩個點定義。

程式碼範例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Verts 參數一律應包含偶數個點，每一行是由兩個點定義")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` 建立變數的寬度和色彩的外框的填滿的多邊形。

程式碼範例：


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon 建立填滿的多邊形變數寬度和色彩的外框")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` 有一條線連接兩個點。 它的行為類似於`DrawCubicBezier`但僅支援單一控制點。

程式碼範例：


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier 有一條線連接兩個點")


### <a name="drawrect"></a>DrawRect

`DrawRect` 建立變數的寬度和色彩的外框的填滿的矩形。

程式碼範例： 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect 建立變數的寬度和色彩的外框的填滿的矩形")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` 有變數的寬度和色彩的一條線連接兩個點。 類似於`DrawLine`，只不過它會建立圓形的端點，而不是一般的端點。

程式碼範例：


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment 有變數的寬度和色彩的一條線連接兩個點")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` 建立指定的色彩和 radius 填滿的楔形。

程式碼範例：


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc 建立區域分布在楔形，將指定的色彩和 radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` 建立指定半徑的填滿的圓形。

程式碼範例：


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle 建立指定半徑的填滿的圓形")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` 建立一份三角形。 每個三角形會定義三個`CCV3F_C4B`陣列中的執行個體。 傳遞至陣列中的頂點數目`verts`參數必須是 3 的倍數。 請注意，只有資訊包含在`CCV3F_C4B`verts 和其色彩 – 位置`DrawTriangleList`方法不支援以紋理繪製三角形。

程式碼範例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList 建立一份三角形")


## <a name="summary"></a>總結

本指南說明如何建立`CCDrawNode`並執行基本項目為基礎的轉譯。 它提供每個繪製呼叫的範例。

## <a name="related-links"></a>相關的連結

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [完整範例](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
