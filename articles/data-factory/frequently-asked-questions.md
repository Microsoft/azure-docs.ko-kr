---
title: 'Azure Data Factory: 질문과 대답 | Microsoft 문서'
description: Azure Data Factory에 대한 질문과 대답입니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345757"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
이 아티클에서는 Azure Data Factory에 대한 질문과 대답을 제공합니다.  

## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 이동과 변환을 자동화하는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 장비를 작동하여 원자재를 완제품으로 변형하는 공장처럼 Azure Data Factory는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다. 

Azure Data Factory를 사용하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있습니다. 처리할 수 있습니다 및 변환 데이터를 사용 하 여 Azure HDInsight, Azure Data Lake Analytics 및 SQL Server Integration Services (SSIS) integration runtime과 같은 서비스를 계산 합니다. 

Data Factory를 사용하여 Azure 기반 클라우드 서비스에서 데이터 처리를 실행하거나 SSIS, SQL Server 및 Oracle과 같은 자체 호스팅 컴퓨팅 환경을 활용할 수 있습니다. 필요한 작업을 수행 하는 파이프라인을 만든 후에 주기적으로 (매시간, 매일 또는 매주, 예를 들어) 기간 예약 실행 되도록 예약할 수도 있고 파이프라인 이벤트 발생을 트리거할 수 있습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요.

### <a name="control-flows-and-scale"></a>제어 흐름 및 크기 조정 
Data Factory 수 있도록 최신 데이터 웨어하우스의 다양 한 통합 흐름 및 패턴을 지원 하기 위해 유연한 데이터 모델링을 파이프라인입니다. 이 프로그래밍 패러다임 조건부 실행을 포함 하는, 매개 변수 내에서 그리고 이러한 흐름 간에 명시적으로 전달 하는 기능과 데이터 파이프라인을 분기의 전체 제어 흐름을 수반 합니다. 제어 흐름 작업 디스패치 외부 실행 엔진 및 복사 작업을 통해 대규모 데이터 이동을 포함 하 여 데이터 흐름 기능을 통해 데이터를 변환도 포함 합니다.

Data Factory는 데이터 통합에 필요하고 요청 시 또는 일정에 따라 반복해서 디스패치할 수 있는 모든 흐름 스타일을 자유롭게 모델링하도록 합니다. 이 모델이 사용할 수 있는 일반적인 흐름은 다음과 같습니다.   

- 제어 흐름:
    - 파이프라인 내에서 시퀀스로 활동을 함께 연결할 수 있습니다.
    - 파이프라인 내에서 활동을 분기할 수 있습니다.
    - 매개 변수
        - 파이프라인 수준에서 매개 변수를 정의할 수 있습니다 하 고 요청 시 또는 트리거에서 파이프라인을 호출 하는 동안에 인수를 전달할 수 있습니다.
        - 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다.
    - 사용자 지정 상태 전달 합니다.
        - 파이프라인의 이후 작업 상태를 포함 하 여 작업 출력을 사용할 수 있습니다.
    - 컨테이너 루프 화 합니다.
        - Foreach 작업은 루프에서 활동의 지정 된 컬렉션을 반복 됩니다. 
- 트리거 기반 흐름:
    - 요청 시 또는 벽시계 시간에 따라 파이프라인을 트리거할 수 있습니다.
- 델타 흐름:
    - 데이터를 레이크에 로드 하는 클라우드 또는 온-프레미스 관계형 저장소에서 차원 또는 참조 테이블을 이동 하는 동안 복사 용 높은-워터 마크를 정의 하려면 매개 변수를 사용할 수 있습니다. 

자세한 내용은 [자습서: 제어 흐름](tutorial-control-flow.md)을 참조하세요.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>코드가 없는 파이프라인을 사용 하 여 대규모로 변환 된 데이터
새로운 브라우저 기반 도구 환경을 통해 코드 제한 없이 파이프라인을 작성하고 최신 대화형 웹 기반 환경에서 배포할 수 있습니다.

