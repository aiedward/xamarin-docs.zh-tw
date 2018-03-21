---
title: "Android 的作業排程器"
description: "本指南會討論如何使用 Android 的工作排程器 API 的背景工作的排程。"
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: dc72b7e4da330185b00541f923d9c4b64b91bc95
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2018
---
# <a name="android-job-scheduler"></a>Android 的作業排程器

_本指南將告訴您如何使用 Android 的工作排程器 API，可在執行 Android 5.0 (API level 21) 的 Android 裝置的背景工作的排程和更新版本。_


## <a name="overview"></a>總覽 

Android 應用程式能繼續回應使用者以最佳方式之一是確保複雜或長時間執行的工作在背景中執行。 不過，很重要，背景工作不會產生負面影響的使用者經驗與裝置。 

比方說，背景工作可能網站每隔三或四個分鐘輪詢查詢特定的資料集的變更。 不過，它會對電池壽命而言的影響，這似乎良性的。 應用程式重複會喚醒裝置，請將 CPU 提高為更高的電源狀態、 無線電開啟電源，確定網路要求，然後處理結果。 它變差，因為裝置將不會立即關閉電源，並返回低電源閒置狀態。 無法順利排程的背景工作不小心可能會保留裝置處於的不必要和多餘的電源需求。 這看似無害的活動 （輪詢網站） 將導致裝置在相對較短的時間內無法使用。

Android 提供下列應用程式開發介面，以協助在背景中執行的工作，但本身沒有足夠的智慧型工作排程。 

