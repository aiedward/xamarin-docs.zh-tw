---
title: Android 工作排程器
description: 本指南將討論如何使用 Android 工作排程器 API 排定背景工作。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 4d28b80b32ff0d20afbe643d9c000f301a8ea582
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027818"
---
# <a name="android-job-scheduler"></a>Android 工作排程器

_本指南討論如何使用 Android 工作排程器 API （可在執行 Android 5.0 （API 層級21）和更高版本的 Android 裝置上取得）來排定背景工作。_

## <a name="overview"></a>總覽 

讓 Android 應用程式回應使用者的最佳方式之一，是確保在背景執行複雜或長時間的工作。 不過，背景工作不會對裝置的使用者體驗造成負面影響。 

例如，背景工作可能會每隔三或四分鐘輪詢一次網站，以查詢特定資料集的變更。 這似乎無害，但會對電池壽命造成災難性的影響。 應用程式會重複喚醒裝置、將 CPU 提升為較高的電源狀態、開啟無線電、進行網路要求，然後處理結果。 因為裝置不會立即關閉電源並回到低電源閒置狀態，所以會變得更糟。 排程不良的背景工作可能會不小心讓裝置處於不必要和過度的電源需求的狀態。 這個看似無害的活動（輪詢網站）將在相當短的時間內，使裝置無法使用。

Android 提供下列 Api 來協助在背景中執行工作，但本身並不足以進行智慧型作業排程。 

- **[意圖服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; 意圖服務很適合用來執行工作，不過，它們不提供任何方式來排程工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; 這些 api 只允許排程工作，但不提供實際執行工作的方式。 此外，AlarmManager 只允許以時間為基礎的條件約束，這表示在特定時間或經過一段特定時間之後引發警示。 
- &ndash; Android 應用程式的 **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)** ，可以設定廣播接收器來執行工作，以回應全系統的事件或意圖。 不過，廣播接收器不會對應執行作業的時機提供任何控制。 Android 作業系統中的變更也會限制廣播接收者的運作時間，或其可以回應的工作類型。 

有兩個主要功能可有效率地執行背景工作（有時稱為_背景作業_或_作業_）：

1. 以**智慧方式排程工作**&ndash; 在應用程式于背景執行工作時，請務必將其做為良好公民。 在理想情況下，應用程式不應該要求執行作業。 相反地，應用程式應該指定作業可以執行時必須符合的條件，然後使用符合條件時將執行工作的作業系統，排程該作業。 這可讓 Android 執行此作業，以確保裝置上的最高效率。 例如，網路要求可能會批次處理成同時執行，以充分利用網路所涉及的額外負荷。
2. **封裝工作**&ndash; 要執行背景工作的程式碼，應該封裝在可獨立于使用者介面之外執行的離散元件中，而且如果工作無法完成，則會相對容易重新排定原因.

Android 工作排程器是內建于 Android 作業系統的架構，可提供 Fluent API 來簡化排程背景工作。  Android 工作排程器是由下列類型組成：

- `Android.App.Job.JobScheduler` 是一項系統服務，用來代表 Android 應用程式進行排程、執行和必要取消作業。
- `Android.App.Job.JobService` 是抽象類別，必須使用將在應用程式的主執行緒上執行作業的邏輯進行擴充。 這表示 `JobService` 會負責以非同步方式執行工作。
- `Android.App.Job.JobInfo` 物件會保留準則，以便在工作應該執行時引導 Android。

若要排定與 Android 工作排程器搭配使用，Xamarin. Android 應用程式必須將程式碼封裝在擴充 `JobService` 類別的類別中。 `JobService` 有三個生命週期方法，可在作業的存留期間呼叫：

