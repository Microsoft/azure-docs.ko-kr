---
title: 엔터프라이즈 보안
titleSuffix: Azure Machine Learning service
description: 'Azure Machine Learning 서비스를 사용 하 여 안전 하 게: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링 합니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821422"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스에 대 한 엔터프라이즈 보안

이 문서에서는 Azure Machine learning 서비스를 사용 하 여 사용할 수 있는 보안 기능에 대 한 알아봅니다 하는 것이 됩니다.

클라우드 서비스를 사용 하는 경우 것이 필요한 사용자 에게만 액세스를 제한 하는 것이 좋습니다. 이 서비스에서 사용 되는 인증 및 권한 부여 모델을 이해 하 여 시작 합니다. 네트워크 액세스를 제한 해야 할 수도 안전 하 게 클라우드에 있는 온-프레미스 네트워크에 리소스를 연결 합니다. 데이터 암호화, 중요 한 미사용와 서비스 간에 데이터를 이동 하는 동안 이기도 합니다. 마지막으로 서비스를 모니터링 하 고 모든 작업의 감사 로그를 생성 하는 일을 할 수 해야 합니다.

## <a name="authentication"></a>Authentication
다단계 인증에는 동일한 Azure Active Directory (Azure AD)가 구성 하는 경우 지원 됩니다.
* 클라이언트는 Azure AD에 로그인 하 고 Azure Resource Manager 토큰을 가져옵니다.  사용자 및 서비스 주체 완전히 지원 됩니다.
* 클라이언트 토큰에서 Azure Resource Manager 및 모든 Azure Machine Learning 서비스를 표시합니다.
* Azure Machine Learning 서비스는 사용자는 컴퓨터에 Azure Machine Learning 토큰을 제공합니다. 예를 들어, Machine Learning Compute 됩니다. 토큰은 사용자가 사용 됩니다.이 Azure Machine Learning 실행 한 후 Azure Machine Learning 서비스 (작업 영역에는 범위 제한)를 다시 호출 하는 전체 계산 합니다.

