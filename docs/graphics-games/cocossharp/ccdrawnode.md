---
title: 繪製使用 ccdrawnode 建立幾何圖形
description: 本文件說明 ccdrawnode 建立，可提供基本的繪圖物件，例如線條、 圓形和三角形的方法。
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: b910e136366c429de8bd2ba1ac959882b4d7201d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123178"
---
# <a name="drawing-geometry-with-ccdrawnode"></a>繪製使用 ccdrawnode 建立幾何圖形

_`CCDrawNode` 提供方法來繪圖基本的物件，例如線條、 圓形和三角形。_

`CCDrawNode` CocosSharp 中的類別提供多種方法來繪製通用的幾何圖形。 它繼承自`CCNode`類別，並通常會加入至`CCLayer`執行個體。 本指南涵蓋如何使用`CCDrawNode`執行自訂轉譯的執行個體。 它也提供可繪製函式的完整清單螢幕擷取畫面和程式碼範例。


## <a name="creating-a-ccdrawnode"></a>建立 ccdrawnode 建立

`CCDrawNode`類別可以用來繪製如圓形、 矩形和線條的幾何物件。 例如，下列程式碼範例示範如何建立`CCDrawNode`執行個體中繪製圓形`CCLayer`實作類別：


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

此程式碼會產生下列的圓形，在執行階段：

![](ccdrawnode-images/image1.png "此程式碼會產生在執行階段此圓形")


## <a name="draw-method-details"></a>繪製方法詳細資料

讓我們看看一些詳細資料與繪圖相關`CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>繪製位置會相對於 ccdrawnode 建立方法

所有的繪製方法都需要至少一個位置的值進行繪圖。 此位置的值是相對於`CCDrawNode`執行個體。 這表示`CCDrawNode`本身的位置，而且所有繪製呼叫對`CCDrawNode`也需要一或多個位置的值。 若要協助您了解如何結合這些值，讓我們看看一些範例。

第一次我們將探討`DrawCircle`上述範例中：


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

在此情況下，`CCDrawNode`位於 (100,100)，而繪製的圓形是在 (0，0)，相對於`CCDrawNode`，產生的圓形受到置中的 100 像素，最多和右邊的 [遊戲] 畫面的左下角。

`CCDrawNode`也位於來源 （左下角的畫面） 上個位移的圓圈, 信賴憑證者：


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

圓形的中心，50 個單位，會導致上述程式碼 (`drawNode.PositionX`加上`CCPoint.X`) 右邊的左邊畫面中，到 60 (`drawNode.PositionY` + `CCPoint.Y`) 上方畫面底部的單位。

除非，一旦已呼叫 draw 方法，無法修改繪製的物件`CCDrawNode.Clear`呼叫方法，因此任何重新定位在上執行需要`CCDrawNode`本身。

物件所繪製`CCNodes`也會受到`CCNode`執行個體的`Rotation`和`Scale`屬性。


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>不需要呼叫每個畫面格繪製方法

需要只能呼叫一次建立的持續性的視覺效果繪製方法。 在範例中，呼叫`DrawCircle`中的建構函式`GameLayer`–`DrawCircle`不需要呼叫每個畫面格畫面重新整理時，重新繪製圓形。

這不同於在 MonoGame，這通常會轉譯到畫面，以便只有一個框架的項目，而且這必須在呼叫每個畫面格的繪製方法。

如果繪製方法會呼叫每個畫面格，則物件最終將會累積在呼叫`CCDrawNode`執行個體，繪製多個物件會導致畫面播放速率會下降。


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>每個 ccdrawnode 建立支援多個繪製呼叫

`CCDrawNode` 執行個體可用來繪製多個圖案。 這可讓複雜的視覺物件，來抓住單一物件中。 例如，下列程式碼可以用來呈現與其中的多個圓形`CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

這會導致下列圖形：

![](ccdrawnode-images/image2.png "這會導致此圖形")


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

`config`參數會定義哪些曲線會通過資料點。 下列範例顯示透過四個點將傳遞的曲線。

`tension`參數會控制如何明確的或圓形上曲線的點會出現。 A`tension`值為 0 會導致的曲線的曲線和`tension`繪製的直線，線條和硬邊緣的曲線會產生值為 1。

雖然曲線曲線，CocosSharp 繪製直線與曲線。 `segments`參數可讓您控制用來繪製曲線的幾個區段。 較大的數字越高的順暢曲線的曲線，但代價是效能。 

程式碼範例：


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "區段參數可讓您控制幾個區段，用來繪製曲線")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` 建立曲線變動數目的點，類似於透過`DrawCardinalLine`。 這個方法不包含張力參數。

程式碼範例：

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom 建立通過變動數目的點，類似於 DrawCardinalLine 曲線的線條")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` 建立的圓形周長指定`radius`。

程式碼範例：

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle 建立周邊的指定半徑的圓形")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` 兩個點，來設定兩個點之間的路徑使用控點之間繪製曲線。

程式碼範例：

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier 兩點之間繪製曲線")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` 建立外的框*橢圓形*，這通常稱為橢圓形 （雖然這兩個不行業相同）。 可由定義橢圓形的圖形`CCRect`執行個體。

程式碼範例：


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse 建立外框的橢圓形，通常稱為橢圓形")


### <a name="drawline"></a>DrawLine

`DrawLine` 連接到指定寬度的有線條的點。 這個方法很類似`DrawSegment`，但它會建立一般的端點，而不是圓角端點。

程式碼範例：


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine 連接到指定寬度的有線條的點")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` 藉由連接點所指定的每個配對建立多行`CCV3F_C4B`陣列。 `CCV3F_C4B`結構包含的位置和色彩值。 `verts`參數一律應包含偶數的點，因為每一行定義由兩個點。

程式碼範例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "因為每一行定義由兩個點，verts 參數一律應包含偶數的點，")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` 建立可變寬度和色彩的外框的填滿的多邊形。

程式碼範例：


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon 建立可變寬度和色彩的外框的填滿的多邊形")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` 有一條線連接兩個點。 它的行為類似於`DrawCubicBezier`，但僅支援單一控制點。

程式碼範例：


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier 有一條線連接兩個點")


### <a name="drawrect"></a>DrawRect

`DrawRect` 建立可變寬度和色彩的外框的填滿的矩形。

程式碼範例： 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect 建立可變寬度和色彩的外框的填滿的矩形")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` 以可變寬度和色彩的線連接兩個點。 類似於`DrawLine`，但它會建立圓角端點，而不是一般的端點。

程式碼範例：


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment 連接兩個點一行則為可變寬度和色彩")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` 建立指定的色彩和 radius 的填滿的楔形。

程式碼範例：


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc 建立區域分布在楔形，將指定的色彩和 radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` 建立指定的 radius 的填滿的圓形。

程式碼範例：


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle 建立指定的 radius 的填滿的圓形")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` 會建立一份三角形。 每一個三角形定義三個`CCV3F_C4B`陣列中的執行個體。 陣列中的頂點數目傳遞至`verts`參數必須是 3 的倍數。 請注意，唯一的資訊包含在`CCV3F_C4B`verts 和色彩 – 位置`DrawTriangleList`方法不支援以紋理繪製三角形。

程式碼範例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList 會建立一份三角形")


## <a name="summary"></a>總結

本指南說明如何建立`CCDrawNode`並執行基本項目為基礎的轉譯。 它提供每個繪製呼叫的範例。

## <a name="related-links"></a>相關連結

- [Ccdrawnode 建立 API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [完整的範例](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
