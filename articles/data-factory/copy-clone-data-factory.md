---
title: Azure Data Factory에서 데이터 팩터리 복사 또는 복제 | Microsoft Docs
description: Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780934"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory에서 데이터 팩터리 복사 또는 복제

이 문서에서는 Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법을 설명합니다.

## <a name="use-cases-for-cloning-a-data-factory"></a>데이터 팩터리 복제 사용 사례

다음은 데이터 팩터리를 복사 또는 복제하는 것이 유용할 수 있는 몇 가지 상황입니다.

-   **리소스 이름 바꾸기**. Azure는 리소스 이름 바꾸기를 지원하지 않습니다. 데이터 팩터리 이름을 바꾸려는 경우 데이터 팩터리를 다른 이름으로 복제한 후 기존 데이터 팩터리를 삭제할 수 있습니다.

-   디버그 기능으로 충분하지 않을 경우 **변경 내용 디버그**. 때때로 변경 내용을 디버그하려는 경우 변경 내용을 주 팩터리에 적용하기 전에 다른 팩터리에서 테스트할 수 있습니다. 대부분의 시나리오에서는 디버그를 사용할 수 있습니다. 그러나 트리거가 자동으로 호출될 때 또는 특정 기간 동안 변경이 동작하는 방식과 같은 트리거의 변경 내용은 체크 인하지 않으면 쉽게 테스트하지 못할 수 있습니다. 이러한 경우 팩터리를 복제하고 변경 내용을 적용하는 것이 상당히 적절할 수 있습니다. Azure Data Factory는 주로 실행 횟수에 따라 요금이 부과되므로 두 번째 팩터리가 추가 요금을 발생하지는 않습니다.

## <a name="how-to-clone-a-data-factory"></a>데이터 팩터리 복제 방법

1. Azure Portal의 Data Factory UI를 사용하여 데이터 팩터리의 전체 페이로드를 매개 변수 파일과 함께 Resource Manager 템플릿으로 내보내면 팩터리를 복제할 때 변경하려는 값을 변경할 수 있습니다.

1. 필수 요소로, Azure Portal에서 대상 데이터 팩터리를 만들어야 합니다.

1. 원본 팩터리의 SelfHosted IntegrationRuntime 경우 대상 공장에서 동일한 이름을 가진 미리 해야 합니다. 다른 팩터리 간의 SelfHosted IRs를 공유 하려는 경우에 게시 된 패턴을 사용할 수 있습니다 [여기](author-visually.md#best-practices-for-git-integration)합니다.

1. GIT 모드에서는 포털에서 게시할 때마다 팩터리의 Resource Manager 템플릿이 리포지토리의 adf_publish 분기에 있는 GIT에 저장됩니다.

1. 다른 시나리오의 경우 포털에서 **Resource Manager 템플릿 내보내기** 단추를 클릭하여 Resource Manager 템플릿을 다운로드할 수 있습니다.

1. Resource Manager 템플릿을 다운로드한 후에 표준 Resource Manager 템플릿 배포 방법을 통해 배포할 수 있습니다.

1. 보안상의 이유로, 생성된 Resource Manager 템플릿에는 연결된 서비스에 대한 암호와 같은 기밀 정보가 포함되어 있지 않습니다. 따라서 이러한 암호를 배포 매개 변수로 제공해야 합니다. 매개 변수를 제공하지 않으려면 Azure Key Vault에서 연결된 서비스의 연결 문자열 및 암호를 가져와야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)에서 Azure Portal을 통해 데이터 팩터리를 만들기 위한 지침을 검토하세요.
