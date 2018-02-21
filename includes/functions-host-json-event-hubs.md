```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|maxBatchSize|64|Максимальное число событий, получаемых в цикле получения.|
|prefetchCount|Недоступно|Значение PrefetchCount по умолчанию, которое будет использоваться базовым узлом EventProcessorHost.| 
|batchCheckpointFrequency|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.| 
