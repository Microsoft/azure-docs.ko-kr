---
title: 파이프라인 작업에서 Azure Key Vault 비밀 사용
description: Azure 주요 자격 증명 모음에서 저장 된 자격 증명을 가져와서 데이터 팩터리 파이프라인 실행 중에 사용 하는 방법을 알아봅니다.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200115"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>파이프라인 작업에서 Azure Key Vault 비밀 사용

Azure Key Vault에 자격 증명 또는 암호 값을 저장 하 고 파이프라인 실행 중에 사용 하 여 작업에 전달할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능은 데이터 팩터리 관리 id에 의존 합니다.  [Data Factory에 대 한 관리 id](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) 에서 작동 하는 방식에 대해 알아보고 데이터 팩터리가 연결 된 것이 있는지 확인 합니다.

## <a name="steps"></a>단계

1. 데이터 팩터리의 속성을 열고 관리 되는 Id 응용 프로그램 ID 값을 복사 합니다.

    ![관리 Id 값](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 키 자격 증명 모음 액세스 정책을 열고 관리 id 권한을 추가 하 여 비밀을 가져오고 나열 합니다.

    ![Key Vault 액세스 정책](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 액세스 정책](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    **추가**를 클릭 한 다음 **저장**을 클릭 합니다.

3. Key Vault 암호로 이동 하 여 비밀 식별자를 복사 합니다.

    ![비밀 식별자](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    데이터 팩터리 파이프라인을 실행 하는 동안 가져오려는 비밀 URI를 적어 둡니다.

4. Data Factory 파이프라인에서 새 웹 활동을 추가 하 고 다음과 같이 구성 합니다.  

    |속성  |값  |
    |---------|---------|
    |보안 출력     |True         |
    |URL     |[사용자의 비밀 URI 값]? api-version = 7.0         |
    |방법     |GET         |
    |인증     |MSI         |
    |리소스        |https://vault.azure.net       |

    ![웹 활동](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > **? Api-version = 7.0** 을 암호 URI의 끝에 추가 해야 합니다.  

    > [!CAUTION]
    > 보안 출력 옵션을 true로 설정 하 여 비밀 값이 일반 텍스트로 기록 되지 않도록 합니다.  이 값을 사용 하는 추가 활동의 경우 보안 입력 옵션을 true로 설정 해야 합니다.

5. 다른 작업의 값을 사용 하려면 다음 코드@activity식을 사용 합니다. **(' Web1 ').**

    ![코드 식](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>다음 단계

Azure Key Vault를 사용 하 여 데이터 저장소 및 계산을 위한 자격 증명을 저장 하는 방법을 알아보려면 [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 을 참조 하세요.