- **Bool OnStartJob （JobParameters 參數）** &ndash; 此方法是由 `JobScheduler` 所呼叫來執行工作，並在應用程式的主要執行緒上執行。 `JobService` 會負責以非同步方式執行工作，並在剩餘工作時 `true`，如果工作已完成，則會 `false`。
    
    當 `JobScheduler` 呼叫此方法時，它會在作業的持續期間要求並保留 Android 的 wakelock。 當作業完成時，`JobService` 會負責藉由呼叫 `JobFinished` 方法（如下所述）來判斷此事實的 `JobScheduler`。

- **JobFinished （JobParameters 參數，Bool needsReschedule）** &ndash; 此方法必須由 `JobService` 呼叫，以告知 `JobScheduler` 工作已完成。 如果未呼叫 `JobFinished`，`JobScheduler` 將不會移除 wakelock，而會造成不必要的電池耗盡。 

- **Bool OnStopJob （JobParameters 參數）** &ndash; 當 Android 停止工作時，會呼叫此值。 如果應該根據重試準則來重新排程作業，它應該會傳回 `true` （更詳細地討論）。

您可以指定_條件約束_或_觸發_程式，以控制何時應執行作業。 例如，您可以限制作業，使其只會在裝置充電時執行，或在拍照時啟動工作。

本指南將詳細討論如何執行 `JobService` 類別，以及如何使用 `JobScheduler`進行排程。

## <a name="requirements"></a>需求

Android 工作排程器需要 Android API 層級21（Android 5.0）或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 工作排程器

使用 Android JobScheduler API 有三個步驟：

1. 執行 JobService 類型來封裝工作。
2. 使用 `JobInfo.Builder` 物件來建立 `JobInfo` 物件，以保存 `JobScheduler` 執行作業的準則。 
3. 使用 `JobScheduler.Schedule`排程作業。

### <a name="implement-a-jobservice"></a>執行 JobService

Android 工作排程器程式庫所執行的所有工作都必須在擴充 `Android.App.Job.JobService` 抽象類別的類型中完成。 建立 `JobService` 與使用 Android framework 建立 `Service` 非常類似： 

1. 擴充 `JobService` 類別。
2. 使用 `ServiceAttribute` 裝飾子類別，並將 `Name` 參數設定為由封裝名稱和類別名稱組成的字串（請參閱下列範例）。
3. 將 `ServiceAttribute` 上的 `Permission` 屬性設定為字串 `android.permission.BIND_JOB_SERVICE`。
4. 覆寫 `OnStartJob` 方法，並加入程式碼來執行工作。 Android 會在應用程式的主執行緒上叫用此方法，以執行作業。 需要線上程上執行數毫秒的工作，以避免封鎖應用程式。
5. 當工作完成時，`JobService` 必須呼叫 `JobFinished` 方法。 這個方法是 `JobService` 告訴 `JobScheduler` 完成工作的方式。 無法呼叫 `JobFinished` 會導致 `JobService` 在裝置上放置不必要的需求，因而縮短電池壽命。 
6. 也要覆寫 `OnStopJob` 方法是個不錯的主意。 此方法是由 Android 在作業完成前關閉，並提供 `JobService` 有機會適當處置任何資源。 如果需要重新排程作業，則這個方法應該會傳回 `true`，如果不是 desireable 來重新執行作業，則會傳回 `false`。

下列程式碼是應用程式最簡單 `JobService` 的範例，使用 TPL 以非同步方式執行一些工作：

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>建立排程工作的 JobInfo

Xamarin Android 應用程式不會直接具現化 `JobService`，而是會將 `JobInfo` 物件傳遞至 `JobScheduler`。 `JobScheduler` 會將要求的 `JobService` 物件具現化，並根據 `JobInfo`中的中繼資料來排程和執行 `JobService`。 `JobInfo` 物件必須包含下列資訊：

- **JobId** &ndash; 這是用來識別 `JobScheduler`作業的 `int` 值。 重複使用這個值將會更新任何現有的作業。 此值對應用程式而言必須是唯一的。 
- **JobService** &ndash; 此參數是明確識別 `JobScheduler` 應用來執行作業之類型的 `ComponentName`。 

