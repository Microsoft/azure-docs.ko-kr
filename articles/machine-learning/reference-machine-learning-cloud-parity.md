---
title: 공용 지역과 소 버린 지역 간 패리티
titleSuffix: Azure Machine Learning
description: 공개 미리 보기 기능과 같은 Azure Machine Learning의 일부 기능은 공용 클라우드 지역 에서만 사용할 수 있습니다. 이 문서에서는 Azure Government, Azure 독일 및 Azure 중국 21Vianet 지역 에서도 사용할 수 있는 기능을 나열 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: c899d14b6e0eea8135b996aadfe2a7388a8fcb89
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861201"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning 소 버린 클라우드 패리티

소 버린 클라우드 지역에서 사용할 수 있는 Azure Machine Learning 기능에 대해 알아봅니다. 

글로벌 Azure 지역 목록에는 특정 시장을 처리 하는 몇 가지 ' 소 버린 ' 지역이 있습니다. 예를 들어 Azure Government 및 Azure 중국 21Vianet 지역이 있습니다. 현재 Azure Machine Learning는 다음 소 버린 클라우드 지역에 배포 됩니다.

* Azure Government 지역 **미국-애리조나** 및 **미국 버지니아**.
* Azure 중국 21Vianet 지역 **중국-동-2**.

> [!TIP]
> 소 버린와 비 소 버린 지역 간에 구분 하기 위해이 문서에서는 __공용 클라우드__ 라는 용어를 사용 하 여 소 버린 지역을 참조 합니다.

공용 클라우드와 소 버린 지역 간에 최대 패리티를 제공 하는 것을 목표로 합니다. 공용 클라우드에서 GA (일반 공급) **의 30 일** 이내에 이러한 지역에서 모든 Azure Machine Learning 기능을 사용할 수 있습니다. 또한 이러한 지역에서 선택 개수의 미리 보기 기능을 사용할 수 있습니다. 아래에는 소 버린와 공용 클라우드 간의 현재 패리티 차이가 표시 됩니다.

## <a name="azure-government"></a>Azure Government 

