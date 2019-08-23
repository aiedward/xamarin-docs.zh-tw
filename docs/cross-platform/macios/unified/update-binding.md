---
title: 將繫結移轉至 Unified API
description: 本文涵蓋更新現有 Xamarin 系結專案所需的步驟, 以支援 Xamarin. IOS 和 Xamarin 應用程式的整合 Api。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 90ef47d4e105dc401369c92e9196111c060314e3
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976426"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>將繫結移轉至 Unified API

_本文涵蓋更新現有 Xamarin 系結專案所需的步驟, 以支援 Xamarin. IOS 和 Xamarin 應用程式的整合 Api。_

## <a name="overview"></a>總覽

從2015年2月1日開始, 對 iTunes 和 Mac App Store 的所有新提交都必須是64位應用程式。 因此, 任何新的 Xamarin 或 Xamarin 應用程式都必須使用新的 Unified API, 而不是現有的傳統 MonoTouch 和 MonoMac Api, 以支援64位。

此外, 任何 Xamarin 系結專案也必須支援要包含在64位的 Xamarin. iOS 或 Xamarin 專案中的新整合 Api。 本文將涵蓋更新現有系結專案以使用 Unified API 所需的步驟。

## <a name="requirements"></a>需求

需要下列專案, 才能完成本文中顯示的步驟:

- **Visual Studio for Mac** -在開發電腦上安裝和設定的最新版本 Visual Studio for Mac。
- **Apple mac** -建立適用于 IOS 和 Mac 的系結專案需要 apple mac。

Windows 電腦上的 Visual studio 不支援系結專案。

## <a name="modify-the-using-statements"></a>修改 Using 語句

統一的 Api 可讓您比以往更輕鬆地在 Mac 和 iOS 之間共用程式碼, 並可讓您使用相同的二進位檔來支援32和64位應用程式。 藉由卸載命名空間中的_MonoMac_和_MonoTouch_前置詞, 可在 Xamarin. Mac 和 xamarin iOS 應用程式專案中進行更簡單的共用。

因此, 我們需要修改系結合約 (以及系結專案中的其他`.cs`檔案), 以從我們`using`的語句中移除_MonoMac_和_MonoTouch_首碼。

例如, 假設在系結合約中有下列 using 語句:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我們會去除前置詞`MonoTouch` , 產生下列結果:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同樣地, 我們必須針對系結專案中`.cs`的任何檔案執行此動作。 進行這項變更後, 下一個步驟是更新系結專案, 以使用新的原生資料類型。

如需 Unified API 的詳細資訊, 請參閱[Unified API](~/cross-platform/macios/unified/index.md)檔。 如需支援32和64位應用程式的背景, 以及架構的相關資訊, 請參閱[32 和64位平臺考慮](~/cross-platform/macios/32-and-64/index.md)檔。

## <a name="update-to-native-data-types"></a>原生資料類型的更新

目標-C 會將`NSInteger`資料類型對應`int32_t`到`int64_t` 32 位系統上的, 以及64位系統上的。 為了符合此行為, 新的 Unified API 會將先前的`int`使用 (在 .net 中定義為`System.Int32`always as) 取代為新的資料類型: `System.nint`。

除了`nint`新的資料類型之外, Unified API 也`nuint`引進和`nfloat`類型, 以便對應至`NSUInteger`和`CGFloat`類型。

在上述情況下, 我們需要檢查我們的 API, 並確定的任何`NSInteger`實例`NSUInteger` , `CGFloat`以及我們先前已對應`int`至`uint` , `float`並將其更新為`nint`新的、 `nuint` 和`nfloat`類型。

例如, 假設有個的目標-C 方法定義:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

如果先前的系結合約具有下列定義:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

我們會更新新的系結, 如下所示:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

如果我們對應的是較新的協力廠商程式庫, 而不是我們最初連結的版本, 則`.h`需要檢查程式庫的標頭檔, 並查看是否有任何結束`int`、 `int32_t`明確呼叫`uint32_t` 、 `unsigned int`、或。已升級為`NSInteger`、 `NSUInteger`或。`CGFloat` `float` 若是如此, 也必須對`nint`、 `nuint`和`nfloat`類型進行相同的修改。

