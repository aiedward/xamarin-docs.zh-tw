---
title: "使用 CocoaPods 真實世界範例"
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: ae92b491e6186371f1fc1ead835f918a94f18f86
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="real-world-example-using-cocoapods"></a>使用 CocoaPods 真實世界範例


**這個範例會使用[AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking)。**

3.0 版中的新目標 Sharpie 支援繫結 CocoaPods，而且即使有前端的命令 (`sharpie pod`) 進行下載、 設定和建置 CocoaPods 非常簡單。 您應該[faimilarize 自己 CocoaPods](https://cocoapods.org)一般之前使用這項功能。

`sharpie pod`命令具有一個全域選項和兩個子命令：

```csharp
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

```csharp
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

多個 CocoaPod 名稱及 subspec 名稱可以提供給`init`。

<pre>$ <b>sharpie pod init ios AFNetworking</b>
<span class="terminal-green">**</span> Setting up CocoaPods master repo ...
   (this may take a while the first time)
<span class="terminal-green">**</span> Searching for requested CocoaPods ...
<span class="terminal-green">**</span> Working directory:
<span class="terminal-green">**</span>   - Writing Podfile ...
<span class="terminal-green">**</span>   - Installing CocoaPods ...
<span class="terminal-green">**</span>     (running `<span class="terminal-blue">pod install --no-integrate --no-repo-update</span>`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
<span class="terminal-green">**</span> 🍻 Success! You can now use other `<span class="terminal-green">sharpie pod</span>`  commands.</pre>

一旦您 CocoaPod 已設定，您現在可以建立繫結：

<pre>$ <b>sharpie pod bind</b></pre>

這將導致 CocoaPod Xcode 專案正在建置然後評估，並由目標 Sharpie 剖析。 許多的主控台輸出，將會產生，但應該導致在結尾的繫結定義：

<pre><em>(... lots of build output ...)</em>

<span class="terminal-blue">Parsing 19 header files...</span>

<span class="terminal-magenta">Binding...</span>
  <span class="terminal-magenta">[write]</span> ApiDefinitions.cs
  <span class="terminal-magenta">[write]</span> StructsAndEnums.cs

<span class="terminal-green">Done.</span></pre>

