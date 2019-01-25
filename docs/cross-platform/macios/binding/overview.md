---
title: OBJECTIVE-C 繫結的概觀
description: 本文件概述不同的方式，來建立C#繫結 OBJECTIVE-C 程式碼，包括命令列的繫結、 繫結專案及目標 Sharpie。 它也會討論繫結的運作方式。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.date: 11/25/2015
ms.openlocfilehash: 3f15eaf9171ac44b870239fb5ffa14edd6210360
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879299"
---
# <a name="overview-of-objective-c-bindings"></a>OBJECTIVE-C 繫結的概觀

_繫結程序的運作方式的詳細資料_

繫結 Objective C 程式庫，以搭配 Xamarin 會採用三個步驟：

1. 寫入C#[API 定義] 來描述原生 API 公開方式在.NET 中，以及它如何對應到基礎的目標 c。 這是使用標準C#結構，例如`interface`和各種繫結**屬性**(請參閱此[簡單範例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API))。

2. 一旦您已撰寫的 [API 定義] C#，您編譯它來產生 「 繫結 」 組件。 這可以在[**命令列**](#commandline) ，或使用[**繫結專案**](#bindingproject) Visual Studio for Mac 或 Visual Studio 中。

3. 「 繫結 」 該組然後會新增至您的 Xamarin 應用程式專案，因此您可以使用您所定義的 API 的原生功能。
  繫結的專案是從您的應用程式的專案完全不同。

**注意：** 步驟 1 可以自動化的協助[**目標 Sharpie**](#objectivesharpie)。 它會檢查 Objective C API，並產生建議C#[API 定義]。 您可以自訂目標 Sharpie 所建立的檔案，並在繫結專案 （或在命令列上），請使用它們來建立您的繫結組件。 目標 Sharpie 不會建立單獨的繫結，它只是大型程序的選擇性部分。

您也可以閱讀更多技術細節[運作方式](#howitworks)，這可協助您撰寫您的繫結。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令列繫結

您可以使用`btouch-native`適用於 Xamarin.iOS (或`bmac-native`如果您使用 Xamarin.Mac) 直接建立的繫結。 其運作方式是傳遞C#API 定義，您以手動方式建立 （或使用目標 Sharpie） 給命令列工具 (`btouch-native`適用於 iOS 或`bmac-native`for Mac)。


叫用這些工具的一般語法是：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上述命令會產生檔案`cocos2d.dll`在目前的目錄中，而且它會包含在專案中，您可以使用完全繫結程式庫。 這是 Visual Studio for Mac 會使用來建立您的繫結，如果您使用的繫結專案的工具 (所述[以下](#bindingproject))。


<a name="bindingproject" />

## <a name="binding-project"></a>繫結專案

繫結專案可以在 Visual Studio for Mac 或 Visual Studio （Visual Studio 只支援 iOS 繫結） 建立，並輕鬆編輯和建置的 （相對於使用命令列） 的繫結的 API 定義。

請遵循這[快速入門指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)以了解如何建立及使用繫結專案來產生繫結。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

目標 Sharpie 是另一個、 個別的命令列工具，可協助建立繫結的初始階段進行。 它不會建立繫結本身，而是會自動產生 API 定義目標的原生程式庫的初始步驟。

讀取[目標 Sharpie docs](~/cross-platform/macios/binding/objective-sharpie/index.md)以了解如何將原生程式庫、 原生的架構和 CocoaPods 剖析為可以內建繫結的 API 定義。

<a name="howitworks" />

## <a name="how-binding-works"></a>繫結的運作方式

您可使用[[註冊]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)屬性， [[匯出]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)屬性，並[手動 OBJECTIVE-C 選取器引動過程](~/ios/internals/objective-c-selectors.md)在一起，以手動方式繫結新 （先前未繫結） 的 Objective C 類型。

首先，尋找您想要繫結的型別。 討論用途 （以及簡單），我們將繫結[NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html)型別 (其中具有已繫結中[Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); 下方的實作，例如只是用途)。

其次，我們需要建立C#型別。 我們可能會想要將此資訊放置的命名空間Objective C 不支援命名空間，所以我們需要使用`[Register]`屬性來變更 Xamarin.iOS 會向 OBJECTIVE-C 執行階段的型別名稱。 C#型別也必須繼承自[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三，檢閱 OBJECTIVE-C 文件，並建立[ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/)您想要使用每個選取器的執行個體。 放在類別主體中：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四，您的類型必須提供建構函式。 您*必須*鏈結您建構函式引動過程的基底類別建構函式。 `[Export]`屬性允許呼叫的建構函式使用指定的選取器名稱的 OBJECTIVE-C 程式碼：

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

第五，提供每個選取器的方法宣告在步驟 3 中。 它們會使用`objc_msgSend()`叫用的原生物件上的選取器。 請注意，使用[Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr))轉換`IntPtr`成適當型別`NSObject`（sub） 類型。 如果您想要能夠從 OBJECTIVE-C 程式碼，該成員呼叫的方法*必須*被**虛擬**。

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

總結：

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

## <a name="related-links"></a>相關連結

- [Xamarin University 課程：建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程：建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)