若要深入瞭解這些資料類型的變更, 請參閱[原生類型](~/cross-platform/macios/nativetypes.md)檔。

## <a name="update-the-coregraphics-types"></a>更新 CoreGraphics 類型

使用32或64位時所使用`CoreGraphics`的點、大小和矩形資料類型, 取決於其執行所在的裝置。 當 Xamarin 最初系結 iOS 和 Mac api 時, 我們使用了現有的資料結構來符合中`System.Drawing`的資料類型 (`RectangleF`例如)。

由於支援64位和新原生資料類型的需求, 在呼叫`CoreGraphic`方法時, 必須對現有的程式碼進行下列調整:

- **Rectanglef** - `RectangleF`在`CGRect`定義浮點矩形區域時, 使用而不是。
- **CGSize** - `SizeF`在`CGSize`定義浮點大小 (寬度和高度) 時使用, 而不是。
- **CGPoint** - `PointF`在`CGPoint`定義浮點位置 (X 和 Y 座標) 時使用, 而不是。

在上述情況下, 我們需要檢查我們的 API `CGRect`, 並確定任何`CGSize`或`CGPoint`先前`RectangleF` `SizeF` `PointF`已系結或變更為原生類型`CGRect`的實例。`CGSize` 或`CGPoint`直接。

例如, 假設指定了的目標-C 初始化運算式:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

如果先前的系結包含下列程式碼:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

我們會將該程式碼更新為:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

現在所有的程式碼變更都已就緒, 我們需要修改系結專案, 或將檔案設為系結至統一的 Api。

## <a name="modify-the-binding-project"></a>修改系結專案

在更新系結專案以使用整合 api 的最後一個步驟中, 我們需要修改用來建立`MakeFile`專案或 Xamarin 專案類型的 (如果我們是從 Visual Studio for Mac 內進行系結), 並指示_btouch_系結針對統一的 Api, 而不是傳統的 Api。


### <a name="updating-a-makefile"></a>更新 MakeFile

如果我們使用 makefile 將系結專案建立成 Xamarin。DLL, 我們必須包含`--new-style`命令列選項並呼叫`btouch-native` , 而不是`btouch`。

因此, 請考慮`MakeFile`下列事項:

<!--markdownlint-disable MD010 -->
```makefile
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

我們需要從呼叫`btouch`切換到`btouch-native`, 所以我們會調整巨集定義, 如下所示:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

我們會更新對的呼叫`btouch` , 並`--new-style`新增選項, 如下所示:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

我們現在可以`MakeFile`照常執行, 以建立 API 的新64位版本。

### <a name="updating-a-binding-project-type"></a>更新系結專案類型

如果我們使用 Visual Studio for Mac 系結專案範本來建立我們的 API, 則必須更新為新的 Unified API 版本的系結專案範本。 若要這麼做, 最簡單的方法就是啟動新的 Unified API 系結專案, 並複製所有現有的程式碼和設定。

請執行下列動作：

1. 啟動 Visual Studio for Mac。
2.  > 選取[檔案][新增方案...] > 
3. 在 [新增解決方案] 對話方塊中, 選取 [ **ios**  >  **Unified API**  > ios 系結**專案**]: 

    [![](update-binding-images/image01new.png "在新的 [方案] 對話方塊中，選取 iOS / 統一的 API / iOS 繫結專案")](update-binding-images/image01new.png#lightbox)
4. 在 [設定您的新專案] 對話方塊中, 輸入新系結專案的**名稱**, 然後按一下 [**確定]** 按鈕。
5. 包含您要為其建立系結的64位版本的目標-C 程式庫。
6. 將原始程式碼從現有的32位複製 Classic API 系結專案 (例如`ApiDefinition.cs`和`StructsAndEnums.cs`檔案)。
7. 對原始程式碼檔案進行上述已記錄的變更。

當所有這些變更都備妥之後, 您就可以建立新的64位版本的 API, 就像是32位版本一樣。

## <a name="summary"></a>總結

在本文中, 我們示範了需要對現有的 Xamarin 系結專案所做的變更, 以支援新的統一 Api 和64位裝置, 以及建立新的64位相容版本的 API 所需的步驟。



## <a name="related-links"></a>相關連結

- [Mac 和 iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md)
- [升級現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
