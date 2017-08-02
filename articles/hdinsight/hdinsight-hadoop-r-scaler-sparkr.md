---
title: "Использование ScaleR и SparkR с Azure HDInsight | Документация Майкрософт"
description: "Использование ScaleR и SparkR с R Server и HDInsight"
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29733f6f6b725dd4735219ed221431805558a5e2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---

# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Совместное использование ScaleR и SparkR в HDInsight

В этой статье показано, как прогнозировать задержки прибытия авиарейсов с помощью модели логистической регрессии **ScaleR** на основе данных по задержкам авиарейсов и погодных данных, объединенных с помощью **SparkR**. В этом сценарии демонстрируются возможности ScaleR для работы с данными в Spark и Microsoft R Server для выполнения задач аналитики. Сочетание этих технологий позволяет применять новейшие возможности распределенной обработки.

Хотя оба пакета работают поверх механизма выполнения Spark Hadoop, совместное использование данных в памяти для них заблокировано, так как для каждого из них требуются отдельные сеансы Spark. Это будет исправлено в последующих версиях R Server, а пока временным решением является поддержание не перекрывающихся сеансов Spark и обмен данными с помощью промежуточных файлов. В приведенных в этой статье инструкциях показано, что обеспечить выполнение этих требований достаточно просто.

Мы будем использовать пример, изначально озвученный в докладе Марио Инчиоза (Mario Inchiosa) и Рони Берда (Roni Burd) на конференции Strata 2016, который доступен в виде вебинара [Building a Scalable Data Science Platform with R](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio) (Создание масштабируемой платформы анализа и обработки данных на основе R). В примере SparkR используется для объединения набора данных по задержкам прибытия рейсов известных авиакомпаний с погодными данными в аэропортах отправления и прибытия. Объединенные данные затем используются в качестве входных для модели логистической регрессии ScaleR, которая служит для прогнозирования задержки прибытия.

Код, который будет использоваться в этом руководстве, первоначально был написан для R Server под управлением Spark в кластере HDInsight в Azure. Однако понятие совместного использования SparkR и ScaleR в одном скрипте в равной степени применимо к локальным средам. Здесь предполагается средний уровень знания языка R и библиотеки [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) сервера R Server. В процессе рассмотрения сценария также приводятся общие сведения об использовании [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html).

## <a name="the-airline-and-weather-datasets"></a>Наборы данных об авиакомпаниях и погоде

