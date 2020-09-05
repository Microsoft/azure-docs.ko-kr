---
title: Azure Pipelines를 사용 하 여 앱 구성에 설정 푸시
description: Azure Pipelines를 사용 하 여 키-값을 앱 구성 저장소로 푸시하는 방법을 알아봅니다.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: lcozzens
ms.openlocfilehash: c1142ef7b37dee916118964778f6c1db2a65d591
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719702"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines를 사용 하 여 앱 구성에 설정 푸시

[Azure 앱 구성 푸시](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 작업은 구성 파일의 키-값을 앱 구성 저장소로 푸시합니다. 이 작업을 통해 앱 구성 저장소에서 설정을 끌어올 수 있을 뿐만 아니라 앱 구성 저장소에 대 한 푸시 설정을 사용할 수 있기 때문에 파이프라인 내에서 전체 서클 기능을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 앱 구성 리소스- [Azure Portal](https://portal.azure.com)에서 무료로 무료로 만듭니다.
- Azure DevOps 프로젝트- [무료로 하나 만들기](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure 앱 구성 푸시 작업- [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)에서 무료로 다운로드 합니다.

## <a name="create-a-service-connection"></a>서비스 연결 만들기

서비스 연결을 사용 하 여 azure DevOps 프로젝트에서 Azure 구독의 리소스에 액세스할 수 있습니다.

1. Azure DevOps에서 대상 파이프라인이 포함 된 프로젝트로 이동 하 여 왼쪽 아래에서 **프로젝트 설정을** 엽니다.
1. **파이프라인** 아래에서 **서비스 연결** 을 선택 하 고 오른쪽 위에 있는 **새 서비스 연결** 을 선택 합니다.
1. **Azure Resource Manager**를 선택 합니다.
1. **서비스 사용자 (자동)** 를 선택 합니다.
1. 구독 및 리소스를 입력 합니다. 서비스 연결에 이름을 지정 합니다.

이제 서비스 연결이 만들어졌으므로 할당 된 서비스 사용자의 이름을 찾습니다. 다음 단계에서이 서비스 사용자에 게 새 역할 할당을 추가 합니다.

1. **프로젝트 설정**  >  **서비스 연결**로 이동 합니다.
1. 이전 섹션에서 만든 서비스 연결을 선택 합니다.
1. **서비스 사용자 관리**를 선택 합니다.
1. **표시 이름이 표시** 됩니다.

## <a name="add-role-assignment"></a>역할 할당 추가

태스크가 앱 구성 저장소에 액세스할 수 있도록 작업 내에서 사용 되는 자격 증명에 적절 한 앱 구성 역할 할당을 할당 합니다.

1. 대상 앱 구성 저장소로 이동 합니다. 
1. 왼쪽에서 **Access control (IAM)** 을 선택 합니다.
1. 위쪽에서 **+ 추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다.
1. **역할**아래에서 **앱 구성 데이터 소유자**를 선택 합니다. 이 역할은 태스크가 앱 구성 저장소에서 읽고 쓸 수 있도록 허용 합니다. 
1. 이전 섹션에서 만든 서비스 연결과 관련 된 서비스 주체를 선택 합니다.
  
## <a name="use-in-builds"></a>빌드에 사용

이 섹션에서는 Azure DevOps 빌드 파이프라인에서 Azure 앱 구성 푸시 작업을 사용 하는 방법을 설명 합니다.

1. **파이프라인**파이프라인을 클릭 하 여 빌드 파이프라인 페이지로 이동  >  **Pipelines**합니다. 빌드 파이프라인에 대 한 설명서는 [여기](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)에서 찾을 수 있습니다.
      - 새 빌드 파이프라인을 만드는 경우 파이프라인의 오른쪽에서 **보조자 표시** 를 선택 하 고 **Azure 앱 구성 푸시** 작업을 검색 합니다.
      - 기존 빌드 파이프라인을 사용 하는 경우 파이프라인을 편집할 때 **작업** 탭으로 이동 하 여 **Azure 앱 구성 푸시** 작업을 검색 합니다.
2. 구성 파일의 키 값을 앱 구성 저장소로 푸시하는 작업에 필요한 매개 변수를 구성 합니다. **구성 파일 경로** 매개 변수는 파일 리포지토리의 루트에서 시작 합니다.
3. 빌드를 저장 하 고 큐에 대기 합니다. 빌드 로그에는 태스크를 실행 하는 동안 발생 한 모든 오류가 표시 됩니다.

## <a name="use-in-releases"></a>릴리스에 사용

이 섹션에서는 Azure DevOps 릴리스 파이프라인에서 Azure 앱 구성 푸시 작업을 사용 하는 방법을 설명 합니다.

1. **파이프라인**릴리스를 선택 하 여 릴리스 파이프라인 페이지로 이동  >  **Releases**합니다. 릴리스 파이프라인에 대 한 설명서는 [여기](https://docs.microsoft.com/azure/devops/pipelines/release?view=azure-devops)에서 찾을 수 있습니다.
1. 기존 릴리스 파이프라인을 선택 합니다. 없는 경우 **+ 새로** 만들기를 선택 하 여 새 항목을 만듭니다.
1. 오른쪽 위 모서리에서 **편집** 단추를 선택 하 여 릴리스 파이프라인을 편집 합니다.
1. **단계** 를 선택 하 여 작업을 추가 합니다. 단계에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/devops/pipelines/release/environments?view=azure-devops)를 참조 하세요.
1. **+** 해당 작업에 대해를 선택 하 고 **배포** 탭에서 **Azure 앱 구성 푸시** 작업을 추가 합니다.
1. 작업 내에서 필요한 매개 변수를 구성 하 여 구성 파일의 키 값을 앱 구성 저장소로 푸시합니다. 매개 변수에 대 한 설명은 아래의 **매개 변수** 섹션 및 각 매개 변수 옆에 있는 도구 설명에서 사용할 수 있습니다.
1. 릴리스를 저장 하 고 큐에 대기 합니다. 릴리스 로그에는 태스크를 실행 하는 동안 발생 한 모든 오류가 표시 됩니다.

## <a name="parameters"></a>매개 변수

앱 구성 푸시 작업에서 사용 되는 매개 변수는 다음과 같습니다.

- **Azure 구독**: 사용 가능한 azure 서비스 연결을 포함 하는 드롭 다운입니다. 사용 가능한 Azure 서비스 연결 목록을 업데이트 하 고 새로 고치려면 텍스트 상자의 오른쪽에 있는 **azure 구독 새로 고침** 단추를 누릅니다.
- **앱 구성 이름**: 선택한 구독에서 사용 가능한 구성 저장소를 로드 하는 드롭다운입니다. 사용 가능한 구성 저장소 목록을 업데이트 하 고 새로 고치려면 텍스트 상자의 오른쪽에 있는 **앱 구성 이름 새로 고침** 단추를 누릅니다.
- **구성 파일 경로**: 구성 파일에 대 한 경로입니다. 빌드 아티팩트를 탐색 하 여 구성 파일을 선택할 수 있습니다. ( `...` 입력란 오른쪽에 있는 단추)
- **Separator**: json 및 .yml 파일을 결합 하는 데 사용 되는 구분 기호입니다.
- **Depth**:. i n i 파일 및. m m m 파일의 평면화 되는 수준입니다.
- **Prefix**: 앱 구성 저장소에 푸시되는 각 키의 시작 부분에 추가 되는 문자열입니다.
- **레이블**: 응용 프로그램 구성 저장소 내의 레이블로 각 키 값에 추가 되는 문자열입니다.
- **콘텐츠 형식**: 앱 구성 저장소 내에서 콘텐츠 형식으로 각 키 값에 추가 되는 문자열입니다.
- **태그**: `{"tag1":"val1", "tag2":"val2"}` 앱 구성 저장소에 푸시되는 각 키 값에 추가 되는 태그를 정의 하는 형식의 JSON 개체입니다.
- **지정 된 접두사 및 레이블을 사용 하 여 저장소의 다른 모든 키 값을 삭제**합니다. 기본값은 **선택 취소**되어 있습니다.
  - **선택**됨: 구성 파일에서 새 키 값을 푸시 하기 전에 지정 된 접두사와 레이블과 일치 하는 앱 구성 저장소의 모든 키 값을 제거 합니다.
  - **Unchecked**: 구성 파일의 모든 키 값을 앱 구성 저장소로 푸시하고 앱 구성 저장소에 있는 다른 모든 항목을 그대로 유지 합니다.

필요한 매개 변수를 입력 한 후 파이프라인을 실행 합니다. 지정 된 구성 파일의 모든 키 값이 앱 구성에 업로드 됩니다.

## <a name="troubleshooting"></a>문제 해결

예기치 않은 오류가 발생 하는 경우 파이프라인 변수를로 설정 하 여 디버그 로그를 사용 하도록 설정할 수 있습니다 `system.debug` `true` .

## <a name="faq"></a>FAQ

**여러 구성 파일을 업로드 하려면 어떻게 해야 하나요?**

동일한 파이프라인 내에서 Azure 앱 구성 푸시 작업의 여러 인스턴스를 만들어 앱 구성 저장소에 여러 구성 파일을 푸시합니다.

**내 구성 저장소에 키-값을 푸시하는 동안 409 오류가 발생 하는 이유는 무엇 인가요?**

작업에서 앱 구성 저장소에 잠겨 있는 키-값을 제거 하거나 덮어쓰도록 시도 하면 409 충돌 오류 메시지가 발생 합니다.