* **[意圖服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意圖服務非常適合用來執行工作，不過它們會不提供任何方法可以排程工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;只有這些 Api 可讓工作排程，但不能實際執行工作。 此外，AlarmManager 僅允許以時間為基礎的條件約束，這表示在特定時間或經過一段時間後會引發警示。 
* **[廣播接收者](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 應用程式可以設定廣播的接收者，以回應系統事件或對應方式執行工作。 不過，廣播的接收者不提供任何控制何時應執行的工作。 Android 作業系統中的變更也會限制當廣播的接收者也能運作，或它們可以回應的工作種類。 

有兩個主要的功能來有效率地執行背景工作 (有時稱為_背景工作_或_作業_):

1. **有智慧地排程工作**&ndash;很重要的應用程式會進行的工作在背景中的，它會以為良好的公民。 在理想情況下，應用程式不應要求執行作業。 相反地，應用程式應該指定工作可以執行，並再排程符合條件時，就會執行工作的作業系統與該作業時必須符合的條件。 這可讓 Android 来執行此作業，以確保在裝置上的最大效率。 例如，網路要求可能會批次處理以發揮最大的負擔與網路有關的所有項目執行。
2. **封裝工作**&ndash;執行背景工作的程式碼應該封裝中不連續的元件，可以獨立使用者介面執行將會是相當容易重新排定工作無法完成基於某些原因。

Android 的作業排程器是內建提供 fluent API 簡化排程背景工作，Android 作業系統的架構。  Android 的作業排程器包含下列類型：

* `Android.App.Job.JobScheduler`是用來排程、 執行和必要時取消，工作代表 Android 應用程式的系統服務。
* `Android.App.Job.JobService`是抽象類別必須使用將在應用程式的主執行緒執行作業的邏輯擴充。 這表示`JobService`負責的以非同步方式執行工作的方式。
* `Android.App.Job.JobInfo`物件會保存引導 Android 時，工作應該執行的準則。

若要排程工作與 Android 的工作排程器，Xamarin.Android 應用程式必須封裝可擴充的類別中的程式碼`JobService`類別。 `JobService` 有三個生命週期的方法，可以呼叫作業的存留期間：

* **bool OnStartJob （JobParameters 參數）** &ndash;這個方法會呼叫`JobScheduler`來執行工作，以及在應用程式的主執行緒上執行。 它負責`JobService`來以非同步方式執行的工作和`true`剩餘，如果沒有工作或`false`如果完成的工作。
    
    當`JobScheduler`呼叫這個方法，它將會要求並從 Android wakelock 保留作業的持續時間。 作業完成時，它負責`JobService`告訴`JobScheduler`藉由呼叫這個事實`JobFinished`方法 （下一步所述）。

* **JobFinished （JobParameters 參數，bool needsReschedule）** &ndash;必須由呼叫這個方法`JobService`告訴`JobScheduler`所完成的工作。 如果`JobFinished`不呼叫`JobScheduler`將不會移除 wakelock，導致不必要的電池清空。 

* **bool OnStopJob （JobParameters 參數）** &ndash; Android 提前停止作業時，這會呼叫。 它應該傳回`true`如果應該重試符合準則 （以下更詳細討論） 重新排程工作。

您可指定_條件約束_或_觸發程序_，將控制作業時可以或應該執行。 例如，很可能限制，讓它只會在執行時裝置正在充電或啟動的工作，當圖片不會採取的工作。

本指南將探討如何實作`JobService`類別，並排定它與`JobScheduler`。

## <a name="requirements"></a>需求

Android 的作業排程器需要 Android API level 21 (Android 5.0) 或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 的作業排程器

有三個步驟，使用 Android JobScheduler API:

1. 實作 JobService 類型，可封裝工作。
2. 使用`JobInfo.Builder`物件以建立`JobInfo`物件保留的準則`JobScheduler`来執行此作業。 
3. 作業使用排程`JobScheduler.Schedule`。

### <a name="implement-a-jobservice"></a>實作 JobService

Android 的作業排程器程式庫所執行的所有工作必須都以要擴充的型別`Android.App.Job.JobService`抽象類別。 建立`JobService`非常類似於建立`Service`Android 架構： 

1. 擴充`JobService`類別。
2. 裝飾以子類別`ServiceAttribute`並設定`Name`字串所組成的封裝名稱和類別名稱的參數 （請參閱下列範例）。
3. 設定`Permission`屬性`ServiceAttribute`字串`android.permission.BIND_JOB_SERVICE`。
4. 覆寫`OnStartJob`方法，加入程式碼來執行工作。 Android 將會叫用這個方法以執行作業的應用程式的主執行緒上。 需要較久的幾毫秒，應該以避免封鎖應用程式的執行緒上執行的工作。
5. 當工作完成時，`JobService`必須呼叫`JobFinished`方法。 這個方法是如何`JobService`告訴`JobScheduler`工作已完成。 無法呼叫`JobFinished`會導致`JobService`放在裝置上，縮短的電池壽命的不必要的要求。 
6. 最好也覆寫`OnStopJob`方法。 當工作正在關機之後才能完成，並提供 Android 呼叫這個方法`JobService`適當處置任何資源的機會。 此方法應傳回`true`重新排定工作，在需要時或`false`如果不是 desireable 重新執行的工作。
   

下列程式碼是最簡單的範例`JobService`的應用程式，使用 TPL 以非同步方式執行一些工作：

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>建立排程作業 JobInfo

Xamarin.Android 應用程式不必執行個體化`JobService`直接管理，而它們將會通過`JobInfo`物件`JobScheduler`。 `JobScheduler`會具現化要求`JobService`物件排程及執行`JobService`的中繼資料中根據`JobInfo`。 A`JobInfo`物件必須包含下列資訊：

* **JobId** &ndash;這是`int`值，用來識別工作以`JobScheduler`。 重複使用此值將會更新現有的作業。 值必須是唯一的應用程式。 
* **JobService** &ndash;這個參數是`ComponentName`可明確識別型別，`JobScheduler`應用於執行工作。 
  

這個擴充方法將示範如何建立`JobInfo.Builder`android `Context`，例如活動：

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

// Sample usage - creates a JobBuilder for a DownloadJob andsets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creats a JobInfo object.
```

Android 的作業排程器的強大功能是能夠控制作業的執行，或在什麼條件作業可能會執行。 下表描述的一些方法上`JobInfo.Builder`，讓應用程式的影響時執行的作業：  


|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定執行工作之前，應該要觀察的延遲 （以毫秒為單位）。 |
| `SetOverridingDeadline`  | 會宣告工作必須在執行之前經過這個時間 （以毫秒為單位）。 |
| `SetRequiredNetworkType`  | 指定工作的網路需求。 |
| `SetRequiresBatteryNotLow` | 當裝置未向使用者顯示 「 電力偏低 」 警告，可能只會執行作業。 |
| `SetRequiresCharging` | 電池充電時，可能只會執行作業。 |
| `SetDeviceIdle` | 當裝置正在使用中，會執行作業。 |
| `SetPeriodic` | 指定應定期執行作業。 |
| `SetPersisted` | 作業會在裝置重新啟動期間應該 perisist。 | 


`SetBackoffCriteria`如何提供一些指引長度`JobScheduler`應等待嘗試再次執行工作。 有兩個部分的輪詢準則： 應該使用毫秒 （30 秒的預設值） 和類型的撤退延遲 (有時稱為_撤退原則_或_重試原則_). 兩個原則都會封裝在`Android.App.Job.BackoffPolicy`列舉：

* `BackoffPolicy.Exponential` &ndash; 指數輪詢原則將會以指數方式增加初始輪詢值之後每一項失敗。 工作失敗，第一次程式庫會在等候之前先重新排定工作 – 範例 30 秒為指定的初始間隔。 作業失敗時，第二次程式庫會等候至少 60 秒後再嘗試執行此作業。 第三個嘗試失敗之後，程式庫會等待 120 秒，以此類推。 這是預設值。
* `BackoffPolicy.Linear` &ndash; 這項策略是線性的輪詢應該重新排定工作，可以執行設定的間隔 （直到它成功）。 線性輪詢最適合針對必須儘快完成的工作或將快速自行解決問題。 

如需有關建立`JobInfo`物件，請閱讀[Google 的文件`JobInfo.Builder`類別](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>將參數傳遞至透過 JobInfo 作業

參數傳遞至作業建立`PersistableBundle`連同傳遞`Job.Builder.SetExtras`方法：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle`存取從`Android.App.Job.JobParameters.Extras`屬性`OnStartJob`方法`JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>排程工作

若要將作業排程，Xamarin.Android 應用程式將取得的參考`JobScheduler`系統服務和呼叫`JobScheduler.Schedule`方法`JobInfo`上一個步驟中所建立的物件。 `JobScheduler.Schedule` 會立即傳回並提供兩個整數值的其中一個：

* **JobScheduler.ResultSuccess** &ndash;作業已成功排程。 
* **JobScheduler.ResultFailure** &ndash;無法排程工作。 這通常因為衝突`JobInfo`參數。

此程式碼是嘗試的排程工作，並通知使用者在排程結果的範例：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>取消作業

若要取消已排程的所有作業或只是單一工作，使用`JobsScheduler.CancelAll()`方法或`JobScheduler.Cancel(jobId)`方法：

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>總結

本指南將討論如何使用 Android 的作業排程器，以聰明的方式在背景中執行工作。 它討論如何封裝要執行為工作`JobService`以及如何使用`JobScheduler`排程的工作，指定與條件`JobTrigger`和失敗應該如何處理與`RetryStrategy`。

## <a name="related-links"></a>相關連結

- [智慧型工作排程](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 參考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [排程作業像 JobScheduler 與專業](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 使用電池及記憶體最佳化的 Google I/O 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert-Xamarin 大學](https://www.youtube.com/watch?v=aSjBBPYjelE)