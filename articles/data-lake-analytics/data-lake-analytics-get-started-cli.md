---
title: Azure CLI를 사용하여 Azure Data Lake Analytics 시작
description: Azure 명령줄 인터페이스를 사용하여 Azure Data Lake Analytics 계정을 만들고 U-SQL 작업을 제출하는 방법을 알아봅니다.
ms.service: data-lake-analytics
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 9d9d5a7232529989901709013dcfac12f94afad0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812754"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Azure CLI를 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

이 문서에서는 Azure CLI 명령줄 인터페이스를 사용하여 Azure Data Lake Analytics 계정을 만들고, USQL 작업 및 카탈로그를 제출하는 방법을 설명합니다. 작업은 TSV(탭 분리 값) 파일을 읽고 CSV(쉼표로 구분된 값) 파일로 변환합니다. 

## <a name="prerequisites"></a>필수 조건
이 작업을 시작하려면 다음 항목이 필요합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 이 문서에서는 Azure CLI 버전 2.0 이상을 실행해야 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 



## <a name="log-in-to-azure"></a>Azure에 로그인

Azure 구독에 로그인하려면

```
azurecli
az login
```

URL로 이동하여 인증 코드를 입력해야 합니다.  그런 다음 소개에 따라 자격 증명을 입력합니다.

로그인되면 로그인 명령에 구독이 나열됩니다.

특정 구독을 사용하려면

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>데이터 레이크 분석 계정 만들기
모든 작업을 실행하기 전에 Data Lake Analytics 계정이 있어야 합니다. Data Lake Analytics 계정을 만들려면 다음 항목을 지정해야 합니다.

* **Azure 리소스 그룹**. Azure 리소스 그룹 내에서 Data Lake Analytics 계정을 만들어야 합니다. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 사용하면 그룹으로 애플리케이션에서 리소스와 함께 사용할 수 있습니다. 애플리케이션에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.  

구독 중인 기존 리소스 그룹을 나열하려면

```
az group list
```

새 리소스 그룹을 만들려면 다음을 수행합니다.

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Data Lake Analytics 계정 이름**. Data Lake Analytics 계정마다 이름이 있습니다.
* **위치** - Data Lake Analytics를 지원하는 Azure 데이터 센터 중 하나를 사용합니다.
* **기본 Data Lake Store 계정**: 모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다.

기존Data Lake Store 계정을 나열하려면

```
az dls account list
```

새 Data Lake Store 계정을 만들려면

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

다음 구문을 사용하여 Data Lake Analytics 계정을 만듭니다.

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

계정을 만든 후 다음 명령을 사용하여 계정을 나열하고 계정 정보를 보여 줍니다.

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>데이터 레이크 저장소에 데이터 업로드
이 자습서에서는 몇 가지 검색 로그를 처리합니다.  검색 로그는 Data Lake Storage 또는 Azure Blob Storage에 저장할 수 있습니다.

Azure Portal은 검색 로그 파일을 포함하는 기본 Data Lake Store 계정에 샘플 데이터 파일을 복사하는 사용자 인터페이스를 제공합니다. 기본 데이터 레이크 저장소 계정에 데이터를 업로드하려면 [원본 데이터 준비](data-lake-analytics-get-started-portal.md) 를 참조하세요.

Azure CLI를 사용하여 파일을 업로드하려면 다음 명령을 사용합니다.

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

데이터 레이크 분석은 Azure Blob Storage에 액세스할 수도 있습니다.  Azure Blob Storage에 데이터를 업로드하려면 [Azure Storage와 Azure CLI 사용](../storage/common/storage-azure-cli.md)을 참조하세요.

## <a name="submit-data-lake-analytics-jobs"></a>데이터 레이크 분석 작업 제출
데이터 레이크 분석 작업은 U-SQL 언어로 작성됩니다. U-SQL에 대한 자세한 내용은 [U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md) 및 [U-SQL 언어 참조](https://go.microsoft.com/fwlink/?LinkId=691348)를 참조하세요.

**데이터 레이크 분석 작업 스크립트를 만들려면**

다음 U-SQL 스크립트를 사용하여 텍스트 파일을 만들고 사용자의 워크스테이션에 텍스트 파일을 저장합니다.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

이 U-SQL 스크립트는 **Extractors.Tsv()** 를 사용하여 원본 데이터 파일을 읽은 다음 **Outputters.Csv()** 를 사용하여 csv 파일을 만듭니다.

원본 파일을 다른 위치에 복사하지 않는 한 두 경로를 수정하지 마세요.  출력 폴더가 없는 경우 Data Lake Analytics에서 해당 폴더를 만듭니다.

기본 Data Lake Store 계정에 저장된 파일의 상대 경로를 사용하는 것이 더 쉽습니다. 절대 경로를 사용할 수도 있습니다.  예를 들면 다음과 같습니다.

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

연결된 Storage 계정의 파일에 액세스하려면 절대 경로를 사용해야 합니다.  연결된 Azure Storage 계정에 저장된 파일에 대한 구문은 다음과 같습니다.

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> 공용 Blob이 있는 Azure Blob 컨테이너는 지원되지 않습니다.      
> 공용 컨테이너가 있는 Azure Blob 컨테이너는 지원되지 않습니다.      
>

**작업을 제출하려면**

다음 구문을 사용하여 작업을 제출합니다.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

예를 들면 다음과 같습니다.

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**작업을 나열하고 작업 정보를 표시하려면**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**작업을 취소하려면**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>작업 결과 검색

작업이 완료된 후 다음 명령을 사용하여 출력 파일을 나열하고 파일을 다운로드할 수 있습니다.

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

예를 들면 다음과 같습니다.

```
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>다음 단계

* Data Lake Analytics Azure CLI 참조 문서를 보려면 [Data Lake Analytics](/cli/azure/dla)를 참조하세요.
* Data Lake Store Azure CLI 참조 문서를 보려면 [Data Lake Store](/cli/azure/dls)를 참조하세요.
* 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
