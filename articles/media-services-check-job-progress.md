<properties linkid="develop-media-services-how-to-guides-check-job-progress" urlDisplayName="Проверка хода выполнения задания" pageTitle="Проверка хода выполнения задания в службах мультимедиа — Azure" metaKeywords="" description="Узнайте, как использовать код обработчика событий для отслеживания хода выполнения задания и отправки обновлений состояния. Примеры кода написаны на C# и используют пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Практическое руководство. Проверка хода выполнения задания" authors="migree" solutions="" manager="" editor="" />





<h1>Практическое руководство. Проверка хода выполнения задания</h1>
Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел: [Практическое руководство. Кодировка актива](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409).

При выполнении заданий часто требуется способ отслеживания хода выполнения задачи. Следующий пример кода определяет обработчик событий StateChanged. Этот обработчик отслеживает ход выполнения задачи и предоставляет обновленное состояние. Кроме того, код определяет метод LogJobStop. Этот вспомогательный метод заносит в журнал сведения об ошибках.

<pre><code>
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    builder.AppendLine("Media Services account location: " + _accountLocation);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
<h1>Следующие шаги</h1>
Теперь, когда вы знаете, как создать задание и отслеживать ход его выполнения, следующим действием будет защита активов. Дополнительные сведения см. в разделе [Практическое руководство. Защита актива с помощью служб мультимедиа в Azure](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

