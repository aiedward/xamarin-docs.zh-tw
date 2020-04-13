---
title: 目標C繫結概述
description: 本文件概述了為 Objective-C 代碼建立 C# 綁定的不同方法,包括命令行綁定、綁定專案和目標夏普。 它還討論了綁定的工作原理。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: be2f7f555b76d472f7a66d95e661bb2f5884c58f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292179"
---
# <a name="overview-of-objective-c-bindings"></a>目標C繫結概述

_繫結過程工作原理的詳細資訊_

繫結目標 C 函式庫以與 Xamarin 一起使用需要三個步驟:

1. 編寫 C#"API 定義"來描述本機 API 在 .NET 中如何公開,以及它如何映射到基礎目標 C。 這是使用標準 C# 建構`interface`(如和各種綁定**屬性**)完成的(請參閱此[簡單範例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API))。

2. 在 C# 中編寫「API 定義」後,將編譯它以生成「綁定」程式集。 這可以在[**指令列**](#commandline)上完成,也可以在 Mac 或 Visual Studio 的 Visual Studio 中使用[**繫結此專案**](#bindingproject)。

3. 然後,該「綁定」程式集將添加到 Xamarin 應用程式專案中,因此您可以使用定義的 API 存取本機功能。
   綁定專案與應用程式專案完全分開。

   > [!NOTE]
   > 步驟1可以在[**目標夏普**](#objectivesharpie)的協助下實現自動化。 它檢查目標 C API 並生成建議的 C# "API 定義」。 您可以自定義 Objective Sharpie 創建的檔案,並在綁定專案(或命令列上)使用它們來建立綁定程式集。 目標夏菲本身不會創建綁定,它只是較大過程的可選部分。

您還可以閱讀更多技術細節,[說明其工作原理](#howitworks),這將有助於您編寫綁定。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令列繫結

您可以使用`btouch-native`for Xamarin.iOS(或者`bmac-native`如果您使用的 Xamarin.Mac)直接建構結合。 它的工作原理是將您手動創建的 C# API 定義(或使用目標夏普)傳遞給命令`btouch-native`列工具(`bmac-native`對於 iOS 或 Mac)。

呼叫這些工具的一般語法是:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上述命令將生成當前目錄中的檔`cocos2d.dll`,它將包含可用於專案的完全綁定庫。 如果您使用繫結項目([如下](#bindingproject)所述),則 Mac Visual Studio 用於建立綁定的工具。

<a name="bindingproject" />

## <a name="binding-project"></a>繫結項目

可以在 Visual Studio 中為 Mac 或 Visual Studio 創建綁定專案(Visual Studio 僅支援 iOS 綁定),並更輕鬆地編輯和建置用於綁定的 API 定義(與使用命令列相比)。

請按照此[入門指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)瞭解如何創建和使用綁定專案來生成綁定。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

目標夏比是另一個單獨的命令列工具,可幫助實現創建綁定的初始階段。 它不自行創建綁定,而是自動執行為目標本機庫生成 API 定義的初始步驟。

閱讀[目標夏普文檔](~/cross-platform/macios/binding/objective-sharpie/index.md),瞭解如何將本機庫、本機框架和 CocoaPods 解析為可內置於綁定中的 API 定義。

<a name="howitworks" />

## <a name="how-binding-works"></a>繫結工作原理

可以使用[[註冊]](xref:Foundation.RegisterAttribute)屬性[、[匯出]](xref:Foundation.ExportAttribute)屬性和[手動 Objective-C 選擇器調用](~/ios/internals/objective-c-selectors.md)一起手動綁定新的(以前未綁定的)目標 C 類型。

首先,找到要綁定的類型。 為了討論的目的(和簡單),我們將綁定[NSENumerator](https://developer.apple.com/documentation/foundation/nsenumerator)類型(已在[Foundation.NSENumerator](xref:Foundation.NSEnumerator)中綁定;下面的實現只是例如)。

其次,我們需要創建 C# 類型。 我們可能想要將它放入命名空間;由於 Objective-C 不支援命名空間,我們`[Register]`需要使用 該屬性來更改 Xamarin.iOS 將在 Objective-C 執行時註冊的類型名稱。 C# 類型還必須從[Foundation.NSObject](xref:Foundation.NSObject)繼承 :

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三,查看 Objective-C 文件並創建[ObjCRuntime.](xref:ObjCRuntime.Selector)選擇要使用的每個選擇器的選擇器實例。 將這些放在類別中:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四,您的類型將需要提供構造函數。 *必須*將建構函數調用連結到基類構造函數。 屬性`[Export]`允許 Objective-C 程式碼使用指定的選擇器名稱呼叫式建構函數:

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

第五,為步驟 3 中聲明的每個選擇器提供方法。 這些將用於`objc_msgSend()`調用本機物件上的選擇器。 請注意使用[運行時.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*)將`IntPtr`轉換為`NSObject`適當鍵入的(子)類型。 如果希望該方法可以從 Objective-C 代碼呼叫,*則成員必須是***虛擬**的 。

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

全部放在一起:

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