| 기능 | 공용 클라우드 상태  | US-버지니아 | US-애리조나| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **자동화된 기계 학습** | | | |
| Notebook에서 실험 만들기 및 실행                                    | GA                   | YES                | YES         |
| 스튜디오 웹 환경에서 실험 만들기 및 실행                        | 공개 미리 보기       | YES                | YES         |
| 업계 최고의 예측 기능                                  | GA                   | YES                | YES         |
| 심층 학습 및 기타 고급 학습자 지원                      | GA                   | YES                | YES         |
| 대량 데이터 지원 (최대 100 GB)                                          | 공개 미리 보기       | YES                | YES         |
| Azure Databricks 통합                                              | GA                   | 아니요                 | 아니요          |
| SQL, CosmosDB 및 HDInsight 통합                                   | GA                   | YES                | YES         |
| **파이프라인 Machine Learning** |   |  | | 
| Azure ML SDK를 사용 하 여 파이프라인 만들기, 실행 및 게시                   | GA                   | YES                | YES         |
| Azure ML SDK를 사용 하 여 파이프라인 끝점 만들기                           | GA                   | YES                | YES         |
| Azure ML SDK를 사용 하 여 예약 된 파이프라인의 실행을 만들고, 편집 하 고, 삭제 합니다. | GA                   | 예로               | 예로        |
| 스튜디오에서 파이프라인 실행 세부 정보 보기                                        | GA                   | YES                | YES         |
| Azure ML 디자이너에서 파이프라인 만들기, 실행, 시각화 및 게시          | 공개 미리 보기       | YES                | YES         |
| ML 파이프라인과 Azure Databricks 통합                             | GA                   | 아니요                 | 아니요          |
| Azure ML 디자이너에서 파이프라인 끝점 만들기                             | 공개 미리 보기       | YES                | YES         |
| **통합 노트북** |   |  | | 
| 작업 영역 노트북 및 파일 공유                                        | GA                   | YES                | YES         |
| R 및 Python 지원                                                       | GA                   | YES                | YES         |
| Virtual Network 지원                                                    | 공개 미리 보기       | 아니요                 | 아니요          |
| **컴퓨팅 인스턴스** |   |  | | 
| 통합 전자 필기장에 대 한 관리 되는 계산 인스턴스                         | GA                   | YES                | YES         |
| Jupyter, JupyterLab 통합                                            | GA                   | YES                | YES         |
| 가상 네트워크(VNet) 지원                                             | 공개 미리 보기       | YES                | YES         |
| **SDK 지원** |  |  | | 
| R SDK 지원                                                              | 공개 미리 보기       | YES                | YES         |
| Python SDK 지원                                                         | GA                   | YES                | YES         |
| **보안** |   | | | 
| 교육에 대 한 VNet (Virtual Network) 지원                                | GA                   | YES                | YES         |
| 유추를 위한 VNet (Virtual Network) 지원                               | GA                   | YES                | YES         |
| 끝점 인증 점수 매기기                                            | 공개 미리 보기       | YES                | YES         |
| 작업 공간 개인 링크                                                     | 공개 미리 보기       | 아니요                 | 아니요          |
| VNet 뒤에 있는 ACI                                                            | 공개 미리 보기       | 아니요                 | 아니요          |
| VNet 뒤의 ACR                                                            | 공개 미리 보기       | 아니요                 | 아니요          |
| AKS 클러스터의 개인 IP                                                  | 공개 미리 보기       | 아니요                 | 아니요          |
| **컴퓨팅** |   | | |
| 작업 영역에서 할당량 관리                                         | GA                   | YES                | YES         |
| **기계 학습에 대 한 데이터** |   | | |
| SDK에서 데이터 집합 및 데이터 저장소 만들기, 보기 또는 편집                  | GA                   | YES                | YES         |
| UI에서 데이터 집합 및 데이터 저장소 만들기, 보기 또는 편집                   | GA                   | YES                | YES         |
| SDK에서 데이터 집합 드리프트 모니터 보기, 편집 또는 삭제                   | 공개 미리 보기       | YES                | YES         |
| UI에서 데이터 집합 드리프트 모니터 보기, 편집 또는 삭제                    | 공개 미리 보기       | YES                | YES         |
| **기계 학습 수명 주기** |   | | |
| 모델 프로 파일링                                                            | GA                   | YES                | PARTIAL     |
| Azure ML CLI & Machine Learning에 대 한 Azure DevOps 확장         | GA                   | YES                | YES         |
| FPGA 기반 하드웨어 가속 모델                                     | GA                   | 아니요                 | 아니요          |
| Visual Studio Code 통합                                             | 공개 미리 보기       | 아니요                 | 아니요          |
| Event Grid 통합                                                     | 공개 미리 보기       | 아니요                 | 아니요          |
| Azure Machine Learning과 Azure Stream Analytics 통합               | 공개 미리 보기       | 아니요                 | 아니요          |
| **원인은** |   | | |
| 프로젝트 관리 포털 레이블 지정                                        | GA                   | YES                | YES         |
| Labeler 포털                                                            | GA                   | YES                | YES         |
| 개인 인력을 사용 하 여 레이블 지정                                          | GA                   | YES                | YES         |
| ML 지원 레이블 지정 (이미지 분류 및 개체 검색)           | 공개 미리 보기       | YES                | YES         |
| **Responsible ML** |   | | |
| UI의 Explainability                                                       | 공개 미리 보기       | 아니요                 | 아니요          |
| 차등 개인 정보 WhiteNoise 도구 키트                                    | .OSS                  | 아니요                 | 아니요          |
| 데이터 시트를 구현 하 Azure Machine Learning의 사용자 지정 태그              | GA                   | 아니요                 | 아니요          |
| 공평 AzureML 통합                                               | 공개 미리 보기       | 아니요                 | 아니요          |
| Interpretability SDK                                                      | GA                   | YES                | YES         |
| **교육이** |   | | |
| 실험 로그 스트리밍                                              | GA                   | YES                | YES         |
| 보충 학습                                                     | 공개 미리 보기       | 아니요                 | 아니요          |
| 실험 UI                                                         | GA                   | YES                | YES         |
| .NET 통합 ML.NET 1.0                                                | GA                   | YES                | YES         |
| **유추** |   | | |
| Batch 추론                                                          | GA                   | YES                | YES         |
| FPGA를 사용 하 여 Data Box Edge                                                    | 공개 미리 보기       | 아니요                 | 아니요          |
| **기타** |   | | |
| Open Datasets                                                              | 공개 미리 보기       | YES                | YES         |
| 사용자 지정 Cognitive Search                                                    | 공개 미리 보기       | YES                | YES         |
| 많은 모델                                                                | 공개 미리 보기       | 아니요                 | 아니요          |


