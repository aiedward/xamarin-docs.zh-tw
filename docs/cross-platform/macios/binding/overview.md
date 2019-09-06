---
title: 目標-C 系結的總覽
description: 本檔概述針對目標 C 程式碼建立C#系結的不同方式，包括命令列系結、系結專案和目標 Sharpie。 它也會討論系結的運作方式。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 3b0e5d12f47ffb46ad009530bcc9c0b373496f63
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279784"
---
# <a name="overview-of-objective-c-bindings"></a>目標-C 系結的總覽

_系結程式運作方式的詳細資料_

系結與 Xamarin 搭配使用的目標-C 程式庫需要三個步驟：

1. C#撰寫「API 定義」來描述如何在 .net 中公開原生 API，以及如何將它對應至基礎目標-C。 這會使用和各種C#系結`interface` **屬性**（請參閱這個簡單的[範例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)）等標準結構來完成。

2. 在中C#撰寫「API 定義」之後，您可以將它編譯以產生「系結」元件。 這可以在[**命令列**](#commandline)上執行，或在 Visual Studio for Mac 或 Visual Studio 中使用系結[**專案**](#bindingproject)。

3. 該「系結」元件接著會新增至您的 Xamarin 應用程式專案，因此您可以使用您定義的 API 來存取原生功能。
   系結專案與您的應用程式專案完全不同。

   > [!NOTE]
   > 步驟1可以透過[**目標 Sharpie**](#objectivesharpie)的協助來自動化。 它會檢查目標-C API，並產生建議C#的「API 定義」。 您可以自訂目標 Sharpie 所建立的檔案，並在系結專案（或命令列）中使用這些檔案來建立系結元件。 目標 Sharpie 不會自行建立系結，它只是較大程式的選擇性部分。

您也可以閱讀更多有關[其運作方式](#howitworks)的技術詳細資料，這可協助您撰寫系結。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令列系結

您可以使用`btouch-native` for Xamarin. iOS （或`bmac-native`如果您使用的是 xamarin）直接建立系結。 其運作方式是將C#您手動建立的 API 定義（或使用目標 Sharpie）傳遞至命令列工具（`btouch-native`適用于 iOS 或`bmac-native` Mac）。


叫用這些工具的一般語法如下：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上述命令會`cocos2d.dll`在目前的目錄中產生檔案，而且它會包含您可在專案中使用的完整系結程式庫。 如果您使用系結專案，這就是 Visual Studio for Mac 用來建立系結的工具（[如下](#bindingproject)所述）。


<a name="bindingproject" />

## <a name="binding-project"></a>系結專案

您可以在 Visual Studio for Mac 或 Visual Studio 中建立系結專案（Visual Studio 僅支援 iOS 系結），並可讓您更輕鬆地編輯和建立系結的 API 定義（相對於使用命令列）。

請遵循此[快速入門手冊](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)，瞭解如何建立和使用系結專案來產生系結。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

目標 Sharpie 是另一個獨立的命令列工具，可協助建立系結的初始階段。 它本身並不會建立系結，而是會自動為目標原生程式庫產生 API 定義的初始步驟。

閱讀[目標 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)檔，瞭解如何將原生程式庫、原生架構和 CocoaPods 剖析為可內建至系結的 API 定義。

<a name="howitworks" />

## <a name="how-binding-works"></a>系結的運作方式

您可以使用[[Register]](xref:Foundation.RegisterAttribute)屬性、 [[Export]](xref:Foundation.ExportAttribute)屬性和[手動的目標 c 選取器調用](~/ios/internals/objective-c-selectors.md)，以手動方式系結新的（先前未系結的）目標 c 類型。

首先，尋找您想要系結的類型。 為了進行討論（和簡化），我們會系結[NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html)類型（已系結至[NSEnumerator](xref:Foundation.NSEnumerator); 以下的實作為範例）。

第二，我們需要建立C#型別。 我們可能會想要將它放在命名空間中;因為目標-c 不支援命名空間，所以我們必須使用`[Register]`屬性來變更 Xamarin iOS 將向目標-C 執行時間註冊的類型名稱。 C#型別也必須繼承自[NSObject](xref:Foundation.NSObject)：

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三，請參閱目標-C 檔，並為您想要使用的每個選取器建立[ObjCRuntime 的選擇器](xref:ObjCRuntime.Selector)實例。 將下列內容放在類別主體內：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四，您的型別必須提供函數。 您*必須*將您的函式調用鏈到基類的「函式」。 `[Export]`屬性允許目標 C 程式碼呼叫具有指定之選取器名稱的函式：

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

第五，為步驟3中所宣告的每個選取器提供方法。 這些會用`objc_msgSend()`來叫用原生物件上的選取器。 請注意， [GetNSObject （）](xref:ObjCRuntime.Runtime.GetNSObject*)的用法，會將轉換`IntPtr` `NSObject`成適當的類型（子類型）。 如果您想要從目標 C 程式碼呼叫方法，成員*必須*是**虛擬**的。

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

全部放在一起：

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```
