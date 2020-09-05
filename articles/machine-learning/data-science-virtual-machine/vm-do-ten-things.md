---
title: Windows에서 데이터 및 모델 탐색
titleSuffix: Azure Data Science Virtual Machine
description: Windows Data Science Virtual Machine에서 데이터 탐색 및 모델링 작업을 수행 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: dba0f22f1dfece7edd92a80ae1c3a91616a4236c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815932"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Windows Data Science Virtual Machine를 사용 하는 데이터 과학

DSVM (Windows Data Science Virtual Machine)은 데이터 탐색 및 모델링 작업을 수행할 수 있는 강력한 데이터 과학 개발 환경입니다. 환경에는 온-프레미스, 클라우드 또는 하이브리드 배포에 대 한 분석을 쉽게 시작할 수 있도록 하는 널리 사용 되는 여러 데이터 분석 도구가 포함 되어 제공 됩니다. 

DSVM은 Azure 서비스와 긴밀 하 게 연동 됩니다. Azure Synapse (이전의 SQL DW), Azure Data Lake Azure Storage 또는 Azure Cosmos DB Azure에 이미 저장 되어 있는 데이터를 읽고 처리할 수 있습니다. 또한 Azure Machine Learning와 같은 기타 분석 도구를 활용할 수 있습니다.

이 문서에서는 DSVM을 사용 하 여 데이터 과학 작업을 수행 하 고 다른 Azure 서비스와 상호 작용 하는 방법을 알아봅니다. 다음은 DSVM에서 수행할 수 있는 작업 중 일부입니다.

- Jupyter 노트북을 사용 하 여 Python 2, Python 3 및 Microsoft R을 사용 하 여 브라우저에서 데이터를 시험해 보세요. Microsoft R은 성능을 위해 설계 된 R의 엔터프라이즈급 버전입니다.
- Microsoft Machine Learning Server 및 Python을 사용 하 여 DSVM에서 로컬로 데이터를 탐색 하 고 모델을 개발 합니다.
- Azure Portal 또는 PowerShell을 사용 하 여 Azure 리소스를 관리 합니다.
- DSVM에 탑재 가능한 드라이브로 Azure Files 공유를 만들어 저장소 공간을 확장 하 고 전체 팀에서 대규모 데이터 집합/코드를 공유 합니다.
- GitHub를 사용 하 여 팀과 코드를 공유 합니다. 사전 설치 된 Git 클라이언트 (Git Bash 및 Git GUI)를 사용 하 여 리포지토리에 액세스 합니다.
- Azure Blob storage, Azure Cosmos DB, Azure Synapse (이전의 SQL DW) 및 Azure SQL Database와 같은 Azure 데이터 및 분석 서비스에 액세스 합니다.
- DSVM에 미리 설치 된 Power BI Desktop 인스턴스를 사용 하 여 보고서 및 대시보드를 빌드하고 클라우드에 배포 합니다.

- 가상 컴퓨터에 추가 도구를 설치 합니다.   

