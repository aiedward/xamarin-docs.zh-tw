---
title: 將繫結移轉至 Unified API
description: 本文涵蓋更新現有 Xamarin 系結專案的必要步驟，以支援適用于 Xamarin 和 Xamarin 應用程式的整合 Api。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: d545daa10f9f771cd1708adf32569a49f6c4e709
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453502"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>將繫結移轉至 Unified API

_本文涵蓋更新現有 Xamarin 系結專案的必要步驟，以支援適用于 Xamarin 和 Xamarin 應用程式的整合 Api。_

## <a name="overview"></a>概觀

自2015年2月1日起，Apple 要求 iTunes 和 Mac App Store 的所有新提交都必須是64位應用程式。 因此，任何新的 Xamarin 或 Xamarin. Mac 應用程式都必須使用新的 Unified API，而不是現有的傳統 Monotouch.dll 和 MonoMac Api 來支援64位。

此外，任何 Xamarin 系結專案也都必須支援新的整合 Api，以包含在64位的 Xamarin 或 Xamarin. Mac 專案中。 本文將討論更新現有的系結專案以使用 Unified API 所需的步驟。

## <a name="requirements"></a>需求

若要完成本文中所述的步驟，必須具備下列專案：

- **Visual Studio for Mac** -在開發電腦上安裝和設定的最新版本 Visual Studio for Mac。
- **Apple mac** -必須有 apple mac 才能建立 IOS 和 Mac 的系結專案。

Windows 電腦上的 Visual studio 不支援系結專案。

## <a name="modify-the-using-statements"></a>修改 Using 語句

統一的 Api 可讓您比以往更輕鬆地在 Mac 與 iOS 之間共用程式碼，並可讓您以相同的二進位檔支援32和64位應用程式。 藉由從命名空間中卸載 _MonoMac_ 和 _monotouch.dll_ 前置詞，可在 Xamarin 和 xamarin. iOS 應用程式專案之間達成更簡單的共用。

因此，我們必須修改系結程式專案中的任何系結合約 (和其他檔案 `.cs`) ，以從我們的語句中移除 _MonoMac_ 和 _monotouch.dll_ 首碼 `using` 。

例如，使用系結合約中的下列語句：

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我們會移除前置詞， `MonoTouch` 以產生下列結果：

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同樣地，我們也需要針對系結專案中的任何檔案進行這 `.cs` 項作業。 進行這項變更後，下一個步驟就是更新我們的系結專案，以使用新的原生資料類型。

如需 Unified API 的詳細資訊，請參閱 [Unified API](~/cross-platform/macios/unified/index.md) 檔。 如需支援32和64位應用程式的背景，以及有關架構的詳細資訊，請參閱 [32 和64位平臺考慮](~/cross-platform/macios/32-and-64/index.md) 檔。

## <a name="update-to-native-data-types"></a>原生資料類型的更新

目標-C 會將 `NSInteger` 資料類型對應至 `int32_t` 32 位系統和 `int64_t` 64 位系統上的。 為了符合這種行為，新的 Unified API 取代了先前的 (的使用方式， `int` 在 .net 中定義為永遠 `System.Int32`) 新的資料型別： `System.nint` 。

除了新的 `nint` 資料類型之外，Unified API 也引進了 `nuint` 和 `nfloat` 類型，以便對應至 `NSUInteger` 和 `CGFloat` 類型。

在上述情況下，我們需要檢查我們的 API，並確定所有的實例 `NSInteger` ，以及我們先前對應到的實例，並將其 `NSUInteger` `CGFloat` `int` `uint` `float` 更新為新的 `nint` `nuint` 和 `nfloat` 類型。

例如，假設有一個目標 C 方法定義：

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

如果先前的系結合約具有下列定義：

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

我們會將新的系結更新為：

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

如果我們要對應的是較新版本的協力廠商程式庫，則必須檢查連結 `.h` 庫的標頭檔，並查看是否有任何已將、、或的明確呼叫 `int` `int32_t` `unsigned int` `uint32_t` `float` 升級為 `NSInteger` ， `NSUInteger` 或 `CGFloat` 。 如果是的話，也必須對 `nint` `nuint` 和類型進行相同的修改 `nfloat` 。

若要深入瞭解這些資料類型的變更，請參閱 [原生類型](~/cross-platform/macios/nativetypes.md) 檔。

