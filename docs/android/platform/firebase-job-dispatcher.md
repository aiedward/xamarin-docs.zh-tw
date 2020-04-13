---
title: Firebase 工作發送器
description: 本指南討論如何使用 Google 的 Firebase 作業調度器庫安排後台工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020238"
---
# <a name="firebase-job-dispatcher"></a>Firebase 工作發送器

_本指南討論如何使用 Google 的 Firebase 作業調度器庫安排後台工作。_

## <a name="overview"></a>概觀

使 Android 應用程式對使用者回應的最佳方式之一是確保在後台執行複雜或長時間運行的工作。 但是,後台工作不會對使用者使用設備的體驗產生負面影響,這一點很重要。 

例如,後台作業可能每隔三或四分鐘輪詢一個網站,以查詢對特定數據集的更改。 這似乎是良性的,但它會對電池壽命產生災難性的影響。 應用程式將反覆喚醒設備,將 CPU 提升到更高的電源狀態,打開收音機電源,發出網路請求,然後處理結果。 情況變得更糟,因為設備不會立即斷電並返回到低功耗空閒狀態。 計劃不當的背景工作可能會無意中使設備處於不必要和過多的電源要求的狀態。 這種看似無辜的活動(輪詢網站)將使設備在相對較短的時間內無法使用。

Android 提供以下 API 來説明在後台執行工作,但僅此本身,它們不足以進行智慧作業調度。 