![Azure Machine Learning 서비스에서 인증이 작동 하는 방법을 보여 주는 스크린샷](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>웹 서비스 배포에 대 한 인증 키

배포에 대해 인증을 사용하도록 설정하면 자동으로 인증 키를 만듭니다.

* Azure Kubernetes Service에 배포할 때 인증은 기본적으로 활성화되어 있습니다.
* Azure 컨테이너 인스턴스에 배포할 때 인증은 기본적으로 비활성화되어 있습니다.

인증을 제어하려면 배포를 만들거나 업데이트할 때 `auth_enabled` 매개 변수를 사용합니다.

인증을 사용하도록 설정한 경우 `get_keys` 메서드를 사용하여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 사용 하 여 키를 다시 생성 해야 하는 경우 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>권한 부여

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유하는 경우 사용자에게 다음 역할을 할당하여 작업 영역에 대한 액세스를 제어할 수 있습니다.
* 소유자
* 참가자
* 판독기
    
다음 표에서 주요 Azure Machine Learning 서비스 작업 및 수행 하는 역할의 일부를 나열 합니다.

| Azure Machine Learning 서비스 작업 | 소유자 | 참가자 | 판독기 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 계산 만들기 | ✓ | ✓ | |
| 계산 연결 | ✓ | ✓ | |
| 데이터 저장소를 연결 합니다. | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 뷰 모델/이미지 | ✓ | ✓ | ✓ |
| 웹 서비스 호출 | ✓ | ✓ | ✓ |

기본 제공 역할이 요구 사항에 맞게 충분 하지 않은 경우에 사용자 지정 역할을 만들 수 있습니다. 작업 영역 및 Machine Learning Compute에 대 한 작업에 대 한 지원만 사용자 지정 역할에는 참고 합니다. 사용자 지정 역할 있을 읽기, 쓰기 또는 작업 영역 및 해당 작업 영역에서 계산 리소스에 대 한 권한을 삭제 합니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준에서 또는 특정 구독 수준에서 역할을 사용할 수 있습니다. 자세한 내용은 참조 하세요. [는 Azure Machine Learning 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>계산 및 데이터 보호
소유자 및 참가자는 작업 영역에 연결 된 모든 계산 대상 및 데이터 저장소를 사용할 수 있습니다.  
각 작업 영역의 작업 영역에서 사용 되는 연결 된 리소스에 대 한 다음 권한을 사용 하 여 관련된 시스템에서 지정한 관리 되는 Id (이름의 동일한 작업 영역)에 있습니다.

관리 되는 id에 대 한 자세한 내용은 참조 하세요. [Azure 리소스에 대 한 id 관리](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| 리소스 | 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 | 
| Storage 계정 | Storage Blob 데이터 Contributor | 
| Key Vault | 모든 키, 비밀, 인증서에 대 한 액세스 | 
| Azure 컨테이너 레지스트리 | 참가자 | 
| 작업 영역을 포함 하는 리소스 그룹 | 참가자 | 
| (다른 작업 영역에 포함 된) 경우 Key Vault를 포함 하는 리소스 그룹 | 참가자 | 

관리자가 관리 되는 위에 언급 된 리소스 id에 대 한 액세스를 해지 하지 않습니다 하는 것이 좋습니다. 액세스 키 다시 동기화 작업을 사용 하 여 복원할 수 있습니다.

Azure Machine Learning 서비스는 모든 작업 영역 지역에 대 한 구독의 참가자 수준 액세스를 사용 하 여 추가 응용 프로그램 (aml-를 사용 하 여 이름 시작)을 만듭니다. 에 대 한 예입니다. 미국 동부에서 작업 영역 및 북유럽에서 다른 작업 영역 동일한 구독에 있는 경우 이러한 두 응용 프로그램 표시 됩니다. 계산 리소스를 Azure Machine Learning 서비스 관리할 수 있도록이 필요 합니다.


## <a name="network-security"></a>네트워크 보안

Azure Machine Learning Service는 다른 Azure 서비스를 통해 컴퓨팅 리소스를 얻습니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 이러한 컴퓨팅 대상을 가상 네트워크 내에서 만들 수 있습니다. 예를 들어 Microsoft Data Science Virtual Machine을 사용하여 모델을 학습시킨 다음, AKS(Azure Kubernetes Service)에 모델을 배포할 수 있습니다.  

자세한 내용은 [가상 네트워크에서 실험 및 추론을 실행 하는 방법을](how-to-enable-virtual-network.md)합니다.

## <a name="data-encryption"></a>데이터 암호화.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화
#### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Machine Learning 서비스는 Azure Machine Learning 서비스 작업 영역에 연결 되 고 사용자의 구독에 상주 하는 Azure Blob Storage 계정에 스냅숏, 출력 및 로그를 저장 합니다. Azure Blob Storage에 저장 된 모든 데이터는 Microsoft-Managed 키를 사용 하 여 미사용 암호화 됩니다.

Azure Blob Storage에 저장 된 데이터에 대 한 고유한 키를 가져오는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Key Vault에서 고객 관리 키를 사용 하 여 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)합니다.

학습 데이터가 일반적으로 저장 됩니다 Azure Blob storage에서 교육 계산에 액세스할 수 있도록. 이 저장소는 Azure Machine Learning을 통해 관리 되는 없지만 원격 파일 시스템으로 계산에 탑재 합니다.

#### <a name="cosmos-db"></a>Cosmos DB
Azure Machine Learning 서비스는 Azure Machine Learning 서비스에서 관리 하는 Microsoft 구독에 메트릭 및 존재 하는 Cosmos DB에 대 한 메타 데이터를 저장 합니다. Cosmos DB에 저장 된 모든 데이터는 Microsoft 관리 키를 사용 하 여 미사용 암호화 됩니다.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
모든 컨테이너 레지스트리에 있는 이미지에 ACR ()는 미사용 시 암호화 됩니다. Azure는 자동으로 저장 하기 전에 이미지를 암호화 하 고 Azure Machine Learning 서비스는 이미지를 가져오고 때 즉석에서을 해독 합니다.

#### <a name="machine-learning-compute"></a>Machine Learning 컴퓨팅
OS 디스크를 Azure Storage에 저장 된 각 계산 노드에 대 한 암호화 됩니다 Microsoft 관리 키를 사용 하 여 Azure Machine Learning 서비스 저장소 계정에서. 이 계산 후 삭제 되며 클러스터 일반적으로 축소 하는 경우 실행이 없습니다. 큐에 대기 합니다. 기본 가상 컴퓨터 프로 비전이 해제 되며 OS 디스크를 삭제 합니다. Azure 디스크 암호화는 OS 디스크에 지원 되지 않습니다.
각 가상 컴퓨터 OS 작업에 대 한 임시 로컬 디스크가 있습니다. 이 디스크 단계 학습 데이터를 필요에 따라 수 있습니다. 이 디스크를 암호화 되지 않습니다. Azure에서 미사용 암호화 작동 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure 데이터 암호화 미사용](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)합니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
다양 한 마이크로 서비스를 Azure Machine Learning 및 점수 매기기 끝점을 호출 하는 외부 통신 간의 내부 통신 모두 SSL을 사용 하 여 지원 됩니다. Azure Storage에 대 한 모든 액세스 보안 채널을 통해 이기도합니다. 자세한 내용은 [SSL을 사용 하 여 보안 Azure Machine Learning 웹 서비스](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)합니다.

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용
작업 영역과 연결 된 Key Vault 인스턴스는 다양 한 종류의 자격 증명을 저장 하려면 Azure Machine Learning 서비스에서 사용 됩니다.
* 연결 된 저장소 계정 연결 문자열
* Azure Container Repository 인스턴스로 암호
* 저장소 데이터에 연결 문자열입니다. 

SSH 암호 및 키 VM 및 HDI HDInsight와 같은 대상을 계산 하기 위해 Microsoft 구독과 연결 된 별도 Key Vault에 저장 됩니다. Azure Machine Learning 서비스는 모든 암호를 저장 하거나 키 생성, 인증 되 면 하 고 실험을 실행 하려면 VM/HDInsight에 연결 하기 위해 자체 SSH 키를 저장 합니다. 대신 사용자가 제공 합니다. 각 작업 영역에 관련된 된 시스템 할당 관리 Id (동일한 이름의 작업 영역으로) 모든 키, 비밀 및 인증서에 대 한 액세스를 Key Vault에 있는.

 
## <a name="monitoring"></a>모니터링

사용자 작업 영역에서 수행 하는 다양 한 작업 보고와 같은 작업 이름, 이벤트를 시작한 사람, 타임 스탬프 등 기본 정보를 보려면 작업 영역에서 활동 로그를 볼 수 있습니다.

다음 스크린샷에서 작업 영역에 대 한 활동 로그를 보여 줍니다.

![작업 영역에서 보여 주는 활동 로그 스크린샷](./media/enterprise-readiness/workspace-activity-log.png)


채 점 요청 세부 정보는 작업 영역을 만드는 동안 사용자의 구독에서 만들어진 appinsights로 저장 됩니다. HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, 기간 등 필드가 포함 됩니다.


## <a name="data-flow-diagram"></a>데이터 흐름 다이어그램

### <a name="create-workspace"></a>작업 영역 만들기
다음 다이어그램에서는 만들기 작업 영역의 워크플로를 보여 줍니다.
사용자가 지원 되는 Azure Machine Learning 서비스 클라이언트 (Python SDK, CLI, Azure portal) 중 하나에서 Azure AD에 로그인 하 고 적절 한 Azure Resource Manager 토큰을 요청 합니다.  사용자는 다음 영역을 Azure Resource Manager를 호출 합니다.  Azure Resource Manager 연락처를 Azure Machine Learning 작업 영역을 프로 비전 할 리소스 공급자 서비스입니다.  추가 리소스는 작업 영역을 만드는 동안 고객 구독에서 생성 됩니다.
* KeyVault (암호 저장)를
* Azure Storage 계정 Blob 및 파일 공유 등
* Azure Container Registry (추론 및 실험에 대 한 docker 이미지를 저장)
* Application Insights (원격 분석을 저장)를

작업 영역 (Azure Kubernetes Service, VM 등)에 연결 된 다른 계산도 프로 비전 할 수는 고객이 필요에 따라 합니다. 

![작업 영역의 워크플로 만드는 보여 주는 스크린샷](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>소스 코드 (학습 스크립트)를 저장 합니다.
다음 다이어그램은 코드 스냅숏 워크플로를 보여줍니다.
Azure Machine Learning과 사용 하 여 연결 된 서비스 작업 영역 이며 디렉터리 (실험) 소스 코드 (학습 스크립트)를 포함 하는  이러한 고객의 로컬 컴퓨터 및 클라우드 (고객의 구독에서 Azure Blob Storage)에 저장 됩니다. 이러한 코드 스냅숏 기록 감사에 대 한 검사 또는 실행을 위해 사용 됩니다.

![작업 영역의 워크플로 만드는 보여 주는 스크린샷](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>교육
다음 다이어그램에서는 학습 워크플로를 보여 줍니다.
* Azure Machine Learning 서비스는 위에서 저장 코드 스냅숏에 대 한 스냅숏 ID를 사용 하 여 호출 됩니다.
* ID (선택 사항) 및 Azure Machine Learning 서비스와 다시 통신할 계산 대상은 Machine Learning 계산/v M 등에서 나중에 사용 되는 Azure Machine Learning 서비스 토큰 서비스를 만들고 azure Machine Learning 실행
* 관리 되는 계산 (예: 선택할 수 있습니다. Machine Learning Compute) 또는 관리 되지 않는 계산 (예: VM) 교육 작업을 실행 합니다. 데이터 흐름 모두 아래 시나리오에 대 한 설명:
* (VM/HDInsight/로컬 – Key Vault에 SSH 자격 증명을 사용 하 여 Microsoft 구독에 액세스) Azure Machine Learning 서비스에서 실행 됩니다 관리 코드 계산 대상입니다.
    1.  환경 준비 (참고: Docker는 VM/로컬에 대 한 옵션을 함께 합니다. 컴퓨터 학습 Compute 아래 docker 컨테이너의 작동 방법을 실행 중인 실험을 이해 하는 단계를 참조)
    2.  코드 다운로드
    3.  환경 변수/구성 설정
    4.  사용자 스크립트 (위에서 언급 한 코드 스냅숏)를 실행 합니다.
* (Machine Learning Compute – 관리 되는 작업 영역 id 사용 하 여 액세스) Machine Learning Compute 이므로 관리 되는 계산, Microsoft에서 관리 되는 Microsoft 구독에서 실행 되는 결과적으로 note 합니다.
    1.  원격 Docker 생성이 필요한 경우 시작
    2.  Azure 파일 공유 사용자에 게 관리 코드를 작성합니다.
    3.  초기를 사용 하 여 컨테이너 시작 명령 즉, 위의 단계에서 관리 코드


#### <a name="querying-runs--metrics"></a>쿼리 실행 및 메트릭
이 단계는 교육 쓰기를 계산 하는 여기서 흐름에 표시 됩니다는 *실행 메트릭* 가져옵니다 Cosmos DB에 저장 되는 위치에서 Azure Machine Learning 서비스를 다시 합니다. 클라이언트에서 Cosmos DB에서 메트릭을 끌어오기를 다시 클라이언트에 반환 하는 Azure Machine Learning 서비스를 호출할 수 있습니다.

![작업 영역의 워크플로 만드는 보여 주는 스크린샷](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>웹 서비스 만들기
다음 다이어그램은 모델을 웹 서비스로 배포 하는 추론 워크플로를 보여줍니다.
아래 세부 정보를 참조 하세요.
* 사용자가 Azure ML SDK와 같은 클라이언트를 사용 하 여 모델 등록
* 사용자 모델, 점수 매기기 파일 및 기타 모델 종속성을 사용 하 여 이미지를 만듭니다.
* Docker 이미지를 만들고 ACR에서 저장
* 위에서 만든 이미지를 사용 하 여 계산 대상 (ACI/AKS)에 배포 된 웹 서비스
* 채 점 요청 세부 정보는 사용자의 구독에 있는 AppInsights에 저장 됩니다.
* 원격 분석은 Microsoft/Azure 구독에도 푸시되 어

![작업 영역의 워크플로 만드는 보여 주는 스크린샷](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>다음 단계

* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-run-batch-predictions.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Virtual Networks에서 Azure Machine Learning Service 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
