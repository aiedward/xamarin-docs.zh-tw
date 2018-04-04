---
title: 管理的片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: b39067b0cb1bbb344866761042db0125fd4a4dc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="managing-fragments"></a>管理的片段

為了協助管理片段，提供 Android`FragmentManager`類別。 每個活動都有的執行個體`Android.App.FragmentManager`，會尋找或動態地變更其片段。 這些變更的每一組稱為*交易*，並使用其中一種類別中包含的 Api 執行`Android.App.FragmentTransation`，這由管理`FragmentManager`。 活動可能會啟動交易像這樣：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

會執行這些變更的片段`FragmentTransaction`執行個體所使用的方法，例如`Add()`，`Remove(),`和`Replace().`所做的變更會套用使用`Commit()`。 在交易中的變更不會立即執行。
相反地，排定儘速活動的 UI 執行緒上執行。

下列範例會示範如何將片段加入至現有容器：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果交易被認可之後`Activity.OnSaveInstanceState()`是呼叫，將會擲回例外狀況。 這是因為當活動儲存其狀態，Android 也會儲存任何託管片段的狀態。 如果任何片段交易認可之後，還原活動時，將會遺失這些交易的狀態。

可儲存活動的片段交易[上一頁堆疊](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)藉由呼叫`FragmentTransaction.AddToBackStack()`。 這可讓使用者透過向後巡覽片段時變更**回**按下按鍵時。 而呼叫這個方法時，不會移除的片段會將被終結，而將無法使用，如果使用者導覽回到程式活動。

下列範例示範如何使用`AddToBackStack`方法`FragmentTransaction`來取代一個片段中，同時保留上一頁堆疊上的第一個片段的狀態：

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

*FragmentManager*知道所有附加至活動的片段，並提供兩種方法可以協助您尋找這些片段：

-   **FindFragmentById** &ndash;這個方法會使用配置檔中指定的識別碼或容器識別碼，此片段加入做為交易的一部分時發現片段。

-   **FindFragmentByTag** &ndash;這個方法用來尋找有的標記提供配置檔案中或已加入在交易中的片段。

片段和活動參考`FragmentManager`，因此相同的技術用來兩者之間來回傳送。 應用程式可能會利用這兩種方法的其中一種尋找片段的參考、 轉型為適當的型別，該參考，然後直接片段上呼叫方法。 下列程式碼片段提供一個範例：

也可能會使用活動`FragmentManager`來尋找程式碼片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>活動與通訊

若要使用的片段可能會`Fragment.Activity`屬性來參考它的主機。 透過將轉型為更特定類型的活動，可能會用來呼叫方法和屬性，其主機上的活動中的下列範例所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