### <a name="azure-government-scenarios"></a>Azure Government 시나리오

| 시나리오                                                    | US-버지니아 | US-애리조나| 제한 사항  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **일반 보안 설정** |   | | |
| 서비스 간 개인 네트워크 통신                                     | 아니요 | 아니요 | 현재 개인 링크 없음 | 
| 인터넷 액세스 사용 안 함/제어 (인바운드 및 아웃 바운드) 및 특정 VNet | PARTIAL| PARTIAL   | VNet 뒤의 ACR는 Azure Government에서 사용할 수 없습니다. ACI에서 더블 검사 | 
| 연결 된 모든 리소스/서비스의 배치  | YES | YES |  |
| 미사용 암호화 및 전송 중입니다.                                                 | YES | YES |  |
| 계산 리소스에 대 한 루트 및 SSH 액세스.                                          | YES | YES |  |
| Endpoint protection, 패치 및 로깅을 포함 하 여 배포 된 시스템 (인스턴스, 끝점 등)의 보안 유지 |  PARTIAL|  PARTIAL |현재 VNet 및 개인 끝점 뒤에 있는 ACI를 사용할 수 없음 |                                  
| ACI/AKS 통합 사용 제어 (사용 안 함/제한/제한)                    | PARTIAL| PARTIAL |현재 VNet 및 개인 끝점 뒤에 있는 ACI를 사용할 수 없음|
| RBAC (역할 기반 Access Control)-사용자 지정 역할 만들기                           | YES | YES |  |
| ML 서비스에서 사용 하는 ACR 이미지에 대 한 액세스 제어 (Azure 제공/유지 관리 및 사용자 지정)  |PARTIAL|  PARTIAL | Azure Government에서 지원 되지 않는 개인 끝점 및 VNet 뒤의 ACR |
| **일반 Machine Learning 서비스 사용** |  | | |
| 개발 환경을 사용 하 여 모델을 작성 하 고, 해당 모델을 학습 하 고, 끝점으로 호스트 하 고, webapp을 통해 사용 하는 기능     | YES | YES |  |
| ADLS에서 데이터를 끌어오는 기능 (Data Lake Storage)                                 |YES | YES |  |
| Azure Blob Storage에서 데이터를 가져오는 기능                                       |YES | YES |  |



### <a name="additional-azure-government-limitations"></a>추가 Azure Government 제한 사항

