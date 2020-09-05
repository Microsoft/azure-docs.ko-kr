---
title: Azure Data Factory의 지속적인 통합 및 지속적인 업데이트
description: 지속적인 통합 및 지속적인 업데이트를 사용하여 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 582a9eb4c98e89602e35e2ee424a00adc54a88e3
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229551"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory의 지속적인 통합 및 지속적인 업데이트

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>개요

지속적인 통합은 코드 베이스에 자동으로 이루어진 변경 내용을 각각 가능한 빨리 테스트하는 방법입니다. 지속적인 업데이트는 지속적인 통합 중에 발생하는 테스트를 수행하고, 변경 내용을 준비 또는 프로덕션 시스템에 푸시합니다.

Azure Data Factory에서 CI/CD(지속적인 통합 및 지속적인 업데이트)는 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 것입니다. Azure Data Factory는 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)을 활용하여 다양한 ADF 엔터티(파이프라인, 데이터 세트, 데이터 흐름 등)의 구성을 저장합니다. Data Factory를 다른 환경으로 승격시키는 두 가지 제안된 방법이 있습니다.

-    Data Factory와 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)의 통합을 사용한 자동화된 배포
-    Azure Resource Manager와 Data Factory UX의 통합을 사용하여 Resource Manager 템플릿을 수동으로 업로드합니다.

이 기능의 소개와 데모를 보려면 다음 동영상(9분)을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 수명 주기

다음은 Azure Repos Git로 구성된 Azure Data Factory의 CI/CD 수명 주기에 대한 샘플 개요입니다. Git 리포지토리를 구성하는 방법에 대한 자세한 내용은 [Azure Data Factory에서 원본 제어](source-control.md)를 참조하세요.

1.  Azure Repos Git를 사용하여 개발 Data Factory를 만들어 구성합니다. 모든 개발자는 파이프라인 및 데이터 세트와 같은 Data Factory 리소스를 작성할 수 있는 권한이 있어야 합니다.