데이터 엔지니어와 시각적 데이터 개발자에 대 한 웹 UI를 Data Factory를 사용 하 여 파이프라인을 구축 하는 코드 없는 디자인 환경입니다. Visual Studio Online Git를 사용 하 여 완전히 통합 하 고 CI/cd 통합 및 디버깅 옵션을 사용 하 여 반복적인 개발을 제공 합니다.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>고급 사용자를 위한 다양 한 플랫폼 간 Sdk
Data Factory V2 제공 작성, 관리 및 즐겨 찾는 IDE를 사용 하 여 파이프라인을 모니터링 하는 Sdk의 다양 한 집합을 포함 합니다.
* Python SDK
* PowerShell CLI
* C# SDK

사용자가 Data Factory V2를 사용 하 여 인터페이스에 문서화 된 REST Api를 이용할 수 있습니다.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>반복적인 개발 및 시각적 도구를 사용 하 여 디버깅
Azure Data Factory 시각적 도구 반복적인 개발 및 디버깅을 사용 합니다. 파이프라인을 만들 수 있습니다 하 고 실행을 테스트 하려면 사용 하 여 합니다 **디버그** 코드 한 줄도 작성 하지 않고 파이프라인 캔버스의 기능입니다. 테스트 실행의 결과 볼 수는 **출력** 파이프라인 캔버스의 창. 테스트 실행에 성공한 후에 파이프라인에 더 많은 작업을 추가 하 고 반복적으로 디버깅을 계속할 수 있습니다. 진행에서 된 후에 테스트 실행을 취소할 수 있습니다. 

선택 하기 전에 데이터 팩터리 서비스에 변경 내용을 게시 하지 않아도 됩니다 **디버그**합니다. 새 추가 또는 변경 개발, 테스트 또는 프로덕션 환경에서 데이터 팩터리 워크플로 업데이트 하기 전에 예상 대로 작동 됩니다 하는지 확인 하려는 시나리오에서 유용 합니다. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Azure에 SSIS 패키지를 배포 하는 기능 
SSIS 워크로드를 이동하려는 경우 Data Factory를 만들고 Azure-SSIS Integration Runtime을 프로비전할 수 있습니다. AZURE-SSIS integration runtime을는 클라우드에서 SSIS 패키지 실행을 전담 하는 Azure Vm (노드)의 완전히 관리 되는 클러스터. 단계별 지침은 [Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md) 자습서를 참조하세요. 
 
### <a name="sdks"></a>SDK
고급 사용자를 프로그래밍 방식 인터페이스를 찾는다면, Data Factory는 다양 한 Sdk 집합을 작성, 관리 또는 즐겨 찾는 IDE를 사용 하 여 파이프라인 모니터링에 사용할 수 있습니다. .NET, PowerShell, Python 및 REST를 비롯한 언어를 지원합니다.

### <a name="monitoring"></a>모니터링
브라우저 사용자 인터페이스에서 PowerShell, SDK 또는 시각적 개체 모니터링 도구를 통해 Data Factories를 모니터링할 수 있습니다. 모니터링 하 고 효율적이 고 효과적인 방식으로 요청 시 트리거를 기반으로 고 클록 기반 사용자 지정 흐름을 관리할 수 있습니다. 기존 작업 취소를 한눈에 참조 오류 드릴 다운 하 여 자세한 오류 메시지를 가져오고 컨텍스트 전환이 나 화면 간에 앞뒤로 이동 하지 않고 투명 효과 단일 창이 모두에서 문제를 디버그 합니다. 

### <a name="new-features-for-ssis-in-data-factory"></a>Data Factory에서 SSIS에 대 한 새로운 기능
초기 공용 2017의 릴리스를 미리 보려면 이므로 Data Factory가 SSIS에 대 한 다음과 같은 기능을 추가:

