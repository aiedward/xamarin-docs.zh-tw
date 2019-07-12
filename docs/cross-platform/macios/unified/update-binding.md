---
title: 將繫結移轉至 Unified API
description: 本文章涵蓋更新現有 Xamarin 繫結的專案以支援 Unified Api Xamarin.IOS 和 Xamarin.Mac 的應用程式所需的步驟。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: f081ccda507fe3fe65af0e2fb50841aecd7b3c23
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830458"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>將繫結移轉至 Unified API

_本文章涵蓋更新現有 Xamarin 繫結的專案以支援 Unified Api Xamarin.IOS 和 Xamarin.Mac 的應用程式所需的步驟。_

## <a name="overview"></a>總覽

從 2015 年 2 月 1 日起，Apple 會要求所有新的提交至 iTunes 和 Mac App Store，必須是 64 位元應用程式。 如此一來，任何新的 Xamarin.iOS 或 Xamarin.Mac 應用程式必須使用新的統一 API 而不是現有的傳統 MonoTouch 和 MonoMac Api 以支援 64 位元。

此外，任何與 Xamarin 繫結的專案也必須支援 64 位元 Xamarin.iOS 或 Xamarin.Mac 專案中要包含新統一 Api。 本文章會說明更新現有的繫結專案以使用統一的 API 所需的步驟。

## <a name="requirements"></a>需求

需要下列項目來完成這篇文章所述的步驟：

- **Visual Studio for Mac** -最新版本的 Visual Studio for Mac 上安裝並設定開發電腦。
- **Apple Mac** -的 Apple mac，才能建立繫結的專案，適用於 iOS 和 mac。

在 Windows 電腦上的 Visual studio 中不支援繫結專案。

## <a name="modify-the-using-statements"></a>修改 Using 陳述式

統一的 Api 可讓它比以往更容易 Mac 和 iOS，以及可讓您以支援 32 和 64 位元應用程式具有相同二進位之間共用程式碼。 卸除_MonoMac_並_MonoTouch_前置詞的命名空間中，在跨 Xamarin.Mac 與 Xamarin.iOS 應用程式專案達成更簡單的共用。

因此我們必須修改任何我們繫結的合約 (和其他`.cs`我們繫結的專案中的檔案) 來移除_MonoMac_並_MonoTouch_前置詞，從我們`using`陳述式。

例如，假設有下列繫結合約中使用陳述式：

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我們會移除`MonoTouch`前置詞產生下列結果：

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同樣地，我們必須這麼做的任何`.cs`我們繫結專案檔中的。 此變更之後下, 一步是將我們繫結專案更新為使用新的原生資料類型。