1.  개발자는 [기능 분기를 만들어](source-control.md#creating-feature-branches) 변경합니다. 가장 최근 변경 내용으로 파이프라인 실행을 디버그합니다. 파이프라인 실행을 디버그하는 방법에 대한 자세한 내용은 [Azure Data Factory를 사용한 반복 개발 및 디버깅](iterative-development-debugging.md)을 참조하세요.

1.  개발자가 해당 변경 내용을 충족하면 해당 기능 분기에서 마스터 또는 협업 분기로 끌어오기 요청을 만들어서 피어에서 해당 변경 내용을 검토할 수 있습니다.

1.  끌어오기 요청을 승인하고 변경 내용을 마스터 분기에서 병합한 후에 변경 내용이 개발 팩터리에 게시됩니다.

1.  팀이 테스트 또는 UAT (사용자 수용 테스트) 팩터리에 대 한 변경 내용을 배포할 준비가 되 면 팀은 해당 Azure Pipelines 릴리스로 이동 하 여 원하는 버전의 개발 팩터리를 UAT에 배포 합니다. 이 배포는 Azure Pipelines 작업의 일부로 발생하고 Resource Manager 템플릿 매개 변수를 사용하여 적절한 구성을 적용합니다.

1.  테스트 팩터리에서 변경 내용을 확인한 후 파이프라인 릴리스의 다음 작업을 사용하여 프로덕션 팩터리에 배포합니다.

> [!NOTE]
> 개발 팩터리만이 Git 리포지토리에 연결됩니다. 테스트 및 프로덕션 팩터리는 Git 리포지토리가 연결되어 있지 않아야 하며 Azure DevOps 파이프라인 또는 리소스 관리 템플릿을 통해서 업데이트해야 합니다.

아래 이미지에는 이 수명 주기의 여러 단계가 강조 표시되어 있습니다.

![Azure Pipelines를 사용하는 지속적인 통합 다이어그램](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure Pipelines 릴리스를 사용하여 지속적인 통합 자동화

다음은 여러 환경에 Data Factory를 자동 배포하는 Azure Pipelines 릴리스를 설정하기 위한 가이드입니다.

### <a name="requirements"></a>요구 사항

-    [Azure Resource Manager 서비스 엔드포인트](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)를 사용하는 Azure Repos 또는 Visual Studio Team Foundation Server에 연결된 Azure 구독

-   Azure Repos Git 통합으로 구성된 Data Factory

-   각 환경의 비밀이 포함된  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)입니다.

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  [Azure DevOps](https://dev.azure.com/)에서 Data Factory로 구성된 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인**을 선택한 다음 **릴리스**를 선택합니다.

    ![파이프라인, 릴리스 선택](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **새 파이프라인**을 선택하거나, 기존 파이프라인이 있는 경우에는 **새로 만들기**를 선택한 다음 **새 릴리스 파이프라인**을 선택합니다.

1.  **빈 작업** 템플릿을 선택합니다.

    ![빈 작업 선택](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **단계 이름** 상자에 사용자 환경의 이름을 입력합니다.

1.  **아티팩트 추가**를 선택한 다음 개발 Data Factory로 구성된 Git 리포지토리를 선택합니다. **기본 분기**에 대한 리포지토리의 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 이 게시 분기는 `adf_publish`입니다. **기본 버전**의 경우 **기본 분기에서 최신 버전**을 선택합니다.

    ![아티팩트 추가](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  단계 보기에서 **단계 작업 보기**를 선택합니다.

    ![단계 보기](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  새 작업을 만듭니다. **ARM 템플릿 배포**를 검색 한 다음 **추가**를 선택 합니다.

    다.  배포 작업에서 대상 Data Factory에 대한 구독, 리소스 그룹 및 위치를 선택합니다. 필요한 경우 자격 증명을 제공합니다.

    d.  **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트**를 선택합니다.

    e.  **템플릿** 상자 옆에 있는 줄임표 단추( **...** )를 선택합니다. 구성된 Git 리포지토리의 게시 분기에 생성된 Azure Resource Manager 템플릿을 찾습니다. adf_publish 분기의 <FactoryName> 폴더에서 `ARMTemplateForFactory.json` 파일을 찾습니다.

    f.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다. adf_publish 분기의 <FactoryName> 폴더에서 `ARMTemplateParametersForFactory.json` 파일을 찾습니다.

    g.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에서 대상 Data Factory에 대해 원하는 매개 변수 값을 입력합니다. Azure Key Vault에서 제공하는 자격 증명의 경우 큰따옴표 사이에 비밀 이름을 입력합니다. 예를 들어 비밀 이름이 cred1인 경우 이 값에 대한 **"$(cred1)"** 을 입력합니다.

    h. **배포 모드**에 대해 **증분**을 선택합니다.

    > [!WARNING]
    > 전체 배포 모드에서는 리소스 그룹에 있지만 새 리소스 관리자 템플릿에 지정 되지 않은 리소스는 **삭제**됩니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../azure-resource-manager/templates/deployment-modes.md) 를 참조 하세요.

    ![Data Factory Prod 배포](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  릴리스 파이프라인을 저장합니다.

1. 릴리스를 트리거하려면 **릴리스 만들기**를 선택합니다. 릴리스 만들기를 자동화하려면 [Azure DevOps 릴리스 트리거](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)를 참조하세요.

   ![릴리스 만들기 선택](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경에서의 IR(통합 런타임) 형식이 동일해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서 동일한 IR이 자체 호스팅 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 연결된 자체 호스팅으로 구성해야 합니다.

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault에서 비밀을 가져옵니다.

Azure Resource Manager 템플릿에 전달할 비밀이 있는 경우 Azure Pipelines 릴리스에서 Azure Key Vault를 사용하는 것이 좋습니다.

비밀을 처리하는 두 가지 방법이 있습니다.

1.  매개 변수 파일에 비밀을 추가합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)을 참조하세요.

    게시 분기에 업로드되는 매개 변수 파일의 복사본을 만듭니다. 다음 형식을 사용하여 Key Vault에서 가져오려는 매개 변수의 값을 설정합니다.

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    이 메서드를 사용하는 경우 키 자격 증명 모음에서 자동으로 암호를 끌어옵니다.

    매개 변수 파일은 게시 분기에 포함되어야 합니다.

1. 이전 섹션에 설명된 Azure Resource Manager 배포 작업 전에 [Azure Key Vault 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)을 추가합니다.

    1.  **작업** 탭에서 새 작업을 만듭니다. **Azure Key Vault**를 검색하여 추가합니다.

    1.  Key Vault 작업에서 키 자격 증명 모음을 만든 구독을 선택합니다. 필요한 경우 자격 증명을 제공하고 키 자격 증명 모음을 선택합니다.

    ![Key Vault 작업 추가](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여

올바른 권한이 설정되어 있지 않으면 액세스 거부 오류가 발생하여 Azure Key Vault 작업이 실패할 수 있습니다. 릴리스에 대한 로그를 다운로드하고, Azure Pipelines 에이전트에 권한을 부여하는 명령을 포함하는 .ps1 파일을 찾습니다. 명령을 직접 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사하고 Azure Portal에 액세스 정책을 수동으로 추가할 수 있습니다. `Get` 및 `List`는 필요한 최소 권한입니다.

### <a name="updating-active-triggers"></a>활성 트리거 업데이트

활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트하려면 수동으로 중단하고 배포 후에 시작해야 합니다. Azure PowerShell 작업을 사용하여 이 작업을 수행할 수 있습니다.

1.  릴리스의 **작업** 탭에서 **Azure PowerShell** 작업을 추가합니다. 작업 버전 4.*를 선택합니다. 

1.  팩터리가 있는 구독을 선택합니다.

1.  스크립트 형식으로 **스크립트 파일 경로**를 선택합니다. 이를 위해서는 PowerShell 스크립트를 리포지토리에 저장해야 합니다. 다음 PowerShell 스크립트를 사용하여 트리거를 중지할 수 있습니다.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

`Start-AzDataFactoryV2Trigger` 함수로 유사한 단계를 완료하여 배포한 후에 트리거를 다시 시작할 수 있습니다.

이 문서의 맨 아래에 Data Factory 팀에서 제공한 [샘플 배포 전/후 스크립트](#script)가 있습니다. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>각 환경에 Resource Manager 템플릿을 수동으로 승격합니다.

1. **ARM 템플릿** 목록에서 **ARM 템플릿 내보내기**를 선택하여 개발 환경에서 Data Factory에 Resource Manager 템플릿을 내보냅니다.

   ![Resource Manager 템플릿 내보내기](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 테스트 및 프로덕션 Data Factory에서 **ARM 템플릿 가져오기**를 선택합니다. 이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 사용자 고유의 템플릿 빌드**를 선택하여 Resource Manager 템플릿 편집기를 엽니다.

   ![사용자 고유의 템플릿 빌드](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **파일 로드**를 선택하고 생성된 Resource Manager 템플릿을 선택합니다. 이는 1단계에서 내보낸 .zip 파일에 있는 **arm_template.json** 파일입니다.

   ![템플릿 편집](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 설정 섹션에서 연결된 서비스 자격 증명과 같은 구성 값을 입력합니다. 완료되면 **구매**를 선택하여 Resource Manager 템플릿을 배포합니다.

   ![설정 섹션](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

개발 팩터리에 연결된 Git 리포지토리가 있는 경우 템플릿을 게시하거나 내보내 생성된 Resource Manager 템플릿의 기본 Resource Manager 템플릿 매개 변수를 재정의할 수 있습니다. 다음과 같은 시나리오에서 기본 매개 변수화 템플릿을 재정의할 수 있습니다.

* 자동화 CI/CD를 사용하고 Resource Manager 배포 중에 일부 속성을 변경하려고 하지만 속성은 기본적으로 매개 변수화되지 않습니다.
* 팩터리가 너무 커서 허용되는 최대 매개 변수(256)를 초과했기 때문에 기본 Resource Manager 템플릿이 유효하지 않습니다.

기본 매개 변수화 템플릿을 재정의하려면 Git 분기의 루트 폴더에 **arm-template-parameters-definition.json**으로 이름을 지정하여 파일을 만듭니다. 정확한 파일 이름을 사용해야 합니다.

   ![사용자 지정 매개 변수 파일](media/continuous-integration-deployment/custom-parameters.png)

협업 분기에서 게시하는 경우 Data Factory가 이 파일을 읽고 해당 구성을 사용하여 매개 변수화된 속성을 생성합니다. 파일이 없는 경우 기본 템플릿이 사용됩니다.

Resource Manager 템플릿을 내보낼 때 Data Factory는 협업 분기뿐만 아니라 현재 작업 중인 모든 분기에서 이 파일을 읽습니다. 프라이빗 분기에서 파일을 만들거나 편집할 수 있습니다. 여기서 UI의 **ARM 템플릿 내보내기**를 선택하여 변경 내용을 테스트할 수 있습니다. 그런 다음 협업 분기에 파일을 병합할 수 있습니다.

> [!NOTE]
> 사용자 지정 매개 변수화 템플릿에서 ARM 템플릿 매개 변수 제한(256)을 변경하지 않습니다. 이를 통해 매개 변수화된 속성의 개수를 선택하고 줄일 수 있습니다.

### <a name="custom-parameter-syntax"></a>사용자 지정 매개 변수 구문

다음은 사용자 지정 매개 변수 파일 **arm-template-parameters-definition.json**을 만들 때 따라야 할 지침입니다. 이 파일은 각 엔터티 형식에 대한 섹션(트리거, 파이프라인, 연결된 서비스, 데이터 세트, 통합 런타임 및 데이터 흐름)으로 구성되어 있습니다.

* 관련 엔터티 형식 아래에 속성 경로를 입력합니다.
* 속성 이름을  `*`로 설정하면 해당 속성 아래(재귀적이 아닌 첫 번째 수준만)의 모든 속성을 매개 변수화합니다. 이 구성에 대한 예외를 제공할 수도 있습니다.
* 문자열로 속성의 값을 설정하면 속성을 매개 변수화하려고 함을 나타냅니다.  `<action>:<name>:<stype>` 양식을 사용합니다.
   *  `<action>`은 다음 문자 중 하나일 수 있습니다.
      * `=`는 매개 변수에 대한 기본값으로 현재 값을 유지하는 것을 의미합니다.
      * `-`는 매개 변수에 대한 기본값을 유지하지 않는 것을 의미합니다.
      * `|`는 연결 문자열 또는 키에 대한 Azure Key Vault에서 비밀에 대한 특수 사례입니다.
   * `<name>`은 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용합니다. 값이 `-` 문자로 시작하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString`에서 `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>`은 매개 변수의 유형입니다.  `<stype>`이 비어 있는 경우 기본 유형은 `string`입니다. 지원되는 값은 `string`, `bool`, `number`, `object`, `securestring`입니다.
* 정의 파일에서 배열을 지정하면 템플릿의 일치하는 속성이 배열임을 나타냅니다. Data Factory는 배열의 Integration Runtime 개체에 지정된 정의를 사용하여 배열에 있는 모든 개체를 반복합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용됩니다.
* 기본적으로 Key Vault 비밀과 같은 모든 보안 문자열과 연결 문자열, 키, 토큰 등의 보안 문자열은 매개 변수화됩니다.
 
### <a name="sample-parameterization-template"></a>샘플 매개 변수화 템플릿

매개 변수화 템플릿의 예는 다음과 같습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
다음은 리소스 종류별로 분할된 이전 템플릿의 구성 방법에 대한 설명입니다.

#### <a name="pipelines"></a>파이프라인
    
* `activities/typeProperties/waitTimeInSeconds` 경로에 있는 모든 속성은 매개 변수화됩니다. `waitTimeInSeconds`라는 코드 수준 속성(예: `Wait` 작업)이 있는 파이프라인의 모든 작업은 기본 이름을 가진 숫자로 매개 변수화됩니다. 그러나 Resource Manager 템플릿에는 기본값이 없습니다. Resource Manager 배포 중에 입력해야 할 필수 항목입니다.
* 마찬가지로 `headers`(예: `Web` 작업)라는 속성은 형식 `object`(JObject)로 매개 변수화됩니다. 이 값은 원본 팩터리의 값과 동일한 기본값을 갖습니다.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용하여 매개 변수화됩니다. 예를 들어 `IntegrationRuntimes` 형식 속성에는 `computeProperties` 및 `ssisProperties`의 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형(Object)으로 만들어집니다.

#### <a name="triggers"></a>트리거

* `typeProperties`에서 두 개의 속성은 매개 변수화됩니다. 첫 번째 값은 기본값을 갖도록 지정된 `maxConcurrency`이며 `string` 형식입니다. 이 값은 기본 매개 변수 이름 `<entityName>_properties_typeProperties_maxConcurrency`를 갖습니다.
* `recurrence` 속성도 매개 변수화됩니다. 이 속성 아래 수준의 모든 속성은 문자열로 매개 변수화되어 기본값 및 매개 변수 이름으로 지정됩니다. 단, `number` 형식으로 매개 변수화되는 `interval` 속성은 예외입니다. 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`가 접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름이 단축되고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>LinkedServices

* 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 세트에는 다양한 형식이 있어 형식별로 사용자 지정할 수 있습니다. 이 예에서는 `AzureDataLakeStore` 형식의 연결된 모든 서비스에 대해 특정 템플릿이 적용됩니다. `*`을 통한 다른 모든 서비스에 대해서는 다른 템플릿이 적용됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화됩니다. 기본값이 없습니다. `connectionString`이 접미사로 사용되는 축약된 매개 변수 이름이 포함됩니다.
* 속성 `secretAccessKey`는 `AzureKeyVaultSecret`(예: Amazon S3에 연결된 서비스)가 됩니다. 자동으로 Azure Key Vault 비밀로 매개 변수화되고 구성된 키 자격 증명 모음에서 페치됩니다. 키 자격 증명 모음을 자체적으로 매개 변수화할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 세트에 대해 형식별 사용자 지정을 사용할 수 있지만, 명시적으로 \* 수준으로 구성하지 않고도 구성할 수 있습니다. 이전 예에서 `typeProperties` 아래의 모든 데이터 세트 속성은 매개 변수화됩니다.

### <a name="default-parameterization-template"></a>기본 매개 변수화 템플릿

다음은 현재의 기본 매개 변수화 템플릿입니다. 몇 개의 매개 변수만 추가해야 하는 경우 기존 매개 변수화 구조를 유지하므로 이 템플릿을 직접 편집하는 것이 좋습니다.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    }
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>예: 기존 Azure Databricks 대화형 클러스터 ID 매개 변수화

다음 예에서는 기본 매개 변수화 템플릿에 단일 값을 추가하는 방법을 보여 줍니다. Databricks 연결된 서비스의 기존 Azure Databricks 대화형 클러스터 ID를 매개 변수 파일에 추가하려고 합니다. 이 파일은 `Microsoft.DataFactory/factories/linkedServices`의 속성 필드에 `existingClusterId`를 추가하는 것을 제외하고 이전 파일과 동일합니다.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>연결된 Resource Manager 템플릿

Data Factory에 CI/CD를 설정한 경우 팩터리가 증가함에 따라 Azure Resource Manager 템플릿 한도를 초과할 수 있습니다. 예를 들어 한 한도는 Resource Manager 템플릿의 최대 리소스 수입니다. 팩터리에 대한 전체 Resource Manager 템플릿을 생성하면서 대형 팩터리를 수용하기 위해 Data Factory가 이제 연결된 Resource Manager 템플릿을 생성합니다. 이 기능을 사용하면 전체 팩터리 페이로드가 여러 파일로 분할되므로 한도에 제한되지 않습니다.

Git를 구성한 경우에는 연결된 템플릿이 생성되어 linkedTemplates라는 새 폴더의 adf_publish 분기에 전체 Resource Manager 템플릿과 함께 저장됩니다.

![연결된 Resource Manager 템플릿 폴더](media/continuous-integration-deployment/linked-resource-manager-templates.png)

연결된 Resource Manager 템플릿은 일반적으로 마스터 템플릿과 마스터에 연결된 자식 템플릿 세트로 구성됩니다. 부모 템플릿은 ArmTemplate_master.json, 자식 템플릿은 ArmTemplate_0.json, ArmTemplate_1.json 등의 패턴으로 이름이 지정됩니다. 

전체 Resource Manager 템플릿 대신 연결된 템플릿을 사용하려면 ArmTemplateForFactory.json(전체 Resource Manager 템플릿)이 아닌 ArmTemplate_master.json을 가리키도록 CI/CD 작업을 업데이트합니다. 또한 Resource Manager에서는 Azure가 배포하는 동안 액세스할 수 있도록 연결된 템플릿을 스토리지 계정에 업로드해야 합니다. 자세한 내용은 [VSTS로 연결된 Resource Manager 템플릿 배포](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)를 참조하세요.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git가 구성되지 않은 경우 **ARM 템플릿** 목록에서 **ARM 템플릿 내보내기**를 통해 연결된 템플릿에 액세스할 수 있습니다.

## <a name="hotfix-production-environment"></a>핫픽스 프로덕션 환경

팩터리를 프로덕션에 배포하고 즉시 해결해야 하는 버그가 있지만 현재의 협업 분기를 배포할 수 없는 경우 핫픽스를 배포해야 할 수 있습니다. 이 접근 방식을 QFE(Quick-Fix Engineering)라고 합니다.

1.    Azure DevOps에서 프로덕션에 배포된 릴리스로 이동합니다. 배포된 마지막 커밋을 찾습니다.

2.    커밋 메시지에서 협업 분기의 커밋 ID를 가져옵니다.

3.    해당 커밋에서 새 핫픽스 분기를 만듭니다.

4.    Azure Data Factory UX로 이동하고 핫픽스 분기로 전환합니다.

5.    Azure Data Factory UX를 사용하여 버그를 수정합니다. 변경 내용을 테스트합니다.

6.    수정 사항을 확인한 후 **ARM 템플릿 내보내기**를 선택하여 핫픽스 Resource Manager 템플릿을 가져옵니다.

7.    adf_publish 분기에서 이 빌드를 수동으로 확인합니다.

8.    adf_publish 체크 인에 따라 자동으로 트리거되도록 릴리스 파이프라인을 구성한 경우 새 릴리스가 자동으로 시작됩니다. 그렇지 않은 경우 수동으로 릴리스를 큐에 넣습니다.

9.    핫픽스 릴리스를 테스트 및 프로덕션 팩터리에 배포합니다. 이 릴리스에는 이전 프로덕션 페이로드와 5단계에서 만든 수정 사항이 포함되어 있습니다.

10.   이후 릴리스에 동일한 버그가 포함되지 않도록 핫픽스의 변경 내용을 개발 분기에 추가합니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Data Factory를 통해 Git 통합을 사용할 때 개발에서 테스트, 프로덕션 순서로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 적용하는 것이 좋습니다.

-   **Git 통합**. Git 통합으로 개발 Data Factory를 구성합니다. 테스트 및 프로덕션에 대한 변경 내용은 CI/CD를 통해 배포되므로 Git 통합이 필요 없습니다.

-   **배포 전 및 배포 후 스크립트**. CI/CD의 Resource Manager 배포 단계 전에 트리거를 중지, 다시 시작, 정리를 수행하는 등의 특정 작업을 완료해야 합니다. 배포 작업 전후에 PowerShell 스크립트를 사용하는 것이 좋습니다. 자세한 내용은 [활성 트리거 업데이트](#updating-active-triggers)를 참조하세요. 이 페이지의 맨 아래에 있는 Data Factory 팀에서 [제공한 스크립트](#script)를 사용할 수 있습니다.

-   **통합 런타임 및 공유**. 통합 런타임은 자주 변경되지 않으며 CI/CD의 모든 단계에서 유사합니다. 따라서 Data Factory에서는 CI/CD의 모든 단계에서 동일한 이름 및 유형의 통합 런타임을 사용해야 합니다. 모든 단계에서 통합 런타임을 공유하려면 공유 통합 런타임을 포함하기 위해 3개로 구성된 팩터리를 사용하는 것이 좋습니다. 모든 환경에서 이 공유 팩터리를 연결된 통합 런타임 형식으로 사용할 수 있습니다.

-   **Key Vault**. 연결 정보가 Azure Key Vault에 저장되어 있는 연결된 서비스를 사용하는 경우 다른 환경에 대해 별도의 키 자격 증명 모음을 유지하는 것이 좋습니다. 또한 각각의 키 자격 증명 모음에 대해 개별 권한 수준을 구성할 수도 있습니다. 예를 들어 팀 멤버에게 프로덕션 비밀에 대한 사용 권한을 부여하지 않을 수 있습니다. 이 접근 방식을 따를 경우 모든 단계에서 동일한 비밀 이름을 유지하는 것이 좋습니다. 동일한 비밀 이름을 유지하는 경우, 별도의 매개 변수인 키 자격 증명 모음 이름이 유일하게 변경되므로 CI/CD 환경에서 각 연결 문자열을 매개 변수화할 필요가 없습니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

- 기본적으로 Data Factory는 커밋의 cherry-pick 또는 리소스의 선택적 게시를 허용할 수 없습니다. 게시에는 Data Factory에서의 모든 변경 내용이 포함됩니다.

    - Data Factory 엔터티는 서로 종속됩니다. 예를 들어, 트리거는 파이프라인에 종속되고, 파이프라인은 데이터 세트 및 다른 파이프라인에 종속됩니다. 리소스 하위 집합을 선택적으로 게시하면 예기치 않은 동작 및 오류가 발생할 수 있습니다.
    - 선택적으로 게시해야 하는 경우 핫픽스를 사용하는 것이 좋습니다. 자세한 내용은 [핫픽스 프로덕션 환경](#hotfix-production-environment)을 참조 하세요.

- Azure Data Factory 팀에서는 데이터 팩터리의 개별 엔터티 (파이프라인, 데이터 집합 등)에 RBAC 컨트롤을 할당 하지 않는 것이 좋습니다. 예를 들어 개발자가 파이프라인 또는 데이터 집합에 액세스할 수 있는 경우 데이터 팩터리의 모든 파이프라인 또는 데이터 집합에 액세스할 수 있어야 합니다. 데이터 팩터리 내에서 많은 RBAC 역할을 구현 해야 하는 경우 두 번째 데이터 팩터리 배포를 살펴보세요.

-   프라이빗 분기에서 게시할 수 없습니다.

-   현재 Bitbucket에서 프로젝트를 호스트할 수 없습니다.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> 샘플 배포 전 및 배포 후 스크립트

다음 샘플 스크립트를 사용하여 배포 전에 트리거를 중지하고 나중에 다시 시작할 수 있습니다. 스크립트에는 제거된 리소스를 삭제하는 코드도 포함됩니다. 스크립트를 Azure DevOps Git 리포지토리에 저장하고 버전 4.*를 사용하여 Azure PowerShell 작업을 통해 해당 스크립트를 참조합니다.

배포 전 스크립트를 실행하는 경우 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


배포 후 스크립트를 실행하는 경우 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 작업](media/continuous-integration-deployment/continuous-integration-image11.png)

다음은 배포 전과 배포 후에 사용할 수 있는 스크립트입니다. 이는 삭제된 리소스 및 리소스 참조에 대한 스크립트입니다.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
