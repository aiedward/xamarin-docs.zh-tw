---
title: Firebase 工作發送器
description: 本指南將討論如何使用 Google 的 Firebase 作業發送器程式庫排程背景工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 0ade609997e391e24d4a6da250172efa81a5d490
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571333"
---
# <a name="firebase-job-dispatcher"></a>Firebase 工作發送器

_本指南將討論如何使用 Google 的 Firebase 作業發送器程式庫排程背景工作。_

## <a name="overview"></a>概觀

將 Android 應用程式保持回應給使用者的最佳方式之一，就是確保在背景執行複雜或長時間執行的工作。 不過，背景工作很重要，不會對裝置的使用者體驗造成負面影響。 

例如，背景工作可能會每隔三或四分鐘輪詢一次網站，以查詢特定資料集的變更。 這似乎無害，但會對電池壽命產生災難性的影響。 應用程式將會重複喚醒裝置、將 CPU 提升為較高的電源狀態、開啟無線電、提出網路要求，然後處理結果。 它會變得更糟，因為裝置不會立即關閉電源，並回到低電源閒置狀態。 排程不良的背景工作可能會不慎讓裝置處於不必要和過多電源需求的狀態。 這種看似無害的活動 (輪詢網站) 會在相對較短的時間內轉譯裝置無法使用。

Android 提供下列 Api 來協助在背景中執行工作，但本身並不足以進行智慧型作業排程。 