-   세 가지에 대 한 자세한 구성/프로젝트/패키지의 SSIS 데이터베이스 (SSISDB)를 호스트 하는 Azure SQL Database의 변형을 지원 합니다.
-   Virtual network 서비스 엔드포인트를 사용 하 여 SQL Database
-   Managed Instance
-   탄력적 풀
-   클래식 가상 네트워크를 나중에 사용 되지 않을 수 있는 기반으로 하는 Azure Resource Manager 가상 네트워크에 대 한 지원 삽입/조인 virtual network 서비스를 사용 하 여 SQL Database에 대해 구성 된 가상 네트워크에 AZURE-SSIS integration runtime 끝점/MI/온-프레미스 데이터 액세스. 자세한 내용은 참고 [가상 네트워크에 AZURE-SSIS 통합 런타임을 조인](join-azure-ssis-integration-runtime-virtual-network.md)합니다.
-   Azure Active Directory (Azure AD) 인증 및 Azure 리소스에 대 한 Data Factory 관리 id 사용 하 여 Azure AD 인증을 허용 하는 SSISDB에 연결할 SQL 인증에 대 한 지원
-   Azure 하이브리드 혜택 옵션에서 상당한 비용 절감을 획득 하려면 사용자 고유의 온-프레미스 SQL Server 라이선스에 대 한 지원
-   고급/프리미엄 기능을 추가 구성 요소/확장 및 파트너 에코 시스템을 설치 하는 사용자 지정 설정 인터페이스를 사용 하는 수 있는 AZURE-SSIS integration runtime Enterprise 버전에서 지원 합니다. 자세한 내용은 참고 [Enterprise Edition, 사용자 지정 설치 및 ADF에서 SSIS에 대 한 타사 파티 확장성](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)합니다. 
-   Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 첫 번째 클래스 호출/트리거 및 SSMS를 통해 예약할 수 있는 Data Factory에서 SSIS 심층적으로 통합 합니다. 자세한 내용은 참고 [Modernize ADF 파이프라인에서 SSIS 작업을 사용 하 여 ETL/ELT 워크플로 확장 하 고](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)입니다.


## <a name="what-is-the-integration-runtime"></a>Integration runtime 이란?
Integration runtime은 Azure Data Factory는 다양 한 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공 하는 데 사용 하는 계산 인프라.

- **데이터 이동**: 데이터 이동을 위해, 통합 런타임은 기본 제공 커넥터, 형식 변환, 열 매핑 및 고성능 및 확장성이 뛰어난 데이터 전송에 대 한 지원을 제공 하는 동안 원본 및 대상 데이터 저장소 간에 데이터를 이동 합니다.
- **Dispatch 활동**: 변환에 대 한 통합 런타임은 SSIS 패키지를 고유 하 게 실행 하는 기능을 제공 합니다.
- **SSIS 패키지 실행**: Integration runtime는 고유 하 게 관리 되는 Azure 계산 환경에서 SSIS 패키지를 실행합니다. Integration runtime 디스패치와 다양 한 Azure HDInsight, Azure Machine Learning, SQL Database 및 SQL Server와 같은 계산 서비스를 실행 하는 변환 활동 모니터링을 지원 합니다.

이동 및 데이터를 변환 하는 데 필요한 만큼 하나 이상의 integration runtime 인스턴스를 배포할 수 있습니다. Integration runtime은 Azure 공용 네트워크 또는 개인 네트워크 (온-프레미스, Azure Virtual Network 또는 Amazon Web Services 가상 사설 클라우드 VPC)에서 실행할 수 있습니다. 

