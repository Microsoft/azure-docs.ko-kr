---
title: Azure Pipelines를 사용 하 여 HPC 솔루션을 빌드 & 배포-Azure Batch
description: Azure Batch에서 실행 되는 HPC 응용 프로그램에 대한 빌드/릴리스 파이프라인을 배포 하는 방법을 알아봅니다.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: ee87cd7d80d4b24e8c52fb3c7dbb780d39071066
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935131"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure Pipelines를 사용 하 여 HPC 솔루션 빌드 및 배포

Azure DevOps 서비스는 사용자 지정 응용 프로그램을 빌드할 때 개발 팀에서 사용 하는 다양 한 도구를 제공 합니다. Azure DevOps에서 제공 하는 도구는 고성능 계산 솔루션의 자동화 된 빌드 및 테스트로 변환할 수 있습니다. 이 문서에서는 Azure Batch에 배포 된 고성능 계산 솔루션에 대한 Azure Pipelines를 사용 하 여 CI (지속적인 통합) 및 CD (연속 배포)를 설정 하는 방법을 보여 줍니다.

Azure Pipelines은 소프트웨어 빌드, 배포, 테스트 및 모니터링을 위한 다양 한 최신 CI/CD 프로세스를 제공 합니다. 이러한 프로세스는 소프트웨어 제공을 가속화 하 여 인프라 및 작업을 지원 하지 않고 코드에 집중할 수 있도록 합니다.

## <a name="create-an-azure-pipeline"></a>Azure 파이프라인 만들기

이 예제에서는 Azure Batch 인프라를 배포 하 고 응용 프로그램 패키지를 릴리스 하는 빌드 및 릴리스 파이프라인을 만듭니다. 코드를 로컬로 개발 하는 경우 일반적인 배포 흐름은 다음과 같습니다.

