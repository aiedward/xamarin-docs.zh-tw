---
title: "選擇器"
description: "選擇器 檢視是從資料清單中選取的文字項目控制項。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: edc724eb73b314c0accd3e8775b9b26b6eac16d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="picker"></a>選擇器

_選擇器 檢視是從資料清單中選取的文字項目控制項。_

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)顯示項目，使用者可以從中選取的簡短清單。 不過， [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)第一次出現時，不會顯示任何資料。 相反地，值及其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)屬性會顯示為 iOS 和 Android 平台上的預留位置：

[![](images/picker-initial.png "初始選擇器顯示")](images/picker-initial-large.png "初始選擇器顯示")

當[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)獲得焦點時，其資料會顯示，而且使用者可以選取項目：

[![](images/picker-selection.png "選擇器中選取項目")](images/picker-selection-large.png "選擇器中選取項目")

選取的項目會顯示所選取項目，下列[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/):

![](images/picker-after-selection.png "選取範圍之後的選擇器")

有兩種方法來填入[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)資料：

- 設定[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)要顯示之資料的屬性。 這是建議使用的技巧，在 Xamarin.Forms 2.3.4 所導入。 如需詳細資訊，請參閱[選擇器 ItemsSource 屬性設定](populating-itemssource.md)。
- 將資料加入至要顯示[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 這項技術是原始的程序，用來填入[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)與資料。 如需詳細資訊，請參閱[將資料加入至選擇器的項目集合](populating-items.md)。


## <a name="related-links"></a>相關連結

- [Picker](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
