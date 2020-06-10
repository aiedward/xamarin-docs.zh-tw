---
title： "動畫 in Xamarin.Forms " 描述： " Xamarin.Forms 包含自己的動畫基礎結構，可直接建立簡單的動畫，同時也有足夠的空間可建立複雜的動畫。"
assetid： AC0B4127-ECA3-44DA-8A24-A2B10A275083 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/14/2016 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="animation-in-xamarinforms"></a>動畫于Xamarin.Forms

_Xamarin 包含自己的動畫基礎結構，非常適合用來建立簡單的動畫，同時也有足夠的空間來建立複雜的動畫。_

Xamarin.Forms動畫類別的目標是視覺元素的不同屬性，而一般動畫會在一段時間後，將屬性從某個值逐漸變更為另一個。 請注意，沒有適用于動畫類別的 XAML 介面 Xamarin.Forms 。 不過，動畫可以封裝在[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)中，然後從 XAML 參考。

## <a name="simple-animations"></a>[簡單動畫](simple.md)

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供擴充方法，可用於建立旋轉、縮放、轉譯和淡化實例的簡單動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 本文示範如何使用類別來建立和取消動畫 `ViewExtensions` 。

## <a name="easing-functions"></a>[Easing 函式](easing.md)

Xamarin.Forms包含 [`Easing`](xref:Xamarin.Forms.Easing) 類別，可讓您指定傳送函式，以控制動畫在執行時的速度或緩慢。 本文示範如何使用預先定義的緩動函式，以及如何建立自訂的緩動函數。

## <a name="custom-animations"></a>[自訂動畫](custom.md)

[`Animation`](xref:Xamarin.Forms.Animation)類別是所有動畫的建立區塊 Xamarin.Forms ，而類別中的擴充方法會 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 建立一或多個 `Animation` 物件。 本文示範如何使用 `Animation` 類別來建立和取消動畫、同步處理多個動畫，以及建立自訂動畫，以動畫顯示現有動畫方法不會產生動畫的屬性。
