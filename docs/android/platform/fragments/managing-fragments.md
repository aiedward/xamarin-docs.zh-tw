---
title: 管理片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027324"
---
# <a name="managing-fragments"></a>管理片段

為了説明管理片段,Android 提供了`FragmentManager`類 。 每個活動都有一個實例`Android.App.FragmentManager`,該實例將查找或動態更改其片段。 這些更改的每一組稱為*事務*,並且使用`Android.App.FragmentTransation`類 中包含的其中一個 API 執行,該`FragmentManager`API 由管理。 作用可能會啟動的事件:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

這些對片段的`FragmentTransaction`變更在實體中使用,使用這些方法(如`Add()`,`Remove(),``Replace().`然後`Commit()`使用套用) 事務中的更改不會立即執行。
相反,它們計劃儘快在活動的 UI 線程上運行。

下面的範例簡報如何向現有容器加入片段:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果在調用后`Activity.OnSaveInstanceState()`提交事務,將引發異常。 這是因為當活動保存其狀態時,Android 還會保存任何託管片段的狀態。 如果在此點之後提交任何片段事務,則還原活動時,這些事務的狀態將丟失。

可以通過呼叫 將片段事務儲存到作用[的後面堆疊](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)`FragmentTransaction.AddToBackStack()`。 這允許使用者在按下 **「後退」** 按鈕時向後導航" 片段" 如果不調用此方法,將銷毀刪除的片段,如果用戶通過活動導航回來,將不可用。

下面的範例展示如何使用`AddToBackStack``FragmentTransaction`a 的方法取代一個片段,同時保留後堆疊上第一個片段的狀態:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```

## <a name="communicating-with-fragments"></a>與片段通訊

*片段管理員*知道附加到活動的所有片段,並提供兩種方法來説明查找這些片段:

- **FindFragmentById**&ndash;此方法將使用在將片段作為事務的一部分添加時在佈局檔中指定的 ID 或容器 ID 來查找片段。

- **FindFragmentByTag**&ndash;此方法用於查找具有佈局檔中提供或在事務中添加的標記的片段。

片段和活動都引用`FragmentManager`, 因此相同的技術用於在它們之間來回通訊。 應用程式可以使用這兩種方法之一查找引用片段,強制對適當的類型進行強制轉換,然後直接調用片段上的方法。 以下代碼段提供範例:

活動也可以使用`FragmentManager`搜尋片段:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>與活動溝通

片段可以使用`Fragment.Activity`該 屬性引用其主機。 通過將活動強制轉換到更具體的類型,活動可以調用其主機上的方法和屬性,如以下示例所示:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
