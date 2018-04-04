---
title: 使用 CocoaPods 真實世界範例
description: 本文件將示範如何使用自動產生的 C# 繫結定義從 CocoaPod 目標 Sharpie。
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: cbcafc8d77304d117f8130cf0d6a89dd2a5ed3c8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="real-world-example-using-cocoapods"></a>使用 CocoaPods 真實世界範例

> [!NOTE]
> 這個範例會使用[AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking)。

3.0 版中的新目標 Sharpie 支援繫結 CocoaPods，而且甚至還包含命令 (`sharpie pod`) 進行下載、 設定和建置 CocoaPods 非常簡單。 您應該[熟悉 CocoaPods](https://cocoapods.org)一般之前使用這項功能。

## <a name="creating-a-binding-for-a-cocoapod"></a>建立繫結的 CocoaPod

`sharpie pod`命令具有一個全域選項和兩個子命令：

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

`init`子命令也會有一些有用的說明：

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

多個 CocoaPod 名稱及 subspec 名稱可以提供給`init`。

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

一旦您 CocoaPod 已設定，您現在可以建立繫結：

```bash
$ sharpie pod bind
```

這將導致 CocoaPod Xcode 專案正在建置然後評估，並由目標 Sharpie 剖析。 許多的主控台輸出，將會產生，但應該導致在結尾的繫結定義：

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>後續步驟

在產生之後**ApiDefinitions.cs**和**StructsAndEnums.cs**檔案，看看下列的文件，產生要在您的應用程式中使用組件：

- [繫結 OBJECTIVE-C 概觀](~/cross-platform/macios/binding/overview.md)
- [繫結 Objective C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [逐步解說： 繫結 iOS Objective C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)