## <a name="update-the-coregraphics-types"></a>更新 CoreGraphics 類型

使用32或64位的 point、size 和 rectangle 資料類型， `CoreGraphics` 取決於其執行所在的裝置。 當 Xamarin 最初系結 iOS 和 Mac Api 時，我們使用現有的資料結構來符合 (中的資料類型， `System.Drawing` `RectangleF` 例如) 。

由於需要支援64位和新的原生資料類型，因此在呼叫方法時，必須對現有的程式碼進行下列調整 `CoreGraphic` ：

- **Rectanglef** - `CGRect` `RectangleF` 在定義浮點數矩形區域時使用，而不是。
- **CGSize** - `CGSize` `SizeF` 在定義浮點數大小時 (寬度和高度) 時使用，而不是。
- **CGPoint** - `CGPoint` `PointF` 在定義浮點數位置時使用，而不是)  (X 和 Y 座標。

在上述情況下，我們將需要檢查我們的 API，並確認任何 `CGRect` `CGSize` 或先前已系結至原生類型或的實例，或是 `CGPoint` `RectangleF` `SizeF` `PointF` 直接變更為原生類型 `CGRect` `CGSize` `CGPoint` 。

例如，假設有一個目標 C 初始化運算式：

```csharp
- (id)initWithFrame:(CGRect)frame;
```

如果先前的系結包含下列程式碼：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

我們會將該程式碼更新為：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

完成所有程式碼變更之後，我們必須修改系結專案或建立檔案，以針對統一的 Api 進行系結。

## <a name="modify-the-binding-project"></a>修改系結專案

在更新系結專案以使用統一 Api 的最後一個步驟中，我們需要修改 `MakeFile` 用來建立專案的或 Xamarin 專案類型 (如果我們是在 Visual Studio for Mac) 內進行系結，並指示 _b c h_ 系結至統一的 api，而不是傳統的 api。

### <a name="updating-a-makefile"></a>更新 MakeFile

如果我們使用 makefile 將我們的系結專案建立到 Xamarin 中。DLL，我們必須包含 `--new-style` 命令列選項和呼叫， `btouch-native` 而不是 `btouch` 。

請提供下列各項 `MakeFile` ：

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

我們需要從呼叫切換 `btouch` 為 `btouch-native` ，因此我們會調整巨集定義的定義，如下所示：

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

我們會更新對的呼叫 `btouch` ，並新增選項，如下所示 `--new-style` ：

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

我們現在可以正常執行 `MakeFile` ，以建立新的64位版本的 API。

### <a name="updating-a-binding-project-type"></a>更新系結專案類型

如果我們使用 Visual Studio for Mac 系結專案範本來建立 API，我們必須更新為系結專案範本的新 Unified API 版本。 若要這麼做，最簡單的方式是啟動新的 Unified API 系結專案，並複製所有現有的程式碼和設定。

執行下列動作：

1. 開始 Visual Studio for Mac。
2. 選取 **[** 檔案  >  **新增**  >  **方案 ...** ]
3. 在 [新增方案] 對話方塊中，選取 [ **ios**  >  **Unified API**  >  **ios**系結專案： 

    [![在 [新增方案] 對話方塊中，選取 [iOS/Unified API/iOS 系結專案]](update-binding-images/image01new.png)](update-binding-images/image01new.png#lightbox)
4. 在 [設定您的新專案] 對話方塊中，輸入新系結專案的 **名稱** ，然後按一下 [ **確定]** 按鈕。
5. 包含要為其建立系結之目標-C 程式庫的64位版本。
6. 從現有的32位 Classic API 系結專案 (（例如和檔案) ）複製 `ApiDefinition.cs` 原始程式碼 `StructsAndEnums.cs` 。
7. 對原始程式碼檔進行上述的變更。

這些變更都備妥之後，您就可以建立新的64位版本的 API，就像是32位版本一樣。

## <a name="summary"></a>摘要

在本文中，我們示範了需要對現有的 Xamarin 系結專案進行的變更，以支援新的統一 Api 和64位裝置，以及建立新的64位相容版本 API 所需的步驟。

## <a name="related-links"></a>相關連結

- [Mac 和 iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md)
- [升級現有的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](/samples/xamarin/ios-samples/bindingsample/)