此擴充方法示範如何使用 Android `Context`建立 `JobInfo.Builder`，例如活動：

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Android 工作排程器的強大功能之一，就是能夠控制作業的執行時間，或在作業可能執行的條件下。 下表描述 `JobInfo.Builder` 上的一些方法，可讓應用程式影響作業執行的時機：  

|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定在執行作業之前應觀察的延遲（以毫秒為單位）。 |
| `SetOverridingDeadline`  | 宣告作業必須在這段時間之前執行（以毫秒為單位）的。 |
| `SetRequiredNetworkType`  | 指定作業的網路需求。 |
| `SetRequiresBatteryNotLow` | 只有在裝置未向使用者顯示「電力不足」警告時，才能執行此作業。 |
| `SetRequiresCharging` | 只有在電池充電時，才可以執行此作業。 |
| `SetDeviceIdle` | 當裝置忙碌時，將會執行此作業。 |
| `SetPeriodic` | 指定應該定期執行作業。 |
| `SetPersisted` | 作業應該會在裝置重新開機時 perisist。 | 

`SetBackoffCriteria` 會提供一些指引，說明在嘗試再次執行作業之前，`JobScheduler` 應該等待多久時間。 輪詢準則有兩個部分：延遲（以毫秒為單位）（預設值為30秒），以及應該使用的後端類型（有時稱為「輪詢_原則_」或「_重試原則_」）。 這兩個原則封裝在 `Android.App.Job.BackoffPolicy` 列舉：

- `BackoffPolicy.Exponential` &ndash; 指數輪詢原則會在每次失敗後，以指數方式增加初始輪詢值。 第一次作業失敗時，程式庫會等待重新排程作業之前指定的初始間隔–範例為30秒。 第二次作業失敗時，程式庫會等待至少60秒，然後才嘗試執行此作業。 第三次嘗試失敗之後，程式庫會等候120秒，依此類推。 此為預設值。
- `BackoffPolicy.Linear` &ndash; 此策略是一種線性輪詢，應將作業重新排程為在設定的間隔執行（直到成功為止）。 線性輪詢最適用于必須儘快完成的工作，或可快速解決的問題。 

如需建立 `JobInfo` 物件的詳細資訊，請參閱[Google 的 `JobInfo.Builder` 類別檔](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>透過 JobInfo 將參數傳遞至作業

藉由建立與 `Job.Builder.SetExtras` 方法一起傳遞的 `PersistableBundle`，將參數傳遞至作業：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle` 可從 `JobService`的 `OnStartJob` 方法中的 `Android.App.Job.JobParameters.Extras` 屬性存取：

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>排程工作

若要排程作業，Xamarin. Android 應用程式會取得 `JobScheduler` 系統服務的參考，並使用上一個步驟中所建立的 `JobInfo` 物件來呼叫 `JobScheduler.Schedule` 方法。 `JobScheduler.Schedule` 會立即傳回兩個整數值的其中一個：

- **JobScheduler. ResultSuccess** &ndash; 已成功排程作業。 
- **JobScheduler。 ResultFailure** &ndash; 無法排程作業。 這通常是因 `JobInfo` 參數衝突所造成。

這段程式碼是排程作業和通知使用者排程嘗試結果的範例：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>取消作業

您可以取消已排程的所有作業，或僅使用 `JobsScheduler.CancelAll()` 方法或 `JobScheduler.Cancel(jobId)` 方法的單一作業：

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>總結

本指南將討論如何使用 Android 工作排程器，以智慧方式在背景中執行工作。 它討論了如何封裝要當做 `JobService` 執行的工作，以及如何使用 `JobScheduler` 來排程該工作、指定 `JobTrigger` 的準則，以及如何使用 `RetryStrategy`來處理失敗。

## <a name="related-links"></a>相關連結

- [智慧型作業-排程](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 參考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [使用 JobScheduler 來排程工作，例如專業人員](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 電池和記憶體優化-Google i/o 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