자세한 내용은 [Azure Data Factory의 Integration Runtime](concepts-integration-runtime.md)을 참조하세요.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>통합 런타임의 수 제한은 얼마입니까?
Data Factory에서 사용할 수 있는 Integration Runtimes 인스턴스의 수에는 엄격한 제한이 없습니다. 하지만 Integration Runtime이 SSIS 패키지 실행을 위해 구독당 사용할 수 있는 VM 코어 수는 제한되어 있습니다. 자세한 내용은 [Data Factory 제한](../azure-subscription-service-limits.md#data-factory-limits)을 참조하세요.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory의 최상위 개념은 무엇인가요?
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼으로서 함께 작동하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="pipelines"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이점은 각 작업을 개별적으로 관리하는 대신, 파이프라인을 사용하여 여러 작업을 하나의 집합으로 관리할 수 있다는 것입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="activities"></a>활동
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어, 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사 하려면 복사 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="datasets"></a>데이터 세트
데이터 세트는 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 이 방법으로 생각해 보십시오. 데이터 원본에 연결을 정의 하는 연결된 된 서비스 및 데이터 집합 데이터의 구조를 나타냅니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 및 Azure blob 데이터 집합을 blob 컨테이너 및 데이터가 포함 된 폴더를 지정 합니다.

연결된 서비스는 Data Factory에서 다음 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server 인스턴스, Oracle 데이터베이스 인스턴스 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소*를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록에 대해서는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 *계산 리소스*를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 컴퓨팅 환경 목록은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작할 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다. 

### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 파이프라인 실행을 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다. 파이프라인에서 매개 변수를 정의하고 실행 컨텍스트에서 실행하는 동안 정의된 매개 변수에 대한 인수를 전달합니다. 실행 컨텍스트는 트리거 또는 수동으로 실행하는 파이프라인에서 생성됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 집합은 강력한 형식된 매개 변수 이며 다시 사용 하거나 참조할 수 있는 엔터티. 데이터 집합 정의에 정의 된 속성을 사용할 수 있습니다 및 작업 데이터 집합을 참조할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 컴퓨팅 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 다시 사용 하거나 참조할 수 있는 엔터티 이기도 합니다.

### <a name="control-flows"></a>제어 흐름
제어 흐름은 파이프라인 수준에서 정의하는 시퀀스, 분기 및 매개 변수의 작업 연결과 요청 시 또는 트리거에서 파이프라인을 호출할 때 전달하는 인수를 포함하는 파이프라인 작업을 조율합니다. 제어 흐름에는 사용자 지정 상태 전달 및 컨테이너 루핑 (즉, foreach 반복기)도 포함 됩니다.


Data Factory 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)
- [파이프라인 및 활동](concepts-pipelines-activities.md)
- [통합 런타임](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory의 가격 책정 모델은 무엇입니까?
Azure Data Factory에 대한 가격 정보는 [Data Factory 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-factory/)를 참조하세요.

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory에 대한 최신 정보를 얻으려면 어떻게 해야 하나요?
Azure Data Factory에 대한 최신 정보를 얻으려면 다음 사이트로 이동하세요.

- [블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [설명서 홈페이지](/azure/data-factory)
- [제품 홈페이지](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>기술 심층 분석 

### <a name="how-can-i-schedule-a-pipeline"></a>파이프라인을 어떻게 예약할 수 있나요? 
스케줄러 트리거 또는 기간 트리거를 사용하여 파이프라인을 예약할 수 있습니다. 트리거는 벽 시계 캘린더 일정을 예약할 수 있는 파이프라인이 주기적으로 또는 캘린더 기반 반복 패턴 (월요일 오후 6 시에) 및 목요일 오후 9 시에서 예를 들어,를 사용 합니다. 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>파이프라인 실행에 매개 변수를 전달할 수 있나요?
예, 매개 변수는 Data Factory의 최상위 수준 개념입니다. 파이프라인 수준에서 매개 변수를 정의하고 요청 시 실행되는 파이프라인 실행 중에 또는 트리거를 사용하여 인수를 전달할 수 있습니다.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>파이프라인 매개 변수의 기본값을 정의할 수 있습니까? 
예. 파이프라인의 매개 변수에 대한 기본값을 정의할 수 있습니다. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>파이프라인의 작업이 파이프라인 실행에 전달된 인수를 사용할 수 있습니까? 
예. 파이프라인 내의 각 작업은 `@parameter` 구문으로 실행되는 파이프라인에 전달된 매개 변수 값을 사용할 수 있습니다. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>활동 출력 속성이 다른 활동에서 사용될 수 있습니까? 
예. 활동 출력은 `@activity` 구문으로 후속 활동에서 사용될 수 있습니다.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>활동 출력의 Null 값을 정상적으로 처리하려면 어떻게 해야 합니까? 
식에서 `@coalesce` 구문을 사용하여 Null 값을 정상적으로 처리할 수 있습니다. 

## <a name="mapping-data-flows"></a>매핑 데이터 흐름

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Data Factory 버전 데이터 흐름을 만들고 사용 합니까?
데이터 흐름을 만들려면 Data Factory V2 버전을 사용 합니다.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>이전 비공개 미리 보기 고객에 게 데이터 흐름을 사용 하 고 Data Factory V2 미리 보기 버전을 데이터 흐름에 사용 되는 합니다.
이 버전은 이제 사용 되지 않습니다. 데이터 흐름에 대 한 Data Factory V2를 사용 합니다.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>무엇이 데이터 흐름 인증과 관련 하 여 제한 된 공개 미리 보기에 비공개 미리 보기에서 변경 되었습니까?
사용자 고유의 Azure Databricks 클러스터를 더 이상. Data Factory는 클러스터 만들기 및 중지를 관리 합니다. Blob 데이터 집합 및 Azure Data Lake 저장소 Gen2 데이터 집합 구분 기호로 분리 된 텍스트 및 Apache Parquet 데이터 집합으로 나뉩니다. 이러한 파일을 저장할 Data Lake 저장소 Gen2 및 Blob storage를 사용할 수 있습니다. 이러한 저장소 엔진에 대 한 적절 한 연결 된 서비스를 사용 합니다.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Data Factory V2를 내 비공개 미리 보기 팩터리를 마이그레이션할 수 있나요?

예. [지침에 따라](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration)합니다.

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>데이터 흐름 논리 내 문제를 해결 하는 데 도움이 필요 합니다. 어떤 정보가 도움을 제공 하나요?

Microsoft 도움말 이나 데이터 흐름을 사용 하 여 문제 해결을 제공 하는 경우에 DSL 코드 계획을 제공 하세요. 이렇게 하려면 다음 단계를 수행하세요.

1. 데이터 흐름 디자이너에서 선택 **코드** 오른쪽 위 모퉁이에서. 데이터 흐름에 대 한 JSON 코드를 편집 가능 보기가 표시 됩니다.
2. 코드 보기에서 선택 **계획** 오른쪽 위 모퉁이에서. 이 설정/이 해제는 JSON에서 읽기 전용으로 서식이 지정 된 DSL 스크립트 계획으로 전환 됩니다.
3. 이 스크립트를 복사 및 붙여넣거나 텍스트 파일에 저장 합니다.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Data Factory의 다른 80 데이터 집합 형식을 사용 하 여 데이터를 액세스 하려면 어떻게 합니까?

데이터 흐름 매핑 기능은 현재 Azure SQL Database, Azure SQL Data Warehouse에서 Azure Blob storage 또는 Azure Data Lake 저장소 Gen2 텍스트 파일 및 원본 및 싱크를 고유 하 게 Blob storage 또는 Data Lake 저장소 Gen2에서 Parquet 파일 구분 기호로 분리 된 수 있습니다. 

다른 커넥터에서 단계 데이터 복사 작업을 사용 하 고 준비 된 후 데이터를 변환 하는 데이터 흐름 작업을 실행 합니다. 예를 들어 파이프라인 먼저 Blob 저장소에 복사 됩니다 및 다음 데이터 흐름 작업을 사용 됩니다 데이터 집합 원본에서 해당 데이터를 변환.

## <a name="next-steps"></a>다음 단계
데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [자습서: 클라우드에 데이터 복사](tutorial-copy-data-dot-net.md)
