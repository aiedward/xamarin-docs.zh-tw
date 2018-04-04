---
title: 如何啟用 Android.axml 檔案中的 Intellisense
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 8278576355299894eab1c7c6d3ceaadb607fe915
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-enable-intellisense-in-android-axml-files"></a>如何啟用 Android.axml 檔案中的 Intellisense

在 Visual Studio 中的 android Intellisense 需要兩個 XML 結構描述檔案，以適當地運作。 若要尋找這些檔案，請移至這個資料夾：

`C:\Program Files (x86)\Xamarin Studio\AddIns\MonoDevelop.MonoDroid\schemas`*

* (先前： `C:\Program Files (x86)\MSBuild\Xamarin\Android`)

內部，您會發現兩個.xsd 檔案：

1. android-layout-xml.xsd
2. schemas.android.com.apk.res.android.xsd

Visual Studio 會將所有的 XML 結構描述內的個別資料夾：

`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Xml\Schemas`

您可以選擇將這兩個的.xsd 檔案移至上述位置，或只是只要加入 Visual Studio 中的這些結構描述。 然後您可以 「 加入 」 透過 Visual Studio 內的這些結構描述**XML > 結構描述 > 新增**對話方塊。






