---
title: Azure Synapse Analytics에서 Apache Spark에 대 한 라이브러리 관리
description: Azure Synapse Analytics에서 Apache Spark에 사용 되는 라이브러리를 추가 하 고 관리 하는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.subservice: spark
ms.openlocfilehash: b7aea6565e8301e2aeb96263b8e7b1d2ea64995d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589563"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Apache Spark에 대 한 라이브러리 관리

라이브러리는 프로그램 또는 프로젝트에 포함 시킬 수 있는 재사용 가능한 코드를 제공 합니다. 응용 프로그램에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 Spark 풀 (미리 보기) 중 하나에 라이브러리를 설치 하면 됩니다. Spark 풀에 대해 라이브러리를 설치한 후에는 동일한 풀을 사용 하는 모든 세션에 대해 사용할 수 있습니다. 

## <a name="default-installation"></a>기본 설치
Azure Synapse Analytics의 Apache Spark에는 완전 한 Anacondas 설치와 추가 라이브러리가 있습니다. 전체 라이브러리 목록은 [Apache Spark 버전 지원](apache-spark-version-support.md)에서 찾을 수 있습니다. 

Spark 인스턴스가 시작 되 면 이러한 라이브러리가 자동으로 포함 됩니다. 추가 Python 및 사용자 지정 빌드 패키지는 Spark 풀 (미리 보기) 수준에서 추가할 수 있습니다.


## <a name="manage-python-packages"></a>Python 패키지 관리
Spark 응용 프로그램에 사용할 라이브러리를 확인 한 후에는 Spark 풀 (미리 보기)에 설치할 수 있습니다. 

 *requirements.txt* 파일 (명령의 출력)을 `pip freeze` 사용 하 여 가상 환경을 업그레이드할 수 있습니다. 설치 또는 업그레이드를 위해이 파일에 나열 된 패키지는 풀을 시작할 때 PyPi에서 다운로드 됩니다. 이 요구 사항 파일은 spark 인스턴스를 Spark 풀에서 만들 때마다 사용 됩니다.

> [!IMPORTANT]
> - 설치 하는 패키지가 크거나 설치 하는 데 시간이 오래 걸리는 경우 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - 설치 시 GCC와 같이 컴파일러 지원이 필요한 패키지는 지원 되지 않습니다.
> - 패키지를 다운 그레이드할 수 없습니다. 추가 하거나 업그레이드할 수 있습니다.

### <a name="requirements-format"></a>요구 사항 형식