* Azure Machine Learning 계산 인스턴스의 경우 24 시간 이상 지속 되는 토큰을 새로 고치는 기능은 Azure Government에서 사용할 수 없습니다.
* 모델 프로 파일링은 미국 애리조나 지역에서 4 개의 Cpu를 지원 하지 않습니다.   
* 샘플 노트북은 공용 데이터에 액세스 해야 하는 경우 Azure Government에서 작동 하지 않을 수 있습니다.
* IP 주소: [VNet 및 강제 터널링](how-to-enable-virtual-network.md#forced-tunneling) 명령에 사용 된 CLI 명령은 ip 범위를 반환 하지 않습니다. 대신 [AZURE IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063) 를 사용 하 여 Azure Government 합니다.
* 또한 예약 된 파이프라인의 경우 blob 기반 트리거 메커니즘도 제공 합니다. CMK 작업 영역에 대해서는이 메커니즘이 지원 되지 않습니다. CMK 작업 영역에 대 한 blob 기반 트리거를 사용 하도록 설정 하려면 추가 설치를 수행 해야 합니다. 자세한 내용은 [논리 앱에서 기계 학습 파이프라인 실행 트리거](how-to-trigger-published-pipeline.md)를 참조 하세요.
* 방화벽: Azure Government 영역을 사용 하는 경우 방화벽 설정에 다음과 같은 추가 호스트를 추가 합니다.

    * 애리조나 사용: `usgovarizona.api.ml.azure.us`
    * 버지니아 사용: `usgovvirginia.api.ml.azure.us`
    * 둘 다: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure 중국 21Vianet 

| 기능                                       | 공용 클라우드 상태 | CH-동-2 | CH-북부-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **자동화된 기계 학습** |    | | |
| Notebook에서 실험 만들기 및 실행                                    | GA               | YES       | 해당 없음        |
| 스튜디오 웹 환경에서 실험 만들기 및 실행                        | 공개 미리 보기   | YES       | 해당 없음        |
| 업계 최고의 예측 기능                                  | GA               | YES       | 해당 없음        |
| 심층 학습 및 기타 고급 학습자 지원                      | GA               | YES       | 해당 없음        |
| 대량 데이터 지원 (최대 100 GB)                                          | 공개 미리 보기   | YES       | 해당 없음        |
| Azure Databricks 통합                                              | GA               | 아니요        | 해당 없음        |
| SQL, CosmosDB 및 HDInsight 통합                                   | GA               | YES       | 해당 없음        |
| **파이프라인 Machine Learning** |    | | |
| Azure ML SDK를 사용 하 여 파이프라인 만들기, 실행 및 게시                   | GA               | YES       | 해당 없음        |
| Azure ML SDK를 사용 하 여 파이프라인 끝점 만들기                           | GA               | YES       | 해당 없음        |
| Azure ML SDK를 사용 하 여 예약 된 파이프라인의 실행을 만들고, 편집 하 고, 삭제 합니다. | GA               | YES       | 해당 없음        |
| 스튜디오에서 파이프라인 실행 세부 정보 보기                                        | GA               | YES       | 해당 없음        |
| Azure ML 디자이너에서 파이프라인 만들기, 실행, 시각화 및 게시          | 공개 미리 보기   | YES       | 해당 없음        |
| ML 파이프라인과 Azure Databricks 통합                             | GA               | 아니요        | 해당 없음        |
| Azure ML 디자이너에서 파이프라인 끝점 만들기                             | 공개 미리 보기   | YES       | 해당 없음        |
| **통합 노트북** |   | | |
| 작업 영역 노트북 및 파일 공유                                        | GA               | YES       | 해당 없음        |
| R 및 Python 지원                                                       | GA               | YES       | 해당 없음        |
| Virtual Network 지원                                                    | 공개 미리 보기   | 아니요        | 해당 없음        |
| **컴퓨팅 인스턴스** |    | | |
| 통합 전자 필기장에 대 한 관리 되는 계산 인스턴스                         | GA               | 아니요        | 해당 없음        |
| Jupyter, JupyterLab 통합                                            | GA               | YES       | 해당 없음        |
| 가상 네트워크(VNet) 지원                                             | 공개 미리 보기   | YES       | 해당 없음        |
| **SDK 지원** |    | | |
| R SDK 지원                                                              | 공개 미리 보기   | YES       | 해당 없음        |
| Python SDK 지원                                                         | GA               | YES       | 해당 없음        |
| **보안** |   | | |
| 교육에 대 한 VNet (Virtual Network) 지원                                | GA               | YES       | 해당 없음        |
| 유추를 위한 VNet (Virtual Network) 지원                               | GA               | YES       | 해당 없음        |
| 끝점 인증 점수 매기기                                            | 공개 미리 보기   | YES       | 해당 없음        |
| 작업 공간 개인 링크                                                     | 공개 미리 보기   | 아니요        | 해당 없음        |
| VNet 뒤에 있는 ACI                                                            | 공개 미리 보기   | 아니요        | 해당 없음        |
| VNet 뒤의 ACR                                                            | 공개 미리 보기   | 아니요        | 해당 없음        |
| AKS 클러스터의 개인 IP                                                  | 공개 미리 보기   | 아니요        | 해당 없음        |
| **컴퓨팅** |   | | |
| 작업 영역에서 할당량 관리                                         | GA               | YES       | 해당 없음        |
| **기계 학습에 대 한 데이터** | | | |
| SDK에서 데이터 집합 및 데이터 저장소 만들기, 보기 또는 편집                  | GA               | YES       | 해당 없음        |
| UI에서 데이터 집합 및 데이터 저장소 만들기, 보기 또는 편집                   | GA               | YES       | 해당 없음        |
| SDK에서 데이터 집합 드리프트 모니터 보기, 편집 또는 삭제                   | 공개 미리 보기   | YES       | 해당 없음        |
| UI에서 데이터 집합 드리프트 모니터 보기, 편집 또는 삭제                    | 공개 미리 보기   | YES       | 해당 없음        |
| **기계 학습 수명 주기** |    | | |
| 모델 프로 파일링                                                            | GA               | PARTIAL   | 해당 없음        |
| Azure ML CLI & Machine Learning에 대 한 Azure DevOps 확장         | GA               | YES       | 해당 없음        |
| FPGA 기반 하드웨어 가속 모델                                     | GA               | 아니요        | 해당 없음        |
| Visual Studio Code 통합                                             | 공개 미리 보기   | 아니요        | 해당 없음        |
| Event Grid 통합                                                     | 공개 미리 보기   | YES       | 해당 없음        |
| Azure Machine Learning과 Azure Stream Analytics 통합               | 공개 미리 보기   | 아니요        | 해당 없음        |
| **원인은** |    | | |
| 프로젝트 관리 포털 레이블 지정                                        | GA               | YES       | 해당 없음        |
| Labeler 포털                                                            | GA               | YES       | 해당 없음        |
| 개인 인력을 사용 하 여 레이블 지정                                          | GA               | YES       | 해당 없음        |
| ML 지원 레이블 지정 (이미지 분류 및 개체 검색)           | 공개 미리 보기   | YES       | 해당 없음        |
| **Responsible ML** |    | | |
| UI의 Explainability                                                       | 공개 미리 보기   | 아니요        | 해당 없음        |
| 차등 개인 정보 WhiteNoise 도구 키트                                    | .OSS              | 아니요        | 해당 없음        |
| 데이터 시트를 구현 하 Azure Machine Learning의 사용자 지정 태그              | GA               | 아니요        | 해당 없음        |
| 공평 AzureML 통합                                               | 공개 미리 보기   | 아니요        | 해당 없음        |
| Interpretability SDK                                                      | GA               | YES       | 해당 없음        |
| **교육이** |    | | |
| 실험 로그 스트리밍                                              | GA               | YES       | 해당 없음        |
| 보충 학습                                                     | 공개 미리 보기   | 아니요        | 해당 없음        |
| 실험 UI                                                         | GA               | YES       | 해당 없음        |
| .NET 통합 ML.NET 1.0                                                | GA               | YES       | 해당 없음        |
| **유추** |   | | |
| Batch 추론                                                          | GA               | YES       | 해당 없음        |
| FPGA를 사용 하 여 Data Box Edge                                                    | 공개 미리 보기   | 아니요        | 해당 없음        |
| **기타** |    | | |
| Open Datasets                                                              | 공개 미리 보기   | YES       | 해당 없음        |
| 사용자 지정 Cognitive Search                                                    | 공개 미리 보기   | YES       | 해당 없음        |
| 많은 모델                                                                | 공개 미리 보기   | 아니요        | 해당 없음        |



### <a name="additional-azure-china-limitations"></a>추가 Azure 중국 제한 사항

* Azure 중국에는 특히 GPU SKU에 대 한 VM SKU가 제한 되어 있습니다. NCv3 제품군 (V100)만 있습니다.
* REST API 끝점은 글로벌 Azure와 다릅니다. 다음 표를 사용 하 여 Azure 중국 지역에 대 한 REST API 끝점을 찾습니다.

    | REST 끝점                 | 글로벌 Azure                                 | 중국-정부                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 관리 평면 | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | 데이터 평면       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* 공용 데이터에 액세스 해야 하는 경우 샘플 노트북이 작동 하지 않을 수 있습니다.
* IP 주소 범위: [VNet 강제 터널링](how-to-enable-virtual-network.md#forced-tunneling) 명령에 사용 된 CLI 명령은 ip 범위를 반환 하지 않습니다. 대신 azure [중국의 AZURE IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062) 를 사용 하세요.
* 현재는 개인 링크가 사용 하도록 설정 된 작업 영역에서는 계산 인스턴스 미리 보기가 지원 되지 않지만, CI는 모든 AML 지역으로의 서비스 확장을 위한 다음 배포에서 지원 됩니다. Azure Machine Learning

## <a name="next-steps"></a>다음 단계

에서 Azure Machine learning을 사용할 수 있는 지역에 대 한 자세한 내용은 [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.
