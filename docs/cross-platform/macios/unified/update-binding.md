---
title: 將繫結移轉至 Unified API
description: 本文涵蓋更新現有 Xamarin 系結專案所需的步驟，以支援 Xamarin. IOS 和 Xamarin 應用程式的整合 Api。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: c8f55dd2d300da80a57c06f15cf185558cfc5e41
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015049"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>將繫結移轉至 Unified API

_本文涵蓋更新現有 Xamarin 系結專案所需的步驟，以支援 Xamarin. IOS 和 Xamarin 應用程式的整合 Api。_

## <a name="overview"></a>總覽

從2015年2月1日開始，對 iTunes 和 Mac App Store 的所有新提交都必須是64位應用程式。 因此，任何新的 Xamarin 或 Xamarin 應用程式都必須使用新的 Unified API，而不是現有的傳統 MonoTouch 和 MonoMac Api，以支援64位。

此外，任何 Xamarin 系結專案也必須支援要包含在64位的 Xamarin. iOS 或 Xamarin 專案中的新整合 Api。 本文將涵蓋更新現有系結專案以使用 Unified API 所需的步驟。

## <a name="requirements"></a>需求

需要下列專案，才能完成本文中顯示的步驟：

- **Visual Studio for Mac** -在開發電腦上安裝和設定的最新版本 Visual Studio for Mac。
- **Apple mac** -建立適用于 IOS 和 Mac 的系結專案需要 apple mac。

Windows 電腦上的 Visual studio 不支援系結專案。

## <a name="modify-the-using-statements"></a>修改 Using 語句

統一的 Api 可讓您比以往更輕鬆地在 Mac 和 iOS 之間共用程式碼，並可讓您使用相同的二進位檔來支援32和64位應用程式。 藉由卸載命名空間中的_MonoMac_和_MonoTouch_前置詞，可在 Xamarin. Mac 和 xamarin iOS 應用程式專案中進行更簡單的共用。

如此一來，我們將需要修改系結合約（以及系結專案中的其他 `.cs` 檔案），以從我們的 `using` 語句中移除_MonoMac_和_MonoTouch_首碼。

例如，假設在系結合約中有下列 using 語句：

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我們會去除 `MonoTouch` 前置詞，產生下列結果：

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同樣地，我們必須針對系結專案中的任何 `.cs` 檔案執行此動作。 進行這項變更後，下一個步驟是更新系結專案，以使用新的原生資料類型。

如需 Unified API 的詳細資訊，請參閱[Unified API](~/cross-platform/macios/unified/index.md)檔。 如需支援32和64位應用程式的背景，以及架構的相關資訊，請參閱[32 和64位平臺考慮](~/cross-platform/macios/32-and-64/index.md)檔。

## <a name="update-to-native-data-types"></a>原生資料類型的更新

Objective-C maps the `NSInteger` data type to `int32_t` on 32 bit systems and to `int64_t` on 64 bit systems. To match this behavior, the new Unified API replaces the previous uses of `int` (which in .NET is defined as always being `System.Int32`) to a new data type: `System.nint`.

Along with the new `nint` data type, the Unified API introduces the `nuint` and `nfloat` types, for mapping to the `NSUInteger` and `CGFloat` types as well.

Given the above, we need to review our API and ensure that any instance of `NSInteger`, `NSUInteger` and `CGFloat` that we previously mapped to `int`, `uint` and `float` get updated to the new `nint`, `nuint` and `nfloat` types.

For example, given an Objective-C method definition of:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

If the previous binding contract had the following definition:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

We would update the new binding to be:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

If we are mapping to a newer version 3rd party library than what we had initially linked to, we need to review the `.h` header files for the library and see if any exiting, explicit calls to `int`, `int32_t`, `unsigned int`, `uint32_t` or `float` have been upgraded to be an `NSInteger`, `NSUInteger` or a `CGFloat`. If so, the same modifications to the `nint`, `nuint` and `nfloat` types will need to be made to their mappings as well.

To learn more about these data type changes, see the [Native Types](~/cross-platform/macios/nativetypes.md) document.

## <a name="update-the-coregraphics-types"></a>Update the CoreGraphics Types

The point, size and rectangle data types that are used with `CoreGraphics` use 32 or 64 bits depending on the device they are running on. When Xamarin originally bound the iOS and Mac APIs we used existing data structures that happened to match the data types in `System.Drawing` (`RectangleF` for example).

Because of the requirements to support 64 bits and the new native data types, the following adjustments will need to be made to existing code when calling `CoreGraphic` methods:

- **CGRect** - Use `CGRect` instead of `RectangleF` when defining floating point rectangular regions.
- **CGSize** - Use `CGSize` instead of `SizeF` when defining floating point sizes (width and height).
- **CGPoint** - Use `CGPoint` instead of `PointF` when defining a floating point location (X and Y coordinates).

Given the above, we will need to review our API and ensure that any instance of `CGRect`, `CGSize` or `CGPoint` that was previously bound to `RectangleF`, `SizeF` or `PointF` be changed to the native type `CGRect`, `CGSize` or `CGPoint` directly.

For example, given an Objective-C initializer of:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

If our previous binding included the following code:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

We would update that code to:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

With all of the code changes now in place, we need to modify our binding project or make file to bind against the Unified APIs.

## <a name="modify-the-binding-project"></a>Modify the Binding Project

As the final step to updating our binding project to use the Unified APIs, we need to either modify the `MakeFile` that we use to build the project or the Xamarin Project Type (if we are binding from within Visual Studio for Mac) and instruct _btouch_ to bind against the Unified APIs instead of the Classic ones.

### <a name="updating-a-makefile"></a>Updating a MakeFile

If we are using a makefile to build our binding project into a Xamarin .DLL, we will need to include the `--new-style` command line option and call `btouch-native` instead of `btouch`.

So given the following `MakeFile`:

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

We need to switch from calling `btouch` to `btouch-native`, so we would adjust our macro definition as follows:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

We would update the call to `btouch` and add the `--new-style` option as follows:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

We can now execute our `MakeFile` as normal to build the new 64 bit version of our API.

### <a name="updating-a-binding-project-type"></a>Updating a Binding Project Type

If we are using a Visual Studio for Mac Binding Project Template to build our API, we'll need to update to the new Unified API version of the Binding Project Template. The easiest way to do this is to start a new Unified API Binding Project and copy over all of the existing code and settings.

請執行下列動作：

1. Start Visual Studio for Mac.
2. Select **File** > **New** > **Solution...**
3. In the New Solution Dialog Box, select **iOS** > **Unified API** > **iOS Binding Project**: 

    [![](update-binding-images/image01new.png "In the New Solution Dialog Box, select iOS / Unified API / iOS Binding Project")](update-binding-images/image01new.png#lightbox)
4. On 'Configure your new project' dialog enter a **Name** for the new binding project and click the **OK** button.
5. Include the 64 bit version of Objective-C library that you are going to be creating bindings for.
6. Copy over the source code from your existing 32 bit Classic API binding project (such as the `ApiDefinition.cs` and `StructsAndEnums.cs` files).
7. Make the above noted changes to the source code files.

With all of these changes in place, you can build the new 64 bit version of the API as you would the 32 bit version.

## <a name="summary"></a>總結

In this article we have shown the changes that need to be made to an existing Xamarin Binding Project to support the new Unified APIs and 64 bit devices and the steps required to build the new 64 bit compatible version of an API.

## <a name="related-links"></a>相關連結

- [Mac and iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 bit Platform Considerations](~/cross-platform/macios/32-and-64/index.md)
- [Upgrading Existing iOS Apps](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