다음 코드 조각에서는 요구 사항 파일의 형식을 보여 줍니다. PyPi 패키지 이름은 정확한 버전과 함께 나열 됩니다. 이 파일은 [pip 고정](https://pip.pypa.io/en/stable/reference/pip_freeze/) 참조 설명서에 설명 된 형식을 따릅니다. 이 예제에서는 특정 버전을 고정 합니다. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Python 패키지 설치
Spark 응용 프로그램을 개발할 때 기존 라이브러리를 업데이트 하거나 새 라이브러리를 설치 해야 하는 경우가 있습니다. 풀을 만들 때 또는 이후에 라이브러리를 업데이트할 수 있습니다.

#### <a name="install-packages-during-pool-creation"></a>풀을 만드는 동안 패키지 설치
풀을 만드는 동안 Spark 풀 (미리 보기)에 라이브러리를 설치 하려면 다음을 수행 합니다.
   
1. Azure Portal에서 Azure Synapse Analytics 작업 영역으로 이동 합니다.
   
2. **Apache Spark 풀 만들기** 를 선택 하 고 **추가 설정** 탭을 선택 합니다. 
   
3. 페이지의 **패키지** 섹션에서 파일 선택기를 사용 하 여 환경 구성 파일을 업로드 합니다. 
   
![풀을 만드는 동안 Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Python 라이브러리 추가")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Synapse 작업 영역에서 패키지 설치
Azure Synapse Analytics 포털에서 Spark 풀 (미리 보기)에 라이브러리를 추가 하거나 업데이트 하려면 다음을 수행 합니다.

1.  Azure Portal에서 Azure Synapse Analytics 작업 영역으로 이동 합니다.
   
2.  Azure Portal에서 Azure Synapse Analytics 작업 영역을 시작 합니다.

3.  기본 탐색 패널에서 **관리** 를 선택한 다음 **Apache Spark 풀**을 선택 합니다.
   
4. 단일 Spark 풀을 선택 하 고 페이지의  **패키지** 섹션에서 파일 선택기를 사용 하 여 환경 구성 파일을 업로드 합니다.

![Synapse에서 Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "Python 라이브러리 추가")
   
#### <a name="install-packages-from-the-azure-portal"></a>Azure Portal에서 패키지 설치
Azure Portal에서 직접 Spark 풀 (미리 보기)에 라이브러리를 설치 하려면 다음을 수행 합니다.
   
 1. Azure Portal에서 Azure Synapse Analytics 작업 영역으로 이동 합니다.
   
 2. **Synapse 리소스** 섹션 아래에서 **Apache Spark 풀** 탭을 선택 하 고 목록에서 Spark 풀을 선택 합니다.
   
 3. Spark 풀의 **설정** 섹션에서 **패키지** 를 선택 합니다. 

 4. 파일 선택기를 사용 하 여 환경 구성 파일을 업로드 합니다.

![Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python 라이브러리 추가")

### <a name="verify-installed-libraries"></a>설치 된 라이브러리 확인

올바른 라이브러리의 올바른 버전이 설치 되어 있는지 확인 하려면 다음 코드를 실행 합니다.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>Python 패키지 업데이트
세션 간에 언제 든 지 패키지를 추가 하거나 수정할 수 있습니다. 새 패키지 구성 파일을 업로드 하면 기존 패키지 및 버전을 덮어씁니다.  

라이브러리를 업데이트 하거나 제거 하려면:
1. Azure Synapse Analytics 작업 영역으로 이동 합니다. 

2. Azure Portal 또는 Azure Synapse 작업 영역을 사용 하 여 업데이트할 **Apache Spark 풀** 을 선택 합니다.

3. **패키지** 섹션으로 이동 하 여 새 환경 구성 파일을 업로드 합니다.
   
4. 변경 내용을 저장 한 후에는 활성 세션을 종료 하 고 풀을 다시 시작 하도록 해야 합니다. 필요에 따라 **새 설정을 강제 적용**하는 확인란을 선택 하 여 활성 세션을 강제로 종료할 수 있습니다.

![Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python 라이브러리 추가")
   

> [!IMPORTANT]
> **새 설정을 강제 적용**하는 옵션을 선택 하면 선택한 Spark 풀의 모든 현재 세션이 종료 됩니다. 세션이 종료 되 면 풀이 다시 시작 될 때까지 기다려야 합니다. 
>
> 이 설정을 선택 하지 않으면 현재 Spark 세션이 종료 될 때까지 기다리거나 수동으로 중지 해야 합니다. 세션이 종료 되 면 풀을 다시 시작 하도록 해야 합니다. 


## <a name="manage-a-python-wheel"></a>Python 휠 관리

### <a name="install-a-custom-wheel-file"></a>사용자 지정 휠 파일 설치
모든 휠 파일을 Synapse 작업 영역에 연결 된 Azure Data Lake Storage (Gen2) 계정에 업로드 하 여 Apache Spark 풀에 사용자 지정 기본 휠 패키지를 설치할 수 있습니다. 

저장소 계정의 기본 컨테이너에 있는 다음 경로에 파일을 업로드 해야 합니다. 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>세션 간에 사용자 지정 패키지를 추가 하거나 수정할 수 있습니다. 그러나 업데이트 된 패키지를 보려면 풀과 세션이 다시 시작 될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
