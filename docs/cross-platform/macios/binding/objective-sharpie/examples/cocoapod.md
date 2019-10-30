---
title: 使用 CocoaPods 的真實世界範例
description: 本檔示範如何使用目標 Sharpie，從 CocoaPod 自動產生C#系結定義。
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: cf117880eb46b028d709a44aa453e111b007b441
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016268"
---
# <a name="real-world-example-using-cocoapods"></a>使用 CocoaPods 的真實世界範例

> [!NOTE]
> 這個範例會使用[AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking)。

3\.0 版的新功能，目標 Sharpie 支援系結 CocoaPods，甚至包含命令（`sharpie pod`），讓下載、設定和建立 CocoaPods 非常簡單。 在使用這項功能之前，您應該先[熟悉 CocoaPods](https://cocoapods.org)的一般情況。

## <a name="creating-a-binding-for-a-cocoapod"></a>建立 CocoaPod 的系結

`sharpie pod` 命令有一個全域選項和兩個子命令：

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

`init` 子命令也有一些實用的協助：

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

可以提供多個 CocoaPod 名稱和 subspec 名稱給 `init`。

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

設定 CocoaPod 之後，您現在可以建立系結：

```bash
$ sharpie pod bind
```

這會導致 CocoaPod Xcode 專案建立，然後由目標 Sharpie 進行評估和剖析。 系統會產生許多主控台輸出，但會在結尾處產生系結定義：

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>後續步驟

產生**ApiDefinitions.cs**和**StructsAndEnums.cs**檔案之後，請參閱下列檔，以產生要在您的應用程式中使用的元件：

- [系結目標-C 總覽](~/cross-platform/macios/binding/overview.md)
- [系結目標-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [逐步解說：系結 iOS 目標-C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)