- **[意圖服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; 意圖服務很適合用來執行工作，但它們不提供任何方式來排程工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; 這些 Api 只允許排程工作，但不提供實際執行工作的方式。 此外，AlarmManager 只允許以時間為基礎的條件約束，這表示在特定時間或經過一段時間之後，就會引發警示。 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule 是一個絕佳的 API，可搭配作業系統來排程工作。 不過，它只適用于以 API 層級21或更高版本為目標的 Android 應用程式。 
- **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 應用程式可以設定廣播接收器來執行工作，以回應全系統的事件或意圖。 不過，廣播接收者不會提供任何作業執行時的控制權。 此外，Android 作業系統的變更將會限制廣播接收器的運作時間，或可回應的工作類型。 

有兩個主要功能可以有效率地執行背景工作 (有時也稱為 _背景_ 工作或 _作業_) ：

1. 以**智慧方式排程工作** &ndash;當應用程式在背景中執行工作時，這點很重要，因為它會以良好公民的形式來執行工作。 在理想情況下，應用程式不應要求執行作業。 相反地，應用程式應指定當作業可以執行時必須符合的條件，然後排程在符合條件時要執行的工作。 這可讓 Android 以智慧方式執行工作。 例如，可能會批次處理網路要求以同時執行，以充分利用與網路相關的額外負荷。
2. **封裝工作** &ndash; 執行背景工作的程式碼應封裝在可獨立于使用者介面之外執行的獨立元件中，而且如果工作因某些原因而無法完成，則會相當容易重新排程。

Firebase 作業發送器是 Google 提供的程式庫，提供流暢的 API 來簡化背景工作的排程。 這是為了取代 Google Cloud Manager。 Firebase 作業發送器包含下列 Api：

- `Firebase.JobDispatcher.JobService`是抽象類別，必須使用將在背景工作中執行的邏輯進行擴充。
- `Firebase.JobDispatcher.JobTrigger`當應該啟動作業時宣告。 這通常會以時間範圍表示，例如，在啟動工作之前至少等候30秒，但在5分鐘內執行作業。
- `Firebase.JobDispatcher.RetryStrategy`包含當作業無法正常執行時應執行之作業的相關資訊。 重試策略會指定嘗試重新執行作業之前要等候的時間長度。 
- `Firebase.JobDispatcher.Constraint`是一個選擇性的值，可描述必須符合才能執行作業的條件，例如裝置處於不計費的網路或收費。
- `Firebase.JobDispatcher.Job`是一個 api，可將先前的 api 統一至可由排程的工作單位 `JobDispatcher` 。 `Job.Builder`類別是用來具現化 `Job` 。
- 會 `Firebase.JobDispatcher.JobDispatcher` 使用前三個 api 來排程作業系統的工作，並視需要提供取消作業的方法。

為了排程使用 Firebase 作業發送器，Xamarin. Android 應用程式必須將程式碼封裝在擴充類別的型別中 `JobService` 。 `JobService` 有三個生命週期方法可在作業的存留期間呼叫：

- **`bool OnStartJob(IJobParameters parameters)`**&ndash;這個方法是工作發生的位置，應該一律執行。 它會在主執行緒上執行。 `true`如果剩餘的工作或工作已完成，此方法將會傳回 `false` 。 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash;當工作因為某些原因而停止時，就會呼叫此作業。 `true`如果應該重新排程作業以供稍後進行，它應該會傳回。
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash;當 `JobService` 完成任何非同步工作時，會呼叫這個方法。 

若要排程作業，應用程式會將物件具現化 `JobDispatcher` 。 然後， `Job.Builder` 會使用來建立 `Job` 物件，此物件 `JobDispatcher` 會嘗試並排程要執行的作業。

本指南將討論如何將 Firebase 作業發送器新增至 Xamarin Android 應用程式，並使用它來排程背景工作。

## <a name="requirements"></a>規格需求

Firebase 作業發送器需要 Android API 層級9或更高版本。 Firebase 作業發送器程式庫依賴 Google Play Services 提供的部分元件;裝置必須安裝 Google Play Services。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>在 Xamarin 中使用 Firebase 作業發送器程式庫

若要開始使用 Firebase 作業發送器，請先將 [Firebase JobDispatcher NuGet 套件](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) 新增至 Xamarin. Android 專案。 在 NuGet 封裝管理員中，搜尋仍在發行前) 的 **Firebase. JobDispatcher** 套件 (。

新增 Firebase 作業發送器程式庫之後，請建立 `JobService` 類別，然後將它排程為使用的實例來執行 `FirebaseJobDispatcher` 。

### <a name="creating-a-jobservice"></a>建立 JobService

Firebase 作業發送器程式庫所執行的所有工作都必須在擴充抽象類別的類型中完成 `Firebase.JobDispatcher.JobService` 。 建立 `JobService` 非常類似于 `Service` 使用 Android framework 建立： 

1. 擴充 `JobService` 類別
2. 使用裝飾子類別 `ServiceAttribute` 。 雖然不是絕對必要，但建議您明確設定 `Name` 參數，以協助進行偵錯工具 `JobService` 。 
3. 加入， `IntentFilter` 以 `JobService` 在 **AndroidManifest.xml**中宣告。 這也可協助 Firebase 作業發送器程式庫找出並叫用 `JobService` 。

下列程式碼是最簡單 `JobService` 的應用程式範例，使用 TPL 以非同步方式執行某些工作：

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

### <a name="creating-a-firebasejobdispatcher"></a>建立 FirebaseJobDispatcher

在可以排程任何工作之前，必須先建立 `Firebase.JobDispatcher.FirebaseJobDispatcher` 物件。 `FirebaseJobDispatcher`負責排程 `JobService` 。 下列程式碼片段是建立實例的一種方式 `FirebaseJobDispatcher` ： 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在先前的程式碼片段中， `GooglePlayDriver` 是類別，可協助您 `FirebaseJobDispatcher` 與裝置上 Google Play Services 中的某些排程 api 進行互動。 參數 `context` 是任何 Android `Context` ，例如活動。 目前， `GooglePlayDriver` 是 `IDriver` Firebase 作業發送器程式庫中的唯一實作為。 

Firebase 作業發送器的 Xamarin. Android 系結會提供擴充方法，以從建立 `FirebaseJobDispatcher` `Context` ： 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一旦具 `FirebaseJobDispatcher` 現化之後，就可以建立， `Job` 並在類別中執行程式碼 `JobService` 。 `Job`是由物件所建立 `Job.Builder` ，並將在下一節中討論。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用作業建立 Firebase. JobDispatcher

`Firebase.JobDispatcher.Job`類別負責封裝執行所需的中繼資料 `JobService` 。 `Job`包含一些資訊，例如在作業可以執行之前必須符合的任何條件約束、 `Job` 是否為週期性，或任何會導致作業執行的觸發程式。  至少 `Job` 必須有一個 _標記_ (唯一的字串，以識別) 的作業 `FirebaseJobDispatcher` ，以及應執行之的型別 `JobService` 。 當執行作業時，Firebase 作業發送器將會具 `JobService` 現化。  `Job`使用類別的實例建立 `Firebase.JobDispatcher.Job.JobBuilder` 。 

下列程式碼片段是如何 `Job` 使用 Xamarin. Android 系結建立的最簡單範例：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`會對作業的輸入值執行一些基本驗證檢查。 如果無法建立，則會擲回例外狀況 `Job.Builder` `Job` 。  `Job.Builder`會建立 `Job` 具有下列預設值的：

- 的 `Job` _存留期_ (排程執行的時間長度) 只會在裝置 &ndash; 重新開機後重新開機時才會 `Job` 遺失。
- `Job`不會重複 &ndash; 執行，只會執行一次。
- `Job`系統會排定儘快執行。
- 的預設重試策略 `Job` 是使用 _指數_ 輪詢 (在下面的 [設定 RetryStrategy](#Setting_a_RetryStrategy) 一節中會更詳細地討論) 

### <a name="scheduling-a-job"></a>排程作業

建立之後 `Job` ，必須在執行之前，以排程 `FirebaseJobDispatcher` 。 排程的方法有兩種 `Job` ：

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

傳回的值 `FirebaseJobDispatcher.Schedule` 將會是下列其中一個整數值：

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; `Job` 已成功排程。
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash;發生未知的問題，導致無法 `Job` 進行排程。
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash;使用了不正確， `IDriver` 或 `IDriver` 無法使用。 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash; `Trigger` 不支援。
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash;服務未正確設定或無法使用。

### <a name="configuring-a-job"></a>設定作業

您可以自訂作業。 如何自訂作業的範例包括下列各項：

- [將參數傳遞至作業](#Passing_Parameters_to_a_Job) &ndash;`Job`可能需要額外的值來執行其工作，例如下載檔案。
- [設定條件約束](#Setting_Constraints) &ndash; 在符合特定條件時，可能只需要執行工作。 例如，只有在 `Job` 裝置充電時才執行。 
- [指定何時 `Job` 應執行](#Setting_Job_Triggers) &ndash; Firebase 作業發送器可讓應用程式指定工作應該執行的時間。  
- 宣告[失敗作業](#Setting_a_RetryStrategy) &ndash; 的重試策略_重試策略_提供的指引，說明如何 `FirebaseJobDispatcher` `Jobs` 完成無法完成的作業。 

下列各節將詳細討論這些主題。

<a name="Passing_Parameters_to_a_Job"></a>

#### <a name="passing-parameters-to-a-job"></a>將參數傳遞至作業

藉由建立與方法一起傳遞的，將參數傳遞至工作 `Bundle` `Job.Builder.SetExtras` ：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`可從 `IJobParameters.Extras` 方法上的屬性存取 `OnStartJob` ：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints"></a>

#### <a name="setting-constraints"></a>設定條件約束

條件約束有助於降低裝置上的成本或電池耗盡。 類別會將 `Firebase.JobDispatcher.Constraint` 這些條件約束定義為整數值：

- `Constraint.OnUnmeteredNetwork`&ndash;只有當裝置連線到不會執行的網路時，才執行此作業。 這有助於防止使用者產生資料費用。
- `Constraint.OnAnyNetwork`&ndash;在裝置所連接的任何網路上執行此作業。 如果與一起指定 `Constraint.OnUnmeteredNetwork` ，此值會優先使用。
- `Constraint.DeviceCharging`&ndash;只有在裝置正在收費時，才執行此作業。

條件約束的設定 `Job.Builder.SetConstraint` 方法如下： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers"></a>

`JobTrigger`提供有關何時應開始作業的作業系統指引。 `JobTrigger`具有執行中的_視窗_，該視窗會定義應該執行的排程時間 `Job` 。 執行視窗具有 _開始視窗_ 值和 _結束時間範圍_ 值。 開始視窗是在執行作業之前，裝置應該等候的秒數，而結束時間範圍值則是執行之前等候的最大秒數 `Job` 。 

您 `JobTrigger` 可以使用方法來建立 `Firebase.Jobdispatcher.Trigger.ExecutionWindow` 。  例如 `Trigger.ExecutionWindow(15,60)` ，這表示作業應該在15到60秒之間，從排程的時間執行。 `Job.Builder.SetTrigger`方法是用來 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

作業的預設 `JobTrigger` 值是以值表示 `Trigger.Now` ，這會指定在排程之後儘快執行作業。

<a name="Setting_a_RetryStrategy"></a>

#### <a name="setting-a-retrystrategy"></a>設定 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用來指定裝置在嘗試重新執行失敗的作業之前，應該使用多少延遲。 `RetryStrategy`具有_原則_，可定義要使用哪個時間基底演算法來重新排程失敗的工作，以及指定可在其中排程作業之視窗的執行視窗。 這項重新 _排定的視窗_ 是由兩個值所定義。 第一個值是重新排程工作 (_初始_ 輪詢值之前要等候的秒數) ，而第二個值是作業必須執行的最大秒數， (_最大_ 的輪詢值) 。 

這兩種類型的重試原則是由這些 int 值所識別：

- `RetryStrategy.RetryPolicyExponential`&ndash;_指數_輪詢原則會在每次失敗後以指數方式增加初始輪詢值。 當工作第一次失敗時，程式庫會等候在重新排程作業範例30秒之前所指定的 _initial 間隔 &ndash; 。 第二次作業失敗時，程式庫會等待至少60秒，然後再嘗試執行此作業。 第三次嘗試失敗之後，程式庫將會等候120秒，依此類推。 `RetryStrategy`Firebase 作業發送器程式庫的預設值是由物件表示 `RetryStrategy.DefaultExponential` 。 它有30秒的初始輪詢和3600秒的最大輪詢。
- `RetryStrategy.RetryPolicyLinear`&ndash;這項策略是一種_線性_輪詢，應該重新排程作業以設定的間隔執行， (直到成功) 為止。 線性輪詢最適用于必須儘快完成的工作，或可快速自行解決的問題。 Firebase 作業發送器程式庫會定義 `RetryStrategy.DefaultLinear` 具有至少30秒和最多3600秒之重新排程時段的。

您可以使用方法來定義自訂 `RetryStrategy` `FirebaseJobDispatcher.NewRetryStrategy` 。 它會採用三個參數：

1. `int policy`&ndash;_原則_是先前的其中一個 `RetryStrategy` 值、 `RetryStrategy.RetryPolicyLinear` 或 `RetryStrategy.RetryPolicyExponential` 。
2. `int initialBackoffSeconds`&ndash;_初始_輪詢是在嘗試再次執行作業之前需要的延遲（以秒為單位）。 此值的預設值為30秒。 
3. `int maximumBackoffSeconds`&ndash;_最大_輪詢值會宣告嘗試重新執行作業之前延遲的最大秒數。 預設值為3600秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作業

您可以使用方法或方法來取消已排程的所有作業，或只取消單一作業 `FirebaseJobDispatcher.CancelAll()` `FirebaseJobDispatcher.Cancel(string)` ：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

這兩種方法都會傳回整數值：

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash;已成功取消作業。
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash;發生錯誤，導致無法取消工作。
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`&ndash; `FirebaseJobDispatcher` 因為沒有可用的有效，所以無法取消作業 `IDriver` 。

## <a name="summary"></a>摘要

本指南將討論如何使用 Firebase 作業發送器，以智慧方式在背景中執行工作。 它會討論如何封裝要執行的工作 `JobService` ，以及如何使用 `FirebaseJobDispatcher` 來排程該工作、指定的準則， `JobTrigger` 以及如何處理失敗 `RetryStrategy` 。

## <a name="related-links"></a>相關連結

- [NuGet 上的 Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-作業-GitHub 上的發送器](https://github.com/firebase/firebase-jobdispatcher-android)
- [Firebase. JobDispatcher 系結](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智慧型工作-排程](https://developer.android.com/topic/performance/scheduling.html)
- [Android 電池與記憶體優化-Google i/o 2016 (video) ](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