> [!NOTE]
> 이 문서에 나열 된 많은 데이터 저장소 및 분석 서비스에 대 한 추가 사용 요금은 적용 됩니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/) 페이지를 참조 하세요.
> 
> 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 필요합니다. [무료 평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure Portal에서 Data Science Virtual Machine를 프로 비전 하는 방법에 대 한 지침은 [가상 컴퓨터를 만들](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)때 제공 됩니다.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Jupyter Notebook 사용
Jupyter Notebook는 데이터 탐색 및 모델링을 위한 브라우저 기반 IDE를 제공 합니다. Jupyter 노트북에서 Python 2, Python 3 또는 R (오픈 소스 및 Microsoft R Server 모두)을 사용할 수 있습니다.

Jupyter Notebook를 시작 하려면 **시작** 메뉴 또는 바탕 화면에서 **Jupyter Notebook** 아이콘을 선택 합니다. DSVM 명령 프롬프트에서 ```jupyter notebook``` 기존 전자 필기장이 있거나 새 노트북을 만들 디렉터리에서 명령을 실행할 수도 있습니다.  

Jupyter를 시작한 후 `/notebooks` DSVM에 미리 포장 된 예: 전자 필기장에 대 한 디렉터리로 이동 합니다. 이제 다음을 수행할 수 있습니다.

* 전자 필기장을 선택 하 여 코드를 확인 합니다.
* Shift + Enter를 선택 하 여 각 셀을 실행 합니다.
* **셀**실행을 선택 하 여 전체 노트북을 실행  >  **Run**합니다.
* Jupyter 아이콘 (왼쪽 위 모퉁이)을 선택 하 고 오른쪽에 있는 **새로 만들기** 단추를 선택한 다음 노트북 언어 (커널이 라고도 함)를 선택 하 여 새 노트북을 만듭니다.   

> [!NOTE]
> 현재 Jupyter의 Python 2.7, Python 3.6, R, 줄리아 및 PySpark 커널이 지원 됩니다. R 커널은 오픈 소스 R 및 Microsoft R 모두에서 프로그래밍을 지원 합니다.   
> 
> 

노트북을 사용 하는 경우 데이터를 탐색 하 고, 모델을 작성 하 고, 선택한 라이브러리를 사용 하 여 모델을 테스트할 수 있습니다.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft Machine Learning Server를 사용 하 여 데이터 탐색 및 모델 개발
R 및 Python 같은 언어를 사용하여 DSVM에서 바로 데이터를 분석할 수 있습니다.

R의 경우 시작 메뉴 또는 바탕 화면에서 찾을 수 있는 RStudio와 같은 IDE를 사용할 수 있습니다. 또는 Visual Studio용 R 도구를 사용할 수 있습니다. Microsoft는 확장 가능한 분석을 가능 하 게 하 고 병렬 청크 분할 분석에서 허용 되는 메모리 크기 보다 큰 데이터를 분석 하는 기능을 제공 하기 위해 오픈 소스 CRAN R에 추가 라이브러리를 제공 했습니다. 

Python의 경우 PTVS(Python Tools for Visual Studio) 확장 기능이 사전 설치된 Visual Studio Community Edition 같은 IDE를 사용할 수 있습니다. 3.6 기본적으로 Conda (root environment)는 PTVS에서 구성 됩니다. Anaconda Python 2.7을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. **도구**  >  **python 도구**  >  **python 환경**으로 이동한 다음 Visual Studio Community Edition에서 **+ 사용자 지정** 을 선택 하 여 각 버전에 대 한 사용자 지정 환경을 만듭니다.
1. 설명을 지정 하 고 환경 접두사 경로를 Anaconda Python 2.7에 대 한 **c:\anaconda\envs\python2** 로 설정 합니다.
1. **자동 검색**  >  **적용** 을 선택 하 여 환경을 저장 합니다.

Python 환경을 만드는 방법에 대 한 자세한 내용은 [Ptvs 설명서](https://aka.ms/ptvsdocs) 를 참조 하세요.

이제 새 Python 프로젝트를 만들도록 설정 되었습니다. **파일**  >  **새로 만들기**프로젝트 Python으로 이동 하 여  >  **Project**  >  **Python** 빌드 중인 Python 응용 프로그램의 유형을 선택 합니다. **Python** 환경을 마우스 오른쪽 단추로 클릭 하 고 **python 환경 추가/제거**를 선택 하 여 현재 프로젝트에 대 한 python 환경을 원하는 버전 (python 2.7 또는 3.6)으로 설정할 수 있습니다. PTVS 사용에 대 한 자세한 내용은 [제품 설명서](https://aka.ms/ptvsdocs)에서 확인할 수 있습니다.



## <a name="manage-azure-resources"></a>Azure 리소스 관리
DSVM을 사용 하면 가상 머신에서 로컬로 분석 솔루션을 빌드할 수 없습니다. 또한 Azure 클라우드 플랫폼에서 서비스에 액세스할 수 있습니다. Azure는 DSVM에서 관리 및 액세스할 수 있는 여러 계산, 저장소, 데이터 분석 및 기타 서비스를 제공 합니다.

Azure 구독 및 클라우드 리소스를 관리 하기 위해 다음 두 가지 옵션을 사용할 수 있습니다.
+ 브라우저를 사용 하 여 [Azure Portal](https://portal.azure.com)로 이동 합니다.

+ PowerShell 스크립트를 사용 합니다. 바탕 화면 또는 **시작** 메뉴의 바로 가기에서 Azure PowerShell를 실행 합니다. 자세한 내용은 [Microsoft Azure PowerShell 설명서](../../powershell-azure-resource-manager.md) 를 참조 하세요. 

## <a name="extend-storage-by-using-shared-file-systems"></a>공유 파일 시스템을 사용 하 여 저장소 확장
데이터 과학자는 팀 내에서 대용량 데이터 세트, 코드 또는 기타 리소스를 공유할 수 있습니다. DSVM은 약 45 GB의 공간을 사용할 수 있습니다. 저장소를 확장 하려면 Azure Files을 사용 하 고 하나 이상의 DSVM 인스턴스에 탑재 하거나 REST API를 통해 액세스할 수 있습니다. [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) 를 사용 하거나 [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) 를 사용 하 여 전용 데이터 디스크를 더 추가할 수도 있습니다. 

> [!NOTE]
> Azure Files 공유의 최대 공간은 5TB입니다. 각 파일의 크기 제한은 1TB입니다. 

Azure PowerShell에서이 스크립트를 사용 하 여 Azure Files 공유를 만들 수 있습니다.

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Azure Files 공유를 만들었으므로 이제 Azure의 가상 머신에서 탑재할 수 있습니다. 대기 시간 및 데이터 전송 요금을 방지 하기 위해 VM을 저장소 계정과 동일한 Azure 데이터 센터에 배치 하는 것이 좋습니다. 다음은 DSVM에서 드라이브를 탑재 하는 Azure PowerShell 명령입니다.

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

이제 VM의 일반적인 드라이브처럼 이 드라이브에 액세스할 수 있습니다.

## <a name="share-code-in-github"></a>GitHub에서 코드 공유
GitHub는 개발자 커뮤니티에서 공유 하는 기술을 사용 하 여 다양 한 도구에 대 한 코드 샘플 및 소스를 찾을 수 있는 코드 리포지토리입니다. Github는 코드 파일 버전을 추적하고 저장하는 기술로 Git를 사용합니다. GitHub는 팀의 공유 코드 및 문서를 저장 하 고, 버전 제어를 구현 하 고, 코드를 보고 참여할 수 있는 사용자를 제어 하는 고유한 리포지토리를 만들 수 있는 플랫폼 이기도 합니다. 

Git 사용에 대한 자세한 내용을 보려면 [GitHub 도움말 페이지](https://help.github.com/)를 방문하세요. 팀과 공동 작업 하 고, 커뮤니티에서 개발한 코드를 사용 하 고, 커뮤니티에 코드를 다시 제공 하는 방법 중 하나로 GitHub를 사용할 수 있습니다.

DSVM은 클라이언트 도구와 함께 명령줄 및 GUI에 로드 되어 GitHub 리포지토리에 액세스할 수 있습니다. Git 및 GitHub를 사용 하는 명령줄 도구를 Git Bash 라고 합니다. Visual Studio는 DSVM에 설치 되 고 Git 확장이 있습니다. **시작** 메뉴와 바탕 화면에서 이러한 도구에 대 한 아이콘을 찾을 수 있습니다.

GitHub 리포지토리에서 코드를 다운로드하려면 ```git clone``` 명령을 사용합니다. 예를 들어 Microsoft에서 게시 한 데이터 과학 리포지토리를 현재 디렉터리에 다운로드 하려면 Git Bash에서 다음 명령을 실행할 수 있습니다.

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

Visual Studio에서 동일한 복제 작업을 수행할 수 있습니다. 다음 스크린샷은 Visual Studio에서 Git 및 GitHub 도구에 액세스 하는 방법을 보여 줍니다.

![GitHub 연결이 표시된 Visual Studio 스크린샷](./media/vm-do-ten-things/VSGit.png)

Git을 사용 하 여 github.com에서 사용할 수 있는 리소스에서 GitHub 리포지토리로 작업 하는 방법에 대 한 자세한 내용을 확인할 수 있습니다. [참고 자료](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) 를 보시면 많은 도움이 될 것입니다.

## <a name="access-azure-data-and-analytics-services"></a>Azure 데이터 및 분석 서비스에 액세스
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage는 크고 작은 데이터를 위한 안정적이 고 경제적인 클라우드 저장소 서비스입니다. 이 섹션에서는 Blob 저장소로 데이터를 이동 하 고 Azure blob에 저장 된 데이터에 액세스 하는 방법을 설명 합니다.

#### <a name="prerequisites"></a>사전 요구 사항

* [Azure Portal](https://portal.azure.com)에서 Azure Blob storage 계정을 만듭니다.

   ![Azure Portal 저장소 계정 만들기 프로세스의 스크린샷](./media/vm-do-ten-things/create-azure-blob.png)

* 명령줄 AzCopy 도구가 사전 설치 되어 있는지 확인 ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` 합니다. azcopy.exe를 포함 하는 디렉터리는 PATH 환경 변수에 이미 있으므로이 도구를 실행할 때 전체 명령 경로를 입력 하지 않아도 됩니다. AzCopy 도구에 대 한 자세한 내용은 [AzCopy 설명서](../../storage/common/storage-use-azcopy.md)를 참조 하세요.
* Azure Storage Explorer 도구를 시작합니다. [Storage 탐색기 웹 페이지](https://storageexplorer.com/)에서 다운로드할 수 있습니다. 

   ![저장소 계정에 액세스 하 Azure Storage 탐색기 스크린샷](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>VM에서 Azure blob으로 데이터 이동: AzCopy

로컬 파일과 Blob 저장소 간에 데이터를 이동 하려면 명령줄 또는 PowerShell에서 AzCopy를 사용할 수 있습니다.

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

**C:\myfolder** 는 파일이 저장 된 경로, blob 저장소 계정 이름으로 **mystorageaccount** , 컨테이너 이름이 있는 **mycontainer** , blob 저장소 액세스 키를 사용 하는 **저장소 계정 키** 로 바꿉니다. [Azure Portal](https://portal.azure.com)에서 저장소 계정 자격 증명을 찾을 수 있습니다.

PowerShell 또는 명령 프롬프트에서 AzCopy 명령을 실행 합니다. 다음은 AzCopy 명령을 사용 하는 몇 가지 예입니다.

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

AzCopy 명령을 실행 하 여 Azure blob에 복사 하면 파일이 Azure Storage 탐색기에 표시 됩니다.

![업로드 된 CSV 파일을 표시 하는 저장소 계정의 스크린샷](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>VM에서 Azure blob으로 데이터 이동: Azure Storage 탐색기

Azure Storage 탐색기를 사용 하 여 VM의 로컬 파일에서 데이터를 업로드할 수도 있습니다.

* 컨테이너에 데이터를 업로드 하려면 대상 컨테이너를 선택 하 고 **업로드** 단추를 선택 합니다. ![ Azure Storage 탐색기 업로드 단추의 스크린샷](./media/vm-do-ten-things/storage-accounts.png)
* **파일 상자 오른쪽** 에 있는 줄임표 (**...**)를 선택 하 고 파일 시스템에서 업로드할 파일을 하나 이상 선택한 다음 **업로드** 를 선택 하 여 파일 업로드를 시작 합니다. ![ 파일 업로드 대화 상자의 스크린샷](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure blob에서 데이터 읽기: Python ODBC

BlobService 라이브러리를 사용 하 여 Jupyter 노트북 또는 Python 프로그램의 blob에서 직접 데이터를 읽을 수 있습니다.

먼저 필요한 패키지를 가져옵니다.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

그런 다음 Blob storage 계정 자격 증명을 연결 하 고 blob에서 데이터를 읽습니다.

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

데이터는 데이터 프레임으로 읽습니다.

![데이터의 처음 10개 행의 스크린샷](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure Synapse Analytics (이전의 SQL DW) 및 데이터베이스
Azure Synapse Analytics (이전의 SQL DW)는 엔터프라이즈급 SQL Server 환경을 갖춘 탄력적 데이터 웨어하우스 서비스로 서,

[이 문서의](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)지침에 따라 Azure Synapse Analytics를 프로 비전 할 수 있습니다. SQL data warehouse를 프로 비전 한 후 [이 연습](../team-data-science-process/sqldw-walkthrough.md) 을 사용 하 여 sql data warehouse 내의 데이터를 사용 하 여 데이터 업로드, 탐색 및 모델링을 수행할 수 있습니다.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB는 클라우드의 NoSQL 데이터베이스입니다. JSON과 같은 문서를 사용 하 고 문서를 저장 및 쿼리 하는 데 사용할 수 있습니다.

다음 필수 구성 요소 단계를 사용 하 여 DSVM에서 Azure Cosmos DB에 액세스 합니다.

1. Azure Cosmos DB Python SDK는 DSVM에 이미 설치 되어 있습니다. 업데이트 하려면 ```pip install pydocumentdb --upgrade``` 명령 프롬프트에서를 실행 합니다.
2. [Azure Portal](https://portal.azure.com)에서 Azure Cosmos DB 계정 및 데이터베이스를 만듭니다.
3. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595) 에서 Azure Cosmos DB 데이터 마이그레이션 도구를 다운로드 하 여 원하는 디렉터리에 추출 합니다.
4. 다음 명령 매개 변수를 사용 하 여 [공용 blob](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) 에 저장 된 JSON 데이터 (화산 데이터)를 마이그레이션 도구에 Azure Cosmos DB 가져옵니다. (Azure Cosmos DB 데이터 마이그레이션 도구를 설치한 디렉터리의 dtui.exe를 사용 합니다.) 다음 매개 변수를 사용 하 여 원본 및 대상 위치를 입력 합니다.
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

데이터를 가져온 후 Jupyter로 이동 하 여 *Documentdbsample*이라는 제목의 노트를 열 수 있습니다. Azure Cosmos DB에 액세스 하 고 몇 가지 기본 쿼리를 수행 하는 Python 코드를 포함 합니다. 서비스의 [설명서 페이지](https://docs.microsoft.com/azure/cosmos-db/)를 방문 하 여 Azure Cosmos DB에 대해 자세히 알아볼 수 있습니다.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI 보고서 및 대시보드 사용 
Power BI Desktop의 이전 Azure Cosmos DB 예제에서 화산 JSON 파일을 시각화 하 여 데이터에 대 한 시각적 정보를 얻을 수 있습니다. 자세한 단계는 [Power BI 문서](../../cosmos-db/powerbi-visualize.md)에서 확인할 수 있습니다. 대략적인 단계는 다음과 같습니다.

1. Power BI Desktop을 열고 **Get Data**를 선택합니다. URL을로 지정 `https://cahandson.blob.core.windows.net/samples/volcano.json` 합니다.
2. 목록으로 가져온 JSON 레코드가 표시 됩니다. Power BI 사용할 수 있도록 목록을 테이블로 변환 합니다.
4. 확장 (화살표) 아이콘을 선택 하 여 열을 확장 합니다.
5. 위치가 **레코드** 필드 임을 확인 합니다. 레코드를 확장하고 좌표만 선택합니다. **좌표** 는 목록 열입니다.
6. 목록 좌표 열을 쉼표로 구분 된 **LatLong** 열로 변환 하는 새 열을 추가 합니다. 수식을 사용 하 여 좌표 목록 필드의 두 요소를 연결 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 합니다.
7. **권한 상승** 열을 10 진수로 변환 하 고 **닫기** 및 **적용** 단추를 선택 합니다.

앞의 단계를 수행 하는 대신 다음 코드를 붙여 넣을 수 있습니다. Power BI의 고급 편집기에 사용 되는 단계를 스크립팅 하 여 데이터 변환을 쿼리 언어로 작성 합니다.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

이제 Power BI 데이터 모델에 데이터가 있습니다. Power BI Desktop 인스턴스는 다음과 같이 표시 되어야 합니다.

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

데이터 모델을 사용 하 여 보고서 및 시각화 작성을 시작할 수 있습니다. [이 Power BI 문서의](../../cosmos-db/powerbi-visualize.md#build-the-reports) 단계를 수행 하 여 보고서를 작성할 수 있습니다.

## <a name="scale-the-dsvm-dynamically"></a>동적으로 DSVM 크기 조정 
프로젝트의 요구 사항에 맞게 DSVM을 확장 및 축소할 수 있습니다. 저녁 이나 주말에 VM을 사용할 필요가 없는 경우 [Azure Portal](https://portal.azure.com)에서 vm을 종료할 수 있습니다.

> [!NOTE]
> VM의 운영 체제에 대해 종료 단추만 사용 하는 경우 compute 요금이 발생 합니다. 대신 Azure Portal 또는 Cloud Shell를 사용 하 여 DSVM의 할당을 취소 해야 합니다.
> 
> 

일부 대규모 분석을 처리 하 고 더 많은 CPU, 메모리 또는 디스크 용량을 필요로 할 수 있습니다. 그렇다면 계산 및 예산 요구를 충족 하는 심층 학습, 메모리 용량 및 디스크 유형 (반도체 드라이브 포함)을 위한 GPU 기반 인스턴스와 CPU 코어를 기준으로 VM 크기를 선택할 수 있습니다. [Azure Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/) 책정 페이지에서 매시간 계산 가격과 함께 vm의 전체 목록을 사용할 수 있습니다.


## <a name="add-more-tools"></a>도구 더 추가
DSVM에 미리 작성 된 도구는 많은 일반 데이터 분석 요구 사항을 해결할 수 있습니다. 환경을 하나씩 설치 하 고 구성할 필요가 없기 때문에 시간을 절약할 수 있습니다. 또한 사용 하는 리소스에 대해서만 비용을 지불 하기 때문에 비용을 절감할 수 있습니다.

이 문서에서 프로 파일링 한 다른 Azure 데이터 및 분석 서비스를 사용 하 여 분석 환경을 향상 시킬 수 있습니다. 일부 경우에는 일부 독점 파트너 도구를 비롯 한 추가 도구가 필요할 수 있습니다. 가상 컴퓨터에 대 한 모든 관리 권한을 보유 하 여 필요한 새 도구를 설치할 수 있습니다. 또한 사전 설치되지 않은 추가 패키지를 Python 및 R에 설치할 수 있습니다. Python의 경우 또는 중 하나를 사용할 수 있습니다 ```conda``` ```pip``` . R의 경우 ```install.packages()``` r 콘솔에서을 사용 하거나, IDE를 사용 하 고 **패키지**  >  **설치 패키지**를 선택할 수 있습니다.

## <a name="deep-learning"></a>딥 러닝

프레임 워크 기반 샘플 외에도 DSVM에서 유효성이 확인 된 일련의 포괄적인 연습을 얻을 수 있습니다. 이러한 연습을 통해 이미지 및 텍스트/언어 이해와 같은 도메인에서 심층 학습 응용 프로그램 개발을 바로 시작할 수 있습니다.   


- [여러 프레임 워크에서 신경망 실행](https://github.com/ilkarman/DeepLearningFrameworks):이 연습에서는 프레임 워크 간에 코드를 마이그레이션하는 방법을 보여 줍니다. 또한 프레임 워크 간에 모델 및 런타임 성능을 비교 하는 방법을 보여 줍니다. 

- [이미지 내 제품을 검색하는 엔드투엔드 솔루션을 빌드하는 방법 가이드](https://github.com/Azure/cortana-intelligence-product-detection-from-images): 이미지 검색은 이미지 내의 개체를 찾고 분류할 수 있는 기술입니다. 이 기술은 많은 실제 비즈니스 도메인에서 상당한 보상을 가져올 수 있습니다. 예를 들어 소매업체는 이 기술을 사용하여 고객이 선택한 제품을 확인할 수 있습니다. 또한 이 정보는 매장의 제품 재고를 관리하는 데 도움이 됩니다. 

- [오디오에 대 한 심층 학습](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/):이 자습서에서는 [도시 소리 데이터 집합](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)에서 오디오 이벤트 검색을 위한 심층 학습 모델을 학습 하는 방법을 보여 줍니다. 또한 오디오 데이터로 작업 하는 방법에 대 한 개요를 제공 합니다.

- [텍스트 문서 분류](https://github.com/anargyri/lstm_han):이 연습에서는 두 신경망 아키텍처를 작성 하 고 학습 하는 방법을 보여 줍니다. 이러한 신경망은 Keras API를 딥 러닝에 사용하여 텍스트 문서를 분류합니다. 

## <a name="summary"></a>요약
이 문서에서는 Microsoft Data Science Virtual Machine에서 수행할 수 있는 몇 가지 작업에 대해 설명 했습니다. DSVM을 효과적인 분석 환경으로 만들기 위해 수행할 수 있는 더 많은 작업이 있습니다.

