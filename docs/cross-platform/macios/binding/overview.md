---
title: "總覽"
description: "繫結程序的運作方式的詳細資料"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: eb6d49433974a5e4e7bda69651508d5e9006a78e
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="overview"></a>總覽

_繫結程序的運作方式的詳細資料_

繫結搭配 Xamarin Objective C 程式庫會採用三個步驟：

1. C# 撰寫 「 應用程式開發介面定義 」 來描述如何於原生 API 公開的.NET 中，以及它如何對應到基礎目標 c。 這是使用標準 C# 建構類似`interface`和各種繫結**屬性**(請參閱此[簡單範例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API))。

2. 當您撰寫完 「 應用程式開發介面定義 」 在 C# 中，您編譯它產生的 「 繫結 」 組件。 這可以對[**命令列**](#commandline)或使用[**繫結專案**](#bindingproject) Mac 或 Visual Studio 的 Visual Studio 中。

3. 「 繫結 」 組件接著會新增至您的 Xamarin 應用程式專案，讓您能夠存取使用您所定義的 API 的原生功能。
  繫結的專案是完全不同於您的應用程式專案。

**注意：**步驟 1 可以自動化的協助[**目標 Sharpie**](#objectivesharpie)。 它會檢查 Objective C 應用程式開發介面，並產生建議 C# 「 應用程式開發介面定義 」。 您可以自訂目標 Sharpie 所建立的檔案及使用繫結專案中 （或在命令列上） 來建立組件繫結。 目標 Sharpie 不會建立本身的繫結，只是大型程序的選擇性部分。

您也可以讀取的詳細技術資訊[它的運作方式](#howitworks)，這將會幫助您撰寫您的繫結。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令列繫結

您可以使用`btouch-native`的 Xamarin.iOS (或`bmac-native`如果您使用 Xamarin.Mac) 來直接建立繫結。 其運作方式是傳遞您以手動方式建立的 C# API 定義 （或使用目標 Sharpie） 命令列工具 (`btouch-native`適用於 iOS 或`bmac-native`for Mac)。


叫用這些工具的一般語法如下：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上述命令會產生檔案`cocos2d.dll`在目前目錄中，而且不會包含完整繫結的程式庫，您可以使用您的專案中。 這是適用於 Mac 的 Visual Studio 用來建立您的繫結，如果您使用的繫結專案的工具 (描述[下方](#bindingproject))。


<a name="bindingproject" />

## <a name="binding-project"></a>繫結專案

繫結專案可以在 Visual Studio 建立的 Mac 或 Visual Studio （Visual Studio 僅支援 iOS 繫結），並可讓您更輕鬆地編輯和建置應用程式開發介面 （有別於使用命令列） 的繫結的定義。

後面要接著[入門指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)以了解如何建立及使用繫結專案來產生繫結。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>目標 Sharpie

目標 Sharpie 是另一個，不同的命令列工具，可協助進行建立繫結的初始階段。 它不會建立本身的繫結，而是會自動產生目標原生程式庫的應用程式開發介面定義的初始步驟。

讀取[目標 Sharpie 文件](~/cross-platform/macios/binding/objective-sharpie/index.md)以了解如何剖析成繫結可以建置的應用程式開發介面定義的原生程式庫、 原生的架構和 CocoaPods。

<a name="howitworks" />

## <a name="how-binding-works"></a>繫結如何運作

您可使用[[註冊]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)屬性[[匯出]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)屬性，和[手動 Objective C 選取器引動過程](~/ios/internals/objective-c-selectors.md)一起，以手動方式繫結新 （先前未繫結） Objective C 類型。

首先，尋找您想要繫結的型別。 討論用途 （以及簡單），我們將繫結[NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html)型別 (其中有已繫結中[Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); 下方的實作，例如只是用途)。

其次，我們要建立 C# 類型。 我們可能會想要將此命名空間;由於 Objective C 不支援命名空間，我們將需要使用`[Register]`屬性變更 Xamarin.iOS 會向 Objective C 執行階段的型別名稱。 C# 類型也必須繼承自[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三，檢閱 Objective C 文件，並建立[ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/)您想要使用的每個選取器的執行個體。 將這些類別主體內：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四個，您的型別必須提供建構函式。 您*必須*鏈結您建構函式引動過程的基底類別建構函式。 `[Export]`屬性允許 Objective C 程式碼呼叫的建構函式具有指定的選取器名稱：

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

第五個，提供每個選取器的方法宣告為在步驟 3 中。 這些將會使用`objc_msgSend()`叫用原生物件上的選取器。 請注意使用[Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr))轉換`IntPtr`成適當型別`NSObject`（sub） 類型。 如果您想要能夠從 Objective C 程式碼中，成員呼叫的方法*必須*是**虛擬**。

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

融會貫通：

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