如需有關 Unified API 的詳細資訊，請參閱[統一 API](~/cross-platform/macios/unified/index.md)文件。 如需有關支援 32 和 64 位元應用程式和架構的相關資訊的詳細背景，請參閱[32 和 64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件。

## <a name="update-to-native-data-types"></a>更新原生資料類型

OBJECTIVE-C 對應`NSInteger`資料類型`int32_t`32 位元系統上以及`int64_t`64 位元系統上。 為了符合此行為，新的統一 API 會取代先前使用`int`(在.NET 會定義為永遠`System.Int32`) 成新的資料類型： `System.nint`。

以及新`nint`資料類型，統一的 API 導入了`nuint`並`nfloat`類型，對應至`NSUInteger`和`CGFloat`類型。

假設上述，我們需要檢閱我們的 API，並確保其中的任何執行個體`NSInteger`，`NSUInteger`並`CGFloat`我們先前對應至`int`，`uint`並`float`更新為新`nint`， `nuint`和`nfloat`型別。

例如，假設 OBJECTIVE-C 方法定義：

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

如果先前的繫結合約有下列定義：

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

我們會更新為新的繫結：

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
如果我們要對應至較新版本第 3 個合作對象文件庫比什麼我們有一開始是連結到，我們需要檢閱`.h`標頭檔，如果有的話，請參閱文件庫的現有，明確呼叫`int`， `int32_t`， `unsigned int`，`uint32_t`或`float`尚未升級為`NSInteger`，`NSUInteger`或`CGFloat`。 如果是的話，在同一個已修改`nint`，`nuint`和`nfloat`類型必須為他們的對應，也必須對。

若要深入了解這些資料類型變更，請參閱[原生類型](~/cross-platform/macios/nativetypes.md)文件。

## <a name="update-the-coregraphics-types"></a>更新 CoreGraphics 類型

搭配使用的點、 大小和矩形資料型別`CoreGraphics`使用根據裝置執行的 32 或 64 位元。 我們的 iOS 和 Mac Api，Xamarin 原本繫結時使用現有的資料結構來比對中的資料類型發生`System.Drawing`(`RectangleF`例如)。

支援 64 位元和新的原生資料類型的需求，因為呼叫時，必須對現有的程式碼需要下列調整`CoreGraphic`方法：

- **CGRect** -使用`CGRect`而不是`RectangleF`時定義浮動點的矩形區域。
- **CGSize** -使用`CGSize`而不是`SizeF`定義浮點數大小 （「 寬度 」 和 「 高度 」） 時。
- **CGPoint** -使用`CGPoint`而不是`PointF`時定義的浮動點位置 （X 和 Y 座標）。

假設上述，我們必須檢閱我們的 API，並確保其中的任何執行個體`CGRect`，`CGSize`或是`CGPoint`，之前繫結至`RectangleF`，`SizeF`或`PointF`變更為原生型別`CGRect`，`CGSize`或`CGPoint`直接。

例如，假設的 Objective C 初始設定式：

```csharp
- (id)initWithFrame:(CGRect)frame;
```

如果我們先前的繫結包含下列程式碼：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

我們會更新該程式碼：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

所有的程式碼中的變更現在的地方，我們需要修改我們繫結的專案，或建立對統一的 Api 繫結檔案。

## <a name="modify-the-binding-project"></a>修改繫結專案

若要更新我們的繫結專案，以使用 Unified Api 的最後一個步驟，我們需要修改`MakeFile`我們用來建置專案或 Xamarin 專案類型 （如果我們要繫結從 Visual studio for Mac），並指示_btouch_來針對統一的 Api，而不是傳統的繫結。


### <a name="updating-a-makefile"></a>正在更新 MakeFile

如果我們使用在 Xamarin 中建置我們的繫結專案的 makefile。DLL，我們必須包含`--new-style`命令列選項，並呼叫`btouch-native`而不是`btouch`。

因此，假設下列`MakeFile`:

```csharp
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

我們要呼叫從切換`btouch`至`btouch-native`，因此我們會調整我們巨集的定義，如下所示：

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

我們會更新以呼叫`btouch`並新增`--new-style`選項，如下所示：

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

我們現在可以執行我們`MakeFile`正常建立新的 64 位元版本的 API。

### <a name="updating-a-binding-project-type"></a>更新繫結專案類型

如果我們使用 Visual Studio for Mac 繫結的專案範本來建置 API，我們將需要更新繫結專案範本的新統一 API 版本。 若要這樣做最簡單的方式是開始新的統一 API 繫結專案和複製的所有現有的程式碼和設定。

請執行下列動作：

1. 啟動 Visual Studio for mac。
2. 選取 **檔案** > **新** > **方案...**
3. 在新的 [解決方案] 對話方塊中，選取**iOS** > **統一 API** > **iOS 繫結專案**: 

    [![](update-binding-images/image01new.png "在新的 [方案] 對話方塊中，選取 iOS / 統一的 API / iOS 繫結專案")](update-binding-images/image01new.png#lightbox)
4. 在 '設定新專案 對話方塊中輸入**名稱**新的繫結專案，然後按一下 **確定** 按鈕。
5. 包含您要建立的繫結的 Objective C 程式庫的 64 位元版本。
6. 複製來源的程式碼，從您現有的 32 位元傳統 API 繫結專案 (例如`ApiDefinition.cs`和`StructsAndEnums.cs`檔案)。
7. 對原始程式碼檔案中的上述標註的變更。

所有這些變更之後，您可以建置新的 64 位元版本的 API，像您一樣的 32 位元版本。

## <a name="summary"></a>總結

在本文中，我們已示範至現有的 Xamarin 繫結專案以支援新的統一的 Api 和 64 位元裝置所需的變更，並建立新 64 位元相容版本的 API 所需的步驟。



## <a name="related-links"></a>相關連結

- [Mac 和 iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)
- [升級現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