![파이프라인의 배포 흐름을 보여 주는 다이어그램](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>설치 프로그램

이 문서의 단계를 수행 하려면 Azure DevOps 조직과 팀 프로젝트가 필요 합니다.

* [Azure DevOps 조직 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure DevOps에서 프로젝트 만들기](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>사용자 환경에 대한 소스 제어

소스 제어를 사용 하면 팀에서 코드 베이스에 대한 변경 내용을 추적 하 고 이전 버전의 코드를 검사할 수 있습니다.

일반적으로 소스 제어는 소프트웨어 코드와 직접 관련 된 것으로 간주 됩니다. 기본 인프라는 어떻게 되나요? 이렇게 하면 인프라를 코드로 사용 하 여 기본 인프라를 선언적으로 정의 하는 Azure Resource Manager 템플릿 또는 다른 오픈 소스 대안을 사용할 수 있습니다.

이 샘플은 많은 리소스 관리자 템플릿 (JSON 문서) 및 기존 이진 파일에 의존 합니다. 이러한 예제를 리포지토리로 복사 하 여 Azure DevOps에 푸시할 수 있습니다.

이 샘플에서 사용 되는 코드 베이스 구조는 다음과 유사 합니다.

* 여러 Azure Resource Manager 템플릿을 포함 하는 **arm 템플릿** 폴더입니다. 템플릿은이 문서에 설명 되어 있습니다.
* [Ffmpeg 샘플을 사용한 .Net 파일 처리 Azure Batch](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) 의 복사본 인 **클라이언트 응용 프로그램** 폴더입니다. 이 문서에서는 필요 하지 않습니다.
* Windows 64 비트 버전의 [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)인 **hpc 응용 프로그램** 폴더입니다.
* **파이프라인** 폴더입니다. 여기에는 빌드 프로세스의 개요를 표시 하는 YAML 파일이 포함 됩니다. 이에 대해서는이 문서에서 설명 합니다.

이 섹션에서는 버전 제어 및 리소스 관리자 템플릿 디자인에 대해 잘 알고 있다고 가정 합니다. 이러한 개념을 잘 모르는 경우 자세한 내용은 다음 페이지를 참조 하세요.

* [원본 제어 란?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager 템플릿

이 예제에서는 여러 리소스 관리자 템플릿을 활용 하 여 솔루션을 배포 합니다. 이렇게 하려면 특정 기능을 구현 하는 다양 한 기능 템플릿 (단위 또는 모듈과 유사)을 사용 합니다. 또한 이러한 기본 기능을 함께 사용 하는 종단 간 솔루션 템플릿을 사용 합니다. 이 방법에는 다음과 같은 몇 가지 이점이 있습니다.

* 기본 기능 템플릿은 개별적으로 단위 테스트할 수 있습니다.
* 기본 기능 템플릿은 조직 내부의 표준으로 정의 하 고 여러 솔루션에서 다시 사용할 수 있습니다.

이 예제에서는 세 가지 템플릿을 배포 하는 종단 간 솔루션 템플릿 (배포. json)이 있습니다. 기본 템플릿은 솔루션의 특정 측면 배포를 담당 하는 기능 템플릿입니다.

![Azure Resource Manager 템플릿을 사용 하는 연결 된 템플릿 구조의 예](media/batch-ci-cd/ARMTemplateHierarchy.png)

표시 되는 첫 번째 템플릿은 Azure Storage 계정에 대한 것입니다. Microsoft의 솔루션에는 Batch 계정에 응용 프로그램을 배포 하는 저장소 계정이 필요 합니다. 저장소 계정에 대한 리소스 관리자 템플릿을 빌드할 때 [Microsoft 저장소 리소스 종류에 대한 리소스 관리자 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 를 알고 있어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

다음으로 Azure Batch 계정 템플릿을 살펴보겠습니다. Azure Batch 계정은 풀 (컴퓨터 그룹)에서 다양 한 응용 프로그램을 실행 하는 플랫폼 역할을 합니다. Batch 계정에 대한 리소스 관리자 템플릿을 작성할 때 [Microsoft batch 리소스 종류에 대한 리소스 관리자 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) 를 알고 있어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

다음 템플릿에서는 Azure Batch 풀 (응용 프로그램을 처리 하는 백 엔드 컴퓨터)을 만드는 예제를 보여 줍니다. Batch 계정 풀의 리소스 관리자 템플릿을 작성할 때 [Microsoft batch 리소스 유형에 대한 리소스 관리자 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) 를 알고 있어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

마지막으로, orchestrator와 유사 하 게 작동 하는 템플릿이 있습니다. 이 템플릿은 기능 템플릿 배포를 담당 합니다.

별도의 문서에서 [연결 된 Azure Resource Manager 템플릿을 만드는](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) 방법에 대해 자세히 알아볼 수도 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC 솔루션

인프라 및 소프트웨어를 동일한 리포지토리에서 코드 및 공동 배치 정의할 수 있습니다.

이 솔루션의 경우 ffmpeg는 응용 프로그램 패키지로 사용 됩니다. Ffmpeg 패키지는 [여기](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)에서 다운로드할 수 있습니다.

![예제 Git 리포지토리 구조](media/batch-ci-cd/git-repository.jpg)

이 리포지토리에는 네 가지 주요 섹션이 있습니다.

* 인프라를 코드로 저장 하는 **arm 템플릿** 폴더
* Ffmpeg의 이진 파일이 포함 된 **hpc 응용 프로그램** 폴더
* 빌드 파이프라인에 대한 정의를 포함 하는 **파이프라인** 폴더입니다.
* **선택 사항**: .net 응용 프로그램에 대한 코드를 저장 하는 **클라이언트 응용 프로그램** 폴더입니다. 샘플에서는이를 사용 하지 않지만 사용자의 프로젝트에서는 클라이언트 응용 프로그램을 통해 HPC Batch 응용 프로그램의 실행을 실행할 수 있습니다.

> [!NOTE]
> 이는 코드 베이스에 대한 구조체의 한 예제 일 뿐입니다. 이 방법은 응용 프로그램, 인프라 및 파이프라인 코드가 동일한 리포지토리에 저장 된다는 것을 보여 주는 용도로 사용 됩니다.

이제 소스 코드가 설정 되었으므로 첫 번째 빌드를 시작할 수 있습니다.

## <a name="continuous-integration"></a>연속 통합

Azure DevOps Services 내에서 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)는 응용 프로그램의 빌드, 테스트 및 배포 파이프라인을 구현 하는 데 도움이 됩니다.

파이프라인의이 단계에서는 일반적으로 코드의 유효성을 검사 하 고 적절 한 소프트웨어를 빌드하는 테스트를 실행 합니다. 테스트의 수와 유형 및 사용자가 실행 하는 모든 추가 작업은 광범위 한 빌드 및 릴리스 전략에 따라 달라 집니다.

## <a name="preparing-the-hpc-application"></a>HPC 응용 프로그램 준비

이 예제에서는 **hpc 응용 프로그램** 폴더에 초점을 둡니다. **Hpc 응용 프로그램** 폴더는 Azure Batch 계정에서 실행 되는 ffmpeg 소프트웨어입니다.

1. Azure DevOps 조직에서 Azure Pipelines의 빌드 섹션으로 이동 합니다. **새 파이프라인**을 만듭니다.

    ![새 빌드 파이프라인 만들기](media/batch-ci-cd/new-build-pipeline.jpg)

1. 빌드 파이프라인을 만들기 위한 두 가지 옵션이 있습니다.

    a. [비주얼 디자이너 사용](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). 이를 사용 하려면 **새 파이프라인** 페이지에서 "비주얼 디자이너 사용"을 클릭 합니다.

    b. [YAML 빌드 사용](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). 새 파이프라인 페이지에서 Azure Repos 또는 GitHub 옵션을 클릭 하 여 새 YAML 파이프라인을 만들 수 있습니다. 또는 소스 제어에 아래 예제를 저장 하 고 비주얼 디자이너를 클릭 한 다음 YAML 템플릿을 사용 하 여 기존 YAML 파일을 참조할 수 있습니다.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 빌드를 필요에 따라 구성한 후 **& 큐에 저장**을 선택 합니다. **트리거** 섹션에서 연속 통합을 사용 하도록 설정한 경우 리포지토리에 대한 새 커밋을 만들 때 빌드가 자동으로 트리거되고 빌드에 설정 된 조건이 충족 됩니다.

    ![기존 빌드 파이프라인의 예](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure Pipelines의 **빌드** 섹션으로 이동 하 여 Azure devops에서 빌드의 진행 상황에 대한 실시간 업데이트를 확인 합니다. 빌드 정의에서 적절 한 빌드를 선택 합니다.

    ![빌드에서 라이브 출력 보기](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 클라이언트 응용 프로그램을 사용 하 여 HPC Batch 응용 프로그램을 실행 하는 경우 해당 응용 프로그램에 대한 별도의 빌드 정의를 만들어야 합니다. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) 설명서에서 다양 한 방법 가이드를 찾을 수 있습니다.

## <a name="continuous-deployment"></a>연속 배포

응용 프로그램 및 기본 인프라를 배포 하는 데에도 사용 Azure Pipelines. [릴리스 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/release) 은 연속 배포를 사용 하 고 릴리스 프로세스를 자동화 하는 구성 요소입니다.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>응용 프로그램 및 기본 인프라 배포

인프라 배포와 관련 된 여러 단계가 있습니다. [연결 된 템플릿을](../azure-resource-manager/templates/linked-templates.md)사용할 때 이러한 템플릿은 공용 엔드포인트 (HTTP 또는 HTTPS)에서 액세스할 수 있어야 합니다. GitHub의 리포지토리입니다, Azure Blob Storage 계정 또는 다른 저장소 위치인 것일 수 있습니다. 업로드 된 템플릿 아티팩트는 개인 모드에서 보유할 수 있지만 특정 형식의 SAS (공유 액세스 서명) 토큰을 사용 하 여 액세스할 수 있으므로 안전 하 게 유지할 수 있습니다. 다음 예제에서는 Azure Storage blob에서 템플릿을 사용 하 여 인프라를 배포 하는 방법을 보여 줍니다.

1. **새 릴리스 정의**를 만들고 빈 정의를 선택 합니다. 그런 다음 새로 만든 환경의 이름을 파이프라인에 관련 된 항목으로 변경 해야 합니다.

    ![초기 릴리스 파이프라인](media/batch-ci-cd/Release-0.jpg)

1. HPC 응용 프로그램에 대한 출력을 가져오기 위해 빌드 파이프라인에 대한 종속성을 만듭니다.

    > [!NOTE]
    > 다시 한 번, 릴리스 정의 내에서 태스크를 만들 때 필요 하므로 **원본 별칭**을 기록해 둡니다.

    ![적절 한 빌드 파이프라인에서 HPCApplicationPackage에 대한 아티팩트 링크를 만듭니다.](media/batch-ci-cd/Release-1.jpg)

1. 이번에는 Azure 리포지토리의 다른 아티팩트에 대한 링크를 만듭니다. 이는 리포지토리에 저장 된 리소스 관리자 템플릿에 액세스 하는 데 필요 합니다. 리소스 관리자 템플릿에 컴파일이 필요 하지 않으므로 빌드 파이프라인을 통해 템플릿을 푸시할 필요가 없습니다.

    > [!NOTE]
    > 다시 한 번, 릴리스 정의 내에서 태스크를 만들 때 필요 하므로 **원본 별칭**을 기록해 둡니다.

    ![Azure Repos에 대한 아티팩트 링크 만들기](media/batch-ci-cd/Release-2.jpg)

1. **변수** 섹션으로 이동 합니다. 파이프라인에서 여러 변수를 만드는 것이 좋습니다. 따라서 여러 작업에 동일한 정보를 입력 하지 않습니다. 이러한 변수는이 예제에서 사용 되는 변수 및 배포에 영향을 주는 방식입니다.

    * **Applicationstorageaccountname**: HPC 응용 프로그램 이진 파일을 저장할 저장소 계정의 이름
    * **Batchaccountapplicationname**: Azure Batch 계정의 응용 프로그램 이름
    * **Batchaccountname**: Azure Batch 계정의 이름입니다.
    * **batchAccountPoolName**: 처리를 수행 하는 vm의 풀 이름입니다.
    * **Batchapplicationid**: Azure Batch 응용 프로그램의 고유 ID
    * **Batchapplicationversion**: batch 응용 프로그램의 의미 체계 버전 (즉, ffmpeg 이진 파일)
    * **위치**: 배포할 Azure 리소스의 위치
    * **resourceGroupName**: 만들 리소스 그룹의 이름 및 리소스를 배포할 위치
    * **Storageaccountname**: 연결 된 리소스 관리자 템플릿을 저장할 저장소 계정의 이름

    ![Azure Pipelines 릴리스에 대해 설정 된 변수의 예](media/batch-ci-cd/Release-4.jpg)

1. 개발 환경에 대한 작업으로 이동 합니다. 아래 스냅숏에서 6 개의 작업을 볼 수 있습니다. 이러한 작업은 압축 된 ffmpeg 파일을 다운로드 하 고, 중첩 된 리소스 관리자 템플릿을 호스트 하는 저장소 계정을 배포 하 고, 해당 리소스 관리자 템플릿을 저장소 계정에 복사 하 고, batch 계정 및 필요한 종속성을 배포 하 고,에서 응용 프로그램을 만듭니다. Azure Batch 계정으로 Azure Batch 계정에 응용 프로그램 패키지를 업로드 합니다.

    ![Azure Batch에 HPC 응용 프로그램을 릴리스 하는 데 사용 되는 작업의 예](media/batch-ci-cd/Release-3.jpg)

1. **파이프라인 아티팩트 다운로드 (미리 보기)** 작업을 추가 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** 에이전트에 ApplicationPackage 다운로드
    * **다운로드할 아티팩트의 이름:** hpc 응용 프로그램
    * **다운로드할 경로**: $ (system.defaultworkingdirectory)

1. 아티팩트를 저장할 저장소 계정을 만듭니다. 솔루션의 기존 저장소 계정을 사용할 수 있지만 자체 포함 된 샘플 및 콘텐츠 격리의 경우 아티팩트에 대한 전용 저장소 계정 (특히 리소스 관리자 템플릿)을 만듭니다.

    **Azure 리소스 그룹 배포** 작업을 추가 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** 리소스 관리자 템플릿에 대한 저장소 계정 배포
    * **Azure 구독:** 적절 한 Azure 구독을 선택 합니다.
    * **작업**: 리소스 그룹 만들기 또는 업데이트
    * **리소스 그룹**: $ (resourceGroupName)
    * **위치**: $ (location)
    * **템플릿**: $ (System.servicemodel sdirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **템플릿 매개 변수 재정의**:-accountName $ (storageaccountname)

1. 원본 제어의 아티팩트를 저장소 계정에 업로드 합니다. 이 작업을 수행 하는 Azure 파이프라인 태스크가 있습니다. 이 작업의 일환으로 저장소 계정 컨테이너 URL 및 SAS 토큰을 Azure Pipelines 변수에 출력할 수 있습니다. 이는이 에이전트 단계 전체에서 다시 사용할 수 있음을 의미 합니다.

    **Azure 파일 복사** 작업을 추가 하 고 다음 속성을 설정 합니다.
    * **원본:** $ (System.servicemodel sdirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure 연결 형식**: Azure Resource Manager
    * **Azure 구독:** 적절 한 Azure 구독을 선택 합니다.
    * **대상 유형**: Azure Blob
    * **RM 저장소 계정**: $ (storageaccountname)
    * **컨테이너 이름**: 템플릿
    * **저장소 컨테이너 URI**: templateContainerUri
    * **저장소 컨테이너 SAS 토큰**: templateContainerSasToken

1. Orchestrator 템플릿을 배포 합니다. 이전에서 orchestrator 템플릿을 회수할 때 SAS 토큰과 함께 저장소 계정 컨테이너 URL에 대한 매개 변수가 있다는 것을 알 수 있습니다. 리소스 관리자 템플릿에 필요한 변수가 릴리스 정의의 변수 섹션에 보관 되어 있거나, 다른 Azure Pipelines 작업 (예: Azure Blob 복사 작업의 일부)에서 설정 되었는지 확인 해야 합니다.

    **Azure 리소스 그룹 배포** 작업을 추가 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch 배포
    * **Azure 구독:** 적절 한 Azure 구독을 선택 합니다.
    * **작업**: 리소스 그룹 만들기 또는 업데이트
    * **리소스 그룹**: $ (resourceGroupName)
    * **위치**: $ (location)
    * **템플릿**: $ (System.servicemodel sdirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **템플릿 매개 변수 재정의**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

일반적인 방법은 Azure Key Vault 작업을 사용 하는 것입니다. 서비스 주체 (Azure 구독에 대한 연결)에 적절 한 액세스 정책이 설정 된 경우 Azure Key Vault에서 비밀을 다운로드 하 고 파이프라인에서 변수로 사용할 수 있습니다. 암호의 이름은 연결 된 값으로 설정 됩니다. 예를 들어 릴리스 정의에서 $ (sshPassword)를 사용 하 여 sshPassword의 암호를 참조할 수 있습니다.

1. 다음 단계는 Azure CLI를 호출 합니다. 첫 번째는 Azure Batch에서 응용 프로그램을 만드는 데 사용 됩니다. 연결 된 패키지를 업로드 합니다.

    **Azure CLI** 태스크를 추가 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch 계정에서 응용 프로그램 만들기
    * **Azure 구독:** 적절 한 Azure 구독을 선택 합니다.
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 두 번째 단계는 응용 프로그램에 연결 된 패키지를 업로드 하는 데 사용 됩니다. 이 경우 ffmpeg 파일입니다.

    **Azure CLI** 태스크를 추가 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch 계정에 패키지 업로드
    * **Azure 구독:** 적절 한 Azure 구독을 선택 합니다.
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 응용 프로그램 패키지의 버전 번호는 변수로 설정 됩니다. 이 기능은 이전 버전의 패키지를 덮어쓰는 경우 편리 하며, Azure Batch으로 푸시되는 패키지의 버전 번호를 수동으로 제어 하려는 경우에 편리 합니다.

1. **릴리스 > 새 릴리스를**선택 하 여 새 릴리스를 만듭니다. 트리거된 후 새 릴리스에 대한 링크를 선택 하 여 상태를 확인 합니다.

1. 환경 아래에 있는 **로그** 단추를 선택 하 여 에이전트에서 실시간 출력을 볼 수 있습니다.

    ![릴리스 상태 보기](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>환경 테스트

환경을 설정한 후에는 다음 테스트를 성공적으로 완료할 수 있는지 확인 합니다.

PowerShell 명령 프롬프트에서 Azure CLI를 사용 하 여 새 Azure Batch 계정에 연결 합니다.

* `az login`를 사용 하 여 Azure 계정에 로그인 하 고 지침에 따라 인증 합니다.
* 이제 Batch 계정 인증: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>사용 가능한 응용 프로그램 나열

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>풀이 올바른지 확인 하십시오.

```azurecli
az batch pool list
```

이 명령의 출력에서 `currentDedicatedNodes` 값을 확인 합니다. 이 값은 다음 테스트에서 조정 됩니다.

#### <a name="resize-the-pool"></a>풀 크기 조정

작업 및 태스크 테스트에 사용할 수 있는 계산 노드가 있도록 풀 크기를 조정 하 고, 크기 조정이 완료 되 고 사용 가능한 노드가 있는 경우 풀 목록 명령을 사용 하 여 현재 상태를 확인 합니다.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>다음 단계

이 문서 외에도 .NET 및 Python을 사용 하 여 ffmpeg를 활용 하는 두 가지 자습서가 있습니다. 간단한 응용 프로그램을 통해 Batch 계정과 상호 작용 하는 방법에 대한 자세한 내용은 다음 자습서를 참조 하세요.

* [Python API를 사용 하 여 Azure Batch에서 병렬 워크 로드 실행](tutorial-parallel-python.md)
* [.NET API를 사용 하 여 Azure Batch에서 병렬 워크 로드 실행](tutorial-parallel-dotnet.md)
