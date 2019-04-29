---
title: Python을 사용하여 Azure Data Lake Analytics 관리
description: 이 문서에서는 Python을 사용하여 Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 82007c780a0c9ff3bb2e1a50a4826499f9df9c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811715"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Python을 사용하여 Azure Data Lake Analytics 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

이 문서에서는 Python을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.

## <a name="supported-python-versions"></a>지원되는 Python 버전

* Python의 64비트 버전을 사용합니다.
* **[Python.org 다운로드](https://www.python.org/downloads/)** 에서 찾을 수 있는 표준 Python 배포를 사용할 수 있습니다. 
* 대부분의 개발자는 **[Anaconda Python 배포](https://www.anaconda.com/download/)** 사용을 편리하게 여깁니다.  
* 이 문서는 표준 Python 배포의 Python 버전 3.6을 사용하여 작성되었습니다.

## <a name="install-azure-python-sdk"></a>Azure Python SDK 설치

다음 모듈을 설치합니다.

* **azure-mgmt-resource** 모듈에는 Active Directory에 대한 다른 Azure 모듈이 포함됩니다.
* **azure-datalake-store** 모듈에는 Azure Data Lake Store 파일 시스템 작업이 포함됩니다. 
* **azure-mgmt-datalake-store** 모듈에는 Azure Data Lake Store 계정 관리 작업이 포함됩니다.
* **azure-mgmt-datalake-analytics** 모듈에는 Azure Data Lake Analytics 작업이 포함됩니다. 

먼저 다음 명령을 실행하여 최신 `pip`가 있는지 확인합니다.

```
python -m pip install --upgrade pip
```

이 문서는 `pip version 9.0.1`을 사용하여 작성되었습니다.

다음 `pip` 명령을 사용하여 명령줄에서 모듈을 설치합니다.

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>새 Python 스크립트 만들기

다음 코드를 스크립트에 붙여 넣습니다.

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

이 스크립트를 실행하여 모듈을 가져올 수 있는지 확인합니다.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>팝업 항목으로 대화형 사용자 인증

이 메서드는 지원되지 않습니다.

### <a name="interactive-user-authentication-with-a-device-code"></a>디바이스 코드로 대화형 사용자 인증

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>SPI 및 암호로 비대화형 인증

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>API 및 인증서로 비대화형 인증

이 메서드는 지원되지 않습니다.

## <a name="common-script-variables"></a>일반 스크립트 변수

이러한 변수는 샘플에서 사용됩니다.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>클라이언트 만들기

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>데이터 레이크 분석 계정 만들기

먼저 저장소 계정을 만듭니다.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
그런 다음 해당 저장소를 사용하는 ADLA 계정을 만듭니다.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>작업 제출

```python
script = """
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
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>작업이 종료될 때까지 대기

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>파이프라인 및 되풀이 나열
작업에 파이프라인 또는 되풀이 메타데이터가 첨부되어 있는지에 따라, 파이프라인 및 되풀이를 나열할 수 있습니다.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>계산 정책 관리

DataLakeAnalyticsAccountManagementClient 개체는 Data Lake Analytics 계정에 대한 계산 정책을 관리하는 메서드를 제공합니다.

### <a name="list-compute-policies"></a>계산 정책 나열

다음 코드는 Data Lake Analytics 계정에 대한 계산 정책 목록을 검색합니다.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>새 계산 정책 만들기

다음 코드는 지정된 사용자가 사용할 수 있는 최대 AU를 50으로, 최소 작업 우선 순위를 250으로 설정하는, Data Lake Analytics 계정에 대한 새 계산 정책을 만듭니다.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>다음 단계

- 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
- U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
- 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.