- **[意向服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**&ndash;意向服務非常適合執行工作,但它們無法提供安排工作的方法。
- **[警報管理員](https://developer.android.com/reference/android/app/AlarmManager.html)**&ndash;這些 API 只允許計畫工作,但無法實際執行該工作。 此外,警報管理器只允許基於時間的約束,這意味著在一定時間或經過一段時間后發出警報。 
- **[作業計劃](https://developer.android.com/reference/android/app/job/JobScheduler.html)**&ndash;作業計畫是一個偉大的 API,它與作業系統一起安排作業。 但是,它僅適用於那些針對 API 級別 21 或更高的 Android 應用。 
- **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)**&ndash;Android 應用程式可以設定廣播接收器以執行回應系統範圍的事件或意圖的工作。 但是,廣播接收器不提供對作業何時運行的任何控制。 此外,Android 作業系統中的更改將限制廣播接收器何時工作,或限制它們可以回應的工作類型。 

高效執行後台工作有兩個關鍵功能(有時稱為_後台作業_或_作業_):

1. **明智地安排工作**&ndash;當應用程式在後台工作時,重要的是它作為一個好公民這樣做。 理想情況下,應用程式不應要求運行作業。 相反,應用程式應指定作業何時可以運行時必須滿足的條件,然後安排滿足條件時運行該工作條件。 這允許 Android 智慧地執行工作。 例如,可以批處理網路請求以同時運行所有請求,以最大限度地利用網路所涉及的開銷。
2. **封裝工作**&ndash;執行後台工作的代碼應封裝在獨立元件中,該元件可以獨立於使用者介面運行,如果工作由於某種原因無法完成,則相對容易重新計劃。

Firebase 作業調度員是 Google 提供的一個庫,提供流暢的 API 以簡化後台工作的安排。 它旨在取代 Google 雲管理器。 Firebase 工作排程程式由以下 API 組成:

- a`Firebase.JobDispatcher.JobService`是一個抽象類,必須使用將在後台作業中運行的邏輯進行擴展。
- 聲明`Firebase.JobDispatcher.JobTrigger`何時應啟動作業。 這通常表示為時間視窗,例如,在開始作業之前至少等待 30 秒,但在 5 分鐘內運行作業。
- 包含`Firebase.JobDispatcher.RetryStrategy`有關作業未能正確執行時應執行的操作的資訊。 重試策略指定在嘗試再次運行作業之前要等待多長時間。 
- A`Firebase.JobDispatcher.Constraint`是一個可選值,用於描述在作業可以運行之前必須滿足的條件,例如設備位於未計量的網路上或充電。
- `Firebase.JobDispatcher.Job`是 API,用於將以前的 API 統一到 可以由排程排`JobDispatcher`程的工作單元 。 類別`Job.Builder`用於實體化`Job`。
- A`Firebase.JobDispatcher.JobDispatcher`使用前三個 API 來安排與作業系統一起工作,並在必要時提供取消作業的方法。

要安排與 Firebase 作業調度程式一起工作,Xamarin.Android 應用程式必須將代碼封`JobService`裝在擴展 類的類型中。 `JobService`有三種生命週期方法,可以在作業的生存期內調用:

- **`bool OnStartJob(IJobParameters parameters)`**&ndash;此方法是工作將發生的地方,應始終實現。 它在主線程上運行。 如果剩餘工作或`true``false`已完成工作,此方法將返回。 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash;當作業由於某種原因停止時,將調用此選項。 如果作業應`true`重新計劃為以後,則應返回。
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash;當 完成任何異`JobService`步 工作時,將調用此方法。 

要安排工作,應用程式將實體化物件`JobDispatcher`。 然後,`Job.Builder`使用`Job`創建物件 ,`JobDispatcher`物件提供給 將 嘗試並安排作業運行的物件。

本指南將討論如何將 Firebase 作業調度程式添加到 Xamarin.Android 應用程式,並用它來安排後台工作。

## <a name="requirements"></a>需求

Firebase 作業調度程式需要 Android API 級別 9 或更高版本。 Firebase 作業調度員庫依賴於 Google Play 服務提供的某些元件;設備必須安裝 Google 播放服務。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>使用 Xamarin.安卓中的火基作業調度器庫

要開始使用 Firebase 作業調度程式,首先將[Xamarin.Firebase.JobDispatcher NuGet 包](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)添加到 Xamarin.Android 專案中。 在 NuGet 包管理器中搜索**Xamarin.Firebase.JobDispatcher**包(該包仍處於預發佈中)。

添加 Firebase 作業調度程式庫後`JobService`,創建一個類,然後計劃它`FirebaseJobDispatcher`使用 的實例運行。

### <a name="creating-a-jobservice"></a>建立工作服務

Firebase 作業調度程式庫執行的所有工作都必須以`Firebase.JobDispatcher.JobService`擴展 抽象類的類型完成。 建立`JobService`與使用 Android`Service`架構 建立非常相似: 

1. 擴充`JobService`類
2. 用`ServiceAttribute`來 裝飾子類。 雖然不是嚴格要求的,但建議顯式設定`Name`參數以幫助除錯`JobService`。 
3. 新增`IntentFilter`以`JobService`宣告**AndroidManifest.xml**中的 。 這會協助 Firebase 工作排程程式庫尋找與呼`JobService`叫 。

以下代碼是應用程式最簡單的`JobService`範例,使用 TPL 同步執行某些工作:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>建立火基工作排程器

在計劃任何工作之前,必須創建一個`Firebase.JobDispatcher.FirebaseJobDispatcher`物件。 `FirebaseJobDispatcher`負責排程`JobService`。 以下代碼段是建立`FirebaseJobDispatcher`的實體的一種方式: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在前面的代碼段中,`GooglePlayDriver`是類,可幫助與設備`FirebaseJobDispatcher`上 Google Play 服務中的一些計畫 API 進行互動。 參數`context`是任何`Context`Android ,如活動。 目前,`GooglePlayDriver``IDriver`是 Firebase 作業調度程式庫中的唯一實現。 

Firebase 工作排程器的 Xamarin.Android 繫結出一種`FirebaseJobDispatcher`擴充`Context`方法, 用於從中建立 。 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

`FirebaseJobDispatcher`實例化後,可以在`JobService`類中`Job`創建和運行代碼。 由`Job`對象創建`Job.Builder`,將在下一節中討論。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>建立火庫作業排程.作業與作業.產生器

類`Firebase.JobDispatcher.Job`負責封裝運行`JobService`. 所需的元數據。 包含`Job`資訊,如在作業可以執行之前必須滿足的任何約束(`Job`如果重複)或將導致作業運行的任何觸發器。  為最低限度,`Job`必須具有_標記_(標識作業的唯一`FirebaseJobDispatcher`字串 )和應運行`JobService`的類型。 Firebase 作業調度程式將實例`JobService`化 何時運行作業。  `Job``Firebase.JobDispatcher.Job.JobBuilder`使用類別的實體建立 。 

以下代碼段是如何使用 Xamarin.Android`Job`綁定建立的最簡單範例:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

將對`Job.Builder`作業的輸入值執行一些基本驗證檢查。 如果 無法建立`Job.Builder`, 則將引發`Job`異常。  建立`Job.Builder`以預設`Job`值的 。

- `Job`的存_留期_(計劃運行多長時間)僅在設備&ndash;重新啟動`Job`後重新啟動為止。
- A`Job`不是&ndash;重複 的,它只會運行一次。
- 將`Job`計劃盡快執行 。
- 的預設重試原則`Job`是使用_指數回退_(下面在[設定重試策略](#Setting_a_RetryStrategy)部分中討論更詳細的內容)

### <a name="scheduling-a-job"></a>安排工作

創建`Job`後,需要在`FirebaseJobDispatcher`運行之前使用計劃。 有兩種方法可以調度`Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

傳`FirebaseJobDispatcher.Schedule`回的值將是以下整數值之一:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash;已成功`Job`計劃。
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash;發生未知問題,無法排程`Job`。
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash;使用了不`IDriver`合法或無法`IDriver`使用 。 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash;不支援`Trigger`。
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash;服務配置不正確或不可用。

### <a name="configuring-a-job"></a>設定工作

可以自定義作業。 如何自訂作業的範例包括:

- [將參數傳遞到作業](#Passing_Parameters_to_a_Job)&ndash;`Job`A 可能需要其他值來執行其工作,例如下載檔。
- [設置約束](#Setting_Constraints)&ndash;可能僅當滿足某些條件時才需要運行作業。 例如,僅在裝置充電時`Job`執行 。 
- [指定應運行`Job`](#Setting_Job_Triggers)&ndash;的「Firebase 作業調度程式」允許應用程式指定作業應運行的時間。  
- &ndash;[宣告失敗工作的重試政策](#Setting_a_RetryStrategy)_重試原則_為 如何處理未`FirebaseJobDispatcher`完成該`Jobs`操作 提供指導 。 

將在以下各節中討論其中每個主題。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>將參數傳遞到工作

參數透過建立式`Bundle``Job.Builder.SetExtras`方法一起傳遞的傳遞給作業:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle``IJobParameters.Extras`從`OnStartJob`方法上的屬性存取的 。

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>設定限制

約束有助於降低設備的成本或電池消耗。 類別`Firebase.JobDispatcher.Constraint`將這些限制定義為整數值:

- `Constraint.OnUnmeteredNetwork`&ndash;僅當設備連接到未計量網路時,才運行作業。 這對於防止用戶產生數據費用非常有用。
- `Constraint.OnAnyNetwork`&ndash;在設備連接到的任何網路上運行作業。 如果與`Constraint.OnUnmeteredNetwork`一起指定,此值將佔據優先順序。
- `Constraint.DeviceCharging`&ndash;僅當設備正在充電時運行作業。

使用`Job.Builder.SetConstraint`方法設定規範: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

向`JobTrigger`操作系統提供有關作業何時開始的指導。 A`JobTrigger`具有_一個執行視窗_,`Job`用於定義 應運行的時間的計劃時間。 執行視窗具有_啟動視窗_值和_結束視窗_值。 啟動視窗是設備在運行作業之前應等待的秒數,結束視窗值是運行`Job`之前 要等待的最大秒數。 

可以使用`JobTrigger``Firebase.Jobdispatcher.Trigger.ExecutionWindow`方法建立 。  例如`Trigger.ExecutionWindow(15,60)`,意味著作業應從計劃時運行 15 到 60 秒。 該方法`Job.Builder.SetTrigger`用於 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

作業的`JobTrigger`預設值由 值表示,`Trigger.Now`該值 指定在計劃后儘快運行作業。

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>設定重試原則

`Firebase.JobDispatcher.RetryStrategy`用於指定設備在嘗試重新執行失敗作業之前應使用的延遲量。 具有`RetryStrategy`_策略_,該策略定義將使用什麼時間基演演演算法來重新計畫失敗的作業,以及指定應安排作業的視窗的執行視窗。 此_重排期視窗_由兩個值定義。 第一個值是重新計畫作業(_初始回退_值)之前要等待的秒數,第二個數位是作業必須運行之前的最大秒數(_最大回退_值)。 

這兩種類型的重試策略由以下 int 值識別:

- `RetryStrategy.RetryPolicyExponential`&ndash;_指數級回退_策略將在每次失敗后以指數級方式增加初始回退值。 第一次作業失敗時,庫將等待指定的_initial間隔,然後再重新安排作業&ndash;示例 30 秒。 第二次作業失敗時,庫將等待至少60秒,然後再嘗試運行該作業。 第三次嘗試失敗后,庫將等待 120 秒,等等。 Firebase`RetryStrategy`作業調度程式庫的預設值`RetryStrategy.DefaultExponential`由 物件表示。 它的初始回退為 30 秒,最大回退為 3600 秒。
- `RetryStrategy.RetryPolicyLinear`&ndash;此策略是_一個線性回退_,應重新計畫作業以設定的時間間隔運行(直到它成功)。 線性回退最適合必須儘快完成的工作或能夠快速解決自己的問題。 Firebase 作業調度程式庫定義一`RetryStrategy.DefaultLinear`個 至少 30 秒和最多 3600 秒的重新計畫視窗。

可以使用方法定義自訂`RetryStrategy``FirebaseJobDispatcher.NewRetryStrategy`。 它需要三個參數:

1. `int policy`&ndash;_這個原則_是以前的`RetryStrategy`值之一`RetryStrategy.RetryPolicyLinear`,`RetryStrategy.RetryPolicyExponential`或 。
2. `int initialBackoffSeconds`&ndash;_初始回退_是嘗試再次運行作業之前所需的延遲(以秒為單位)。 默認值為 30 秒。 
3. `int maximumBackoffSeconds`&ndash;_最大回退_值聲明在嘗試再次運行作業之前要延遲的最大秒數。 默認值為 3600 秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消工作

可以取消已計劃的所有作業,或者僅取消使用`FirebaseJobDispatcher.CancelAll()`方法或方法`FirebaseJobDispatcher.Cancel(string)`的 單個作業:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

任一方法都將返回整數值:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash;已成功取消作業。
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash;錯誤阻止作業被取消。
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`無法取消作業,因為沒有有效的`IDriver`可用&ndash;`FirebaseJobDispatcher`。

## <a name="summary"></a>總結

本指南討論了如何使用 Firebase 作業調度程式在後台智慧地執行工作。 它討論了如何封裝要作為`JobService`執行 的工作,以及`FirebaseJobDispatcher`如何使用 來安排該工作,指定`JobTrigger`具有的條件 ,以及如何使用`RetryStrategy`來處理失敗。

## <a name="related-links"></a>相關連結

- [Xamarin.火庫.在NuGet上作業調度器](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [GitHub 上的火基作業調度程式](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.火庫.作業調度器綁定](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智慧作業計劃](https://developer.android.com/topic/performance/scheduling.html)
- [Android 電池和記憶體優化 - Google I/O 2016 (視頻)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
