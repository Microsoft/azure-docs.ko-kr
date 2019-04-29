---
title: Azure HDInsight에서 ScaleR 및 SparkR 사용
description: HDInsight의 ML 서비스에서 ScaleR 및 SparkR 사용
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2017
ms.openlocfilehash: 78ea29b9b37c55a588a44f8d4b69486b89ee2fee
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761354"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>HDInsight에서 ScaleR과 SparkR 결합

이 문서에서는 **ScaleR** 로지스틱 회귀 모델을 사용하여 항공편 도착 지연을 예측하는 방법을 보여 줍니다. 이 예제에서는 **SparkR**을 사용하여 연결되는 항공편 지연과 날씨 데이터를 사용합니다.

두 패키지 모두 Apache Hadoop의 Spark 실행 엔진에서 실행되지만 각각 고유한 각 Spark 세션이 필요하므로 메모리 내 데이터 공유에서 차단됩니다. ML Server의 향후 버전에서 이 문제를 해결할 때까지는 겹치지 않는 Spark 세션을 유지하고 중간 파일을 통해 데이터를 교환하는 것이 해결 방법입니다. 아래 지침은 이러한 요구 사항을 간단하게 달성할 수 있음을 보여줍니다.

이 예제는 Mario Inchiosa와 Roni Burd가 Strata 2016에서 나눈 대화에서 처음 공유되었습니다. 이 대화는 [R을 사용하여 확장 가능한 데이터 과학 플랫폼 빌드](https://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio)에서 찾을 수 있습니다.

이 코드는 원래 Azure HDInsight 클러스터의 Spark에서 실행 중인 ML Server용으로 작성된 것입니다. 하지만 하나의 스크립트에서 SparkR과 ScaleR을 혼합하여 사용하는 개념도 온-프레미스 환경의 컨텍스트에서 유효합니다.

이 문서의 단계에서는 사용자가 R 및 ML Server의 [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) 라이브러리에 대한 중간 수준의 지식을 보유하고 있다고 가정합니다. 이 시나리오를 진행하는 동안 [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html)도 소개됩니다.

## <a name="the-airline-and-weather-datasets"></a>항공사 및 날씨 데이터 세트

항공편 데이터는 [미국 정부 아카이브](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236)에서 사용할 수 있습니다. [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip)에서 zip 파일로도 사용 가능합니다.

날씨 데이터는 [National Oceanic and Atmospheric Administration(미국해양대기관리처) 리포지토리](https://www.ncdc.noaa.gov/orders/qclcd/)에서 월별로 원시 형식의 zip 파일로 다운로드할 수 있습니다. 이 예제의 경우 2007년 5월 – 2012년 12월에 대한 데이터를 다운로드하세요. 시간별 데이터 파일과 각 zip 내에 포함된 `YYYYMMMstation.txt` 파일을 사용하세요. 

## <a name="setting-up-the-spark-environment"></a>Spark 환경 설정

다음 코드를 사용하여 Spark 환경을 설정합니다.

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
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

다음으로, R 패키지에 대한 검색 경로에 `Spark_Home`을 추가합니다. 검색 경로에 추가하면 SparkR를 사용하고 SparkR 세션을 초기화할 수 있습니다.

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

## <a name="preparing-the-weather-data"></a>날씨 데이터 준비

날씨 데이터를 준비하려면 모델링에 필요한 열에 하위 집합으로 넣습니다. 

- "Visibility"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

그런 다음, 기상 관측소와 연결된 공항 코드를 추가하고 측정값을 현지 시간에서 UTC로 변환합니다.

기상 관측소(WBAN) 정보를 공항 코드에 매핑하는 파일을 만들어 시작합니다. 다음 코드는 각 시간별 원시 날씨 데이터 파일을 읽어서 필요한 열에 하위 집합으로 넣고 기상 관측소 매핑 파일을 병합한 다음 측정값의 날씨 시간을 UTC로 조정한 후 파일의 새 버전을 기록합니다.

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

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Spark DataFrame으로 항공사 및 날씨 데이터 가져오기

이제 SparkR [read.df()](https://docs.databricks.com/spark/1.6/sparkr/functions/read.df.html#read-df) 함수를 사용하여 날씨 및 항공사 데이터를 Spark DataFrame으로 가져옵니다. 이 함수는 다른 많은 Spark 메서드와 마찬가지로 느리게 실행됩니다. 즉 실행 큐에 있지만 필요할 때까지는 실행되지 않습니다.

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

## <a name="data-cleansing-and-transformation"></a>데이터 정리 및 변환

다음으로, 열 이름을 변경하기 위해 가져온 항공기 데이터에 대한 일부 정리 작업을 수행합니다. 필요한 변수만을 유지하며, 출발 시 최신 날짜와 병합할 수 있도록 예정된 출발 시간을 가장 가까운 시간으로 받아 내립니다.

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

이제 날씨 데이터에 대해 다음과 비슷한 작업을 수행합니다.

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

## <a name="joining-the-weather-and-airline-data"></a>날씨 및 항공사 데이터 결합

SparkR [join()](https://docs.databricks.com/spark/1.6/sparkr/functions/join.html#join) 함수를 사용하여 출발 AirportID와 날짜/시간을 기준으로 항공사와 날씨 데이터의 왼쪽 외부 조인을 수행합니다. 외부 조인을 사용하면 일치하는 날씨 데이터가 없는 경우에도 모든 항공사 데이터 레코드를 유지할 수 있습니다. 조인에 따라 중복 열 몇 개를 제거하고, 유지 열의 이름을 바꿔 조인에서 도입된 들어오는 DataFrame 접두사를 제거합니다.

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

비슷한 방식으로 도착 AirportID와 날짜/시간을 기준으로 날씨와 항공사 데이터를 조인합니다.

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

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>ScaleR과 교환하기 위해 CSV로 결과 저장

이것으로 SparkR을 사용하는 데 필요한 조인이 완료됩니다. ScaleR에 입력할 수 있도록 최종적인 “joinedDF5” Spark DataFrame의 데이터를 CSV로 저장한 다음 SparkR 세션을 닫습니다. 80개의 개별 파티션에 결과 CSV를 저장하도록 SparkR에 명시적으로 지시하여 ScaleR 처리에서 병렬로 충분하게 처리할 수 있도록 합니다.

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

## <a name="import-to-xdf-for-use-by-scaler"></a>ScaleR에서 사용할 XDF로 가져오기

ScaleR 텍스트 데이터 원본을 통해 모델링하기 위해 조인된 항공사 및 날짜 데이터의 CSV 파일을 그대로 사용합니다. 하지만 데이터 세트에서 여러 작업 실행 시 더 효율적이므로 먼저 XDF로 가져옵니다.

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
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

## <a name="splitting-data-for-training-and-test"></a>학습 및 테스트를 위한 데이터 분할

rxDataStep을 사용하여 테스트를 위해 2012년 데이터를 분할하고, 나머지 데이터는 교육을 위해 그대로 유지합니다.

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

## <a name="train-and-test-a-logistic-regression-model"></a>로지스틱 회귀 모델 학습 및 테스트

이제 모델을 빌드할 준비가 되었습니다. 도착 시간 지연에 날짜 데이터가 미치는 영향을 확인하기 위해 ScaleR의 로지스틱 회귀 루틴을 사용합니다. 이 루틴을 사용하여 15분 이상의 도착 지연이 출도착 공항의 날씨 영향을 받는지 모델링합니다.

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

이제 몇 가지 예측을 수행하고 ROC와 AUC를 살펴봄으로써 테스트 데이터에서 수행하는 방법을 알아보겠습니다.

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

## <a name="scoring-elsewhere"></a>다른 곳에서 점수 매기기

또한, 다른 플랫폼의 데이터에 점수를 매기기 위해 이 모델을 사용할 수도 있습니다. RDS 파일로 저장한 다음 전송하여 해당 RDS를 Microsoft SQL Server R Services 등의 대상 점수 매기기 환경으로 가져옵니다. 모델이 빌드된 요소 수준과 점수를 매길 데이터의 요소 수준이 일치하는지 확인하는 것이 중요합니다. 이러한 일치는 ScaleR의 `rxCreateColInfo()` 함수를 통해 모델링 데이터와 관련된 열 정보를 추출하고 저장한 다음, 해당 열 정보를 입력 데이터 원본에 적용하여 예측함으로써 구현할 수 있습니다. 다음 예제에서는 테스트 데이터 세트의 몇 개 행만 저장하고 예측 스크립트에서 이 샘플의 열 정보를 추출하여 사용합니다.

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

## <a name="summary"></a>요약

이 문서에서는 Hadoop Spark에서 모델 개발을 위해 ScaleR과 데이터 조작을 위해 SparkR의 사용을 결합하는 것이 어떻게 가능한지 보여주었습니다. 이 시나리오에서는 한 번에 하나의 세션만 실행하면서 별도의 Spark 세션을 유지하고 CSV 파일을 통해 데이터를 교환해야 합니다. SparkR과 ScaleR에서 Spark 세션을 공유하고 이에 따라 Spark DataFrame을 공유할 수 있는 경우 이 프로세스가 간단하지만 예정된 ML 서비스 릴리스에서 훨씬 더 쉽게 수행될 것입니다.

## <a name="next-steps-and-more-information"></a>다음 단계 및 자세한 정보

- Apache Spark에서 ML Server 사용에 대한 자세한 내용은 [시작 가이드](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)를 참조하세요.

- ML Server에 대한 일반 정보는 [R 시작](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node)(영문)을 참조하세요.

- HDInsight의 ML 서비스에 대한 자세한 내용은 [HDInsight의 ML 서비스 개요](r-server/r-server-overview.md) 및 [Azure HDInsight에서 ML 서비스 시작](r-server/r-server-get-started.md)을 참조하세요.

SparkR 사용에 대한 자세한 내용은 다음을 참조하세요.

- [Apache SparkR 문서](https://spark.apache.org/docs/2.1.0/sparkr.html)

- Databricks의 [SparkR 개요](https://docs.databricks.com/spark/latest/sparkr/overview.html)(영문)