Открытый набор данных об авиакомпаниях **AirOnTime08to12CSV** содержит сведения о прибытии и отправлении всех коммерческих авиарейсов в США с октября 1987 г. по декабрь 2012 г. Это большой набор данных, содержащий около 150 миллионов записей. Примерный размер составляет 4 ГБ в не распакованном виде. Он доступен в [архиве правительства США](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Кроме того, он доступен в виде ZIP-файла (AirOnTimeCSV.zip), содержащего набор из 303 отдельных ежемесячных CSV-файлов из [репозитория Revolution Analytics](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/).

Чтобы увидеть влияние погоды на задержки авиарейсов, также требуются данные по погоде в каждом из аэропортов. Их можно скачать как ZIP-файлы в необработанном виде по месяцам из [репозитория Национального управления океанических и атмосферных исследований](http://www.ncdc.noaa.gov/orders/qclcd/). Для этого примера мы возьмем данные по погоде за период с мая 2007 г. по декабрь 2012 г. и воспользуемся почасовыми файлами данных каждого из 68 ежемесячных ZIP-архивов. Ежемесячные ZIP-файлы также содержат сопоставления (YYYYMMstation.txt) между идентификатором метеостанции (WBAN), связанным с ней аэропортом (CallSign) и отклонением часового пояса аэропорта от времени UTC (часовой пояс). Все они потребуются при объединении с данными авиакомпаний по задержке рейсов.

## <a name="setting-up-the-spark-environment"></a>Настройка среды Spark

Сначала необходимо настроить среду Spark. Начнем с указания каталога, содержащего каталоги входных данных, и создания контекста вычислений Spark и функции ведения журнала в консоли:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Далее мы добавим "Spark_Home" в путь поиска для пакетов R, чтобы использовать SparkR, и инициализируем сеанс SparkR.

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Подготовка данных о погоде

Чтобы подготовить данные по погоде, подставим их в столбцы, необходимые для моделирования: 

- "Visibility"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

Затем добавим код аэропорта, связанного с метеостанцией, и преобразуем значения местного времени в формат UTC.

Начнем с создания файла для сопоставления сведений о метеостанции (WBAN) с кодом аэропорта. Их можно получить из файла сопоставления, который содержится в данных по погоде, сопоставив поле *CallSign* (например, LAX) в файле данных по погоде с *источником* в данных по авиакомпаниям. Также мы получаем сопоставление *WBAN* с *идентификатором аэропорта* (например, 12892 для LAX), включающее сведения о *часовом поясе* и сохраненные в CSV-файле с именем wban-to-airport-id-tz.CSV, который мы можем использовать. Например:

| AirportID | WBAN | TimeZone
|-----------|------|---------
| 10685 | 54831 | -6
| 14871 | 24232 | –8
| .. | .. | ..

Следующий код считывает каждый почасовой необработанный файл данных по погоде, выполняет подстановку в необходимые столбцы, объединяет файл сопоставления метеостанции, преобразует дату и время измерений в формат UTC, а затем записывает новую версию файла:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Импорт данных авиакомпаний и погодных данных в таблицы данных Spark

Теперь мы используем функцию SparkR [read.df()](https://docs.databricks.com/spark/latest/sparkr/functions/read.df.html) для импорта данных по погоде и авиакомпаниям в кадры данных Spark. Это функция отложенного выполнения, как и многие другие методы Spark. Это означает, что они находятся в очереди на выполнение, но выполняются только при необходимости.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Фильтрация и преобразование данных

Далее выполним очистку импортированных данных по авиакомпаниям, чтобы переименовать столбцы. Мы оставим только необходимые переменные и округлим время запланированного отправления до ближайшего часа, чтобы объединить с последними данными по погоде перед вылетом.

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Теперь выполним аналогичные операции с данными по погоде:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Объединение данных о погоде с данными об авиакомпаниях

Теперь воспользуемся функцией SparkR [join()](https://docs.databricks.com/spark/latest/sparkr/functions/join.html), чтобы выполнить левое внешнее соединение данных по погоде с данными по авиакомпаниям на основе идентификатора аэропорта отправления (AirportID) и значения даты и времени (datetime). Внешнее объединение позволяет сохранить все записи данных об авиакомпаниях, даже если они не коррелируют с погодными данными. После соединения удалим некоторые избыточные столбцы и переименуем оставшиеся, чтобы удалить входящий префикс кадра данных, возникший при соединении.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Аналогичным образом объединим данные по погоде и авиакомпаниям на основе идентификатора аэропорта прибытия (AirportID) и значения даты и времени (datetime).

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Сохранение результатов в CSV-файл для обмена со ScaleR

На этом операции соединения с помощью SparkR завершены. Сохраним данные из окончательного кадра данных Spark joinedDF5 в CSV-файл в качестве входных данных для SparkR и завершим сеанс SparkR. Мы явно указываем SparkR сохранить итоговый CSV-файл в 80 отдельных секциях, чтобы обеспечить достаточный параллелизм при обработке в ScaleR.

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Импорт в XDF для использования в ScaleR

Мы могли бы использовать CSV-файл с объединенными данными по авиакомпаниям и погоде "как есть", чтобы создать модель с помощью текстового источника данных ScaleR. Однако вместо этого мы сначала импортируем его в формат XDF, так как он более эффективен при выполнении нескольких операций с набором данных.

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for R Server 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Разделение данных на наборы для обучения и тестирования

Воспользуемся rxDataStep для отделения данных за 2012 год в целях тестирования, оставив остальные данные для обучения.

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Обучение и тестирование модели логистической регрессии

Все готово для создания модели. Чтобы увидеть влияние данных по погоде на задержку времени прибытия, мы используем подпрограмму логистической регрессии ScaleR. С ее помощью мы смоделируем, зависит ли задержка прибытия более чем на 15 минут от погоды в аэропортах отправления и прибытия.

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Теперь давайте посмотрим, как это работает с данными для тестирования, сделав некоторые прогнозы и оценив ROC и AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Альтернативное применение модели

Эту модель можно также использовать для оценки данных на другой платформе путем ее сохранения в файл RDS с последующей передачей и импортом в конечную среду оценки, например в службы SQL Server R. Важно убедиться в том, что уровни фактора данных для оценки соответствуют уровням, на которых основана модель. Этого можно добиться, если извлечь и сохранить сведения столбцов, связанные с данными для моделирования, с помощью функции ScaleR `rxCreateColInfo()`, а затем применить эти сведения к источнику входных данных для прогнозирования. В следующем примере мы сохраним несколько строк из тестового набора данных, а затем извлечем и используем сведения о столбцах из этого примера в скрипте прогнозирования:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Сводка

В этой статье показано, как можно использовать SparkR для работы с данными в сочетании со ScaleR для разработки модели в Hadoop Spark. Этот сценарий требует создания нескольких сеансов Spark, использования одновременно только одного сеанса и обмена данными посредством CSV-файлов. При всей своей простоте этот процесс станет еще проще в предстоящем выпуске R Server, в котором SparkR и ScaleR смогут совместно использовать сеансы и кадры данных Spark.

## <a name="next-steps-and-more-information"></a>Дальнейшие действия и дополнительные сведения

- Дополнительные сведения об использовании R Server в Spark см. в [руководстве по началу работы в библиотеке MSDN](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).

- Общие сведения об R Server см. в статье [Начало работы с Microsoft R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node).

- Сведения об R Server в HDInsight см. в статьях [Основные сведения об R Server и возможностях открытого кода R в HDInsight](hdinsight-hadoop-r-server-overview.md) и [Приступая к работе с R Server в HDInsight](hdinsight-hadoop-r-server-get-started.md).

Дополнительные сведения об использовании SparkR см. в следующих статьях:

- [SparkR (R on Spark)](https://spark.apache.org/docs/2.1.0/sparkr.html) (SparkR (язык R в Spark))

- [SparkR Overview](https://docs.databricks.com/spark/latest/sparkr/overview.html) (Общие сведения о SparkR) на сайте Databricks

