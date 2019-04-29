---
title: HPC 솔루션-Azure Batch 빌드 및 배포를 Azure 파이프라인을 사용 하 여 | Microsoft Docs
description: Azure Batch에서 실행 중인 HPC 응용 프로그램에 대 한 빌드/릴리스 파이프라인을 배포 하는 방법에 알아봅니다.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880949"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>HPC 솔루션 빌드 및 배포 하려면 Azure 파이프라인을 사용 합니다.

Azure DevOps 서비스는 다양 한 사용자 지정 응용 프로그램을 빌드할 때 개발 팀에서 사용 되는 도구를 제공 합니다. Azure DevOps에서 제공 하는 도구는 자동으로 빌드 및 고성능 컴퓨팅 솔루션의 테스트로 변환할 수 있습니다. 이 문서는 CI (지속적인 통합) 및 CD (지속적인 배포)를 사용 하 여 높은 성능을 위해 Azure 파이프라인 계산 Azure Batch에서 배포 된 솔루션을 설정 하는 방법을 보여 줍니다.

Azure 파이프라인 구축, 배포, 테스트 및 모니터링 소프트웨어에 대 한 다양 한 최신 CI/CD 프로세스를 제공 합니다. 이러한 프로세스에는 인프라 및 작업을 지원 하지 않고 코드에 집중할 수 있도록 하 여 소프트웨어 배달 가속화 하세요.

## <a name="create-an-azure-pipeline"></a>Azure 파이프라인을 만듭니다.

이 예제에서는 빌드 만들기를 릴리스 파이프라인을 Azure Batch 인프라 배포 및 응용 프로그램 패키지를 릴리스 합니다. 가정 하 고 코드를 로컬로 개발 하 고,이 일반 배포 흐름:

![파이프라인에서 배포의 흐름을 보여 주는 다이어그램](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>설정

이 문서의 단계를 수행 하려면 Azure DevOps 조직 및 팀 프로젝트를 필요 합니다.

* [Azure DevOps 조직 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure devops에서 프로젝트 만들기](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>사용자 환경에 대 한 소스 제어

소스 제어 코드 베이스에 대 한 변경 내용을 추적 하 고 이전 버전의 코드를 검사 하는 팀을 허용 합니다.

일반적으로 원본 제어 소프트웨어 코드를 사용 하 여 손에서 직접 생각 됩니다. 기본 인프라 어떤가요? 이 사용 하 여 Azure Resource Manager 템플릿 또는 기타 오픈 소스 대안 기본 인프라를 선언적으로 정의 하는 코드와 인프라를 제공 합니다.

이 샘플 Resource Manager 템플릿 (JSON 문서) 및 기존 이진 파일의 수를 크게 의존합니다. 리포지토리로 이러한 예제를 복사 하 고 Azure DevOps를 적용할 수 수 있습니다.

이 샘플에서 사용 되는 코드 베이스 구조는; 다음과 유사 합니다.

* **arm 템플릿** 폴더, 다양 한 Azure Resource Manager 템플릿 포함 합니다. 이 문서에서는 템플릿 설명 되어 있습니다.
* A **클라이언트 응용 프로그램** 복사본 인 폴더의 합니다 [ffmpeg를 사용 하 여 처리는 Azure Batch.NET 파일](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) 샘플입니다. 이 문서는 필요 하지 않습니다.
* **hpc 응용 프로그램** 폴더는 Windows 64 비트 버전의 [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)합니다.
* A **파이프라인** 폴더입니다. 이 빌드 프로세스 개요 YAML 파일을 포함 합니다. 이 문서에 설명 되어 있습니다.

이 섹션에서는 버전 제어 및 디자인 Resource Manager 템플릿을 사용 하 여 잘 알고 있다면 가정 합니다. 이러한 개념을 잘 모르는 경우 자세한 내용은 다음 페이지를 참조 하십시오.

* [소스 제어란?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

이 예제 솔루션을 배포 하려면 여러 Resource Manager 템플릿을 활용 합니다. 이 위해 다양 한 기능의 특정 부분을 구현 하는 기능 템플릿 (유사 장치 또는 모듈) 사용 합니다. 또한 해당 하는 기능을 함께 기본 제공 하는 일을 담당 하는 종단 간 솔루션 템플릿을 사용 합니다. 이 방식의 이점은 몇 가지가 있습니다.

* 기본 기능 템플릿에 개별적으로 단위 테스트할 수 있습니다.
* 기본 기능 조직 내에서 표준으로 정의할 수 있습니다 템플릿과 여러 솔루션에서 다시 사용할 수 있습니다.

이 예제에서는 세 가지 템플릿을 배포 하는 종단 간 솔루션 템플릿 (deployment.json)이 있습니다. 기본 템플릿은 솔루션의 특정 측면을 배포 하는 템플릿 기능을 보여 줍니다.

![Azure Resource Manager 템플릿을 사용 하 여 연결 된 템플릿 구조의 예](media/batch-ci-cd/ARMTemplateHierarchy.png)

첫 번째 서식 파일에서 살펴볼 것을 Azure Storage 계정입니다. 솔루션에는 Batch 계정에 응용 프로그램을 배포 하는 저장소 계정이 필요 합니다. 인식 되 고 가치가 합니다 [속하고 Microsoft.Storage 리소스 종류에 대 한 Resource Manager 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 저장소 계정에 대 한 Resource Manager 템플릿을 작성 하는 경우.

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

다음으로 Azure Batch 계정 템플릿을 살펴보겠습니다. Azure Batch 계정 (컴퓨터의 그룹인) 풀에 걸쳐 다양 한 응용 프로그램을 실행 하기 위한 플랫폼으로 작동 합니다. 인식 되 고 가치가 합니다 [Microsoft.Batch 리소스 형식에 대 한 Resource Manager 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) Batch 계정에 대 한 Resource Manager 템플릿을 작성 하는 경우.

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

다음 템플릿 (응용 프로그램을 처리 하는 백 엔드 컴퓨터)는 Azure Batch 풀을 만드는 예를 보여 줍니다. 인식 되 고 가치가 합니다 [Microsoft.Batch 리소스 형식에 대 한 Resource Manager 템플릿 참조 가이드](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) 배치 계정 풀에 대 한 Resource Manager 템플릿을 작성 하는 경우.

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

마지막으로, 오 케 스트레이 터와 비슷하게 작동 하는 템플릿을 했습니다. 이 템플릿에 기능 템플릿을 배포 하는 일을 담당 합니다.

에 대 한 자세한 내용을 확인할 수도 있습니다 [연결 된 Azure Resource Manager 템플릿 생성](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) 별도 문서에서 합니다.

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

인프라 및 소프트웨어를 코드로 정의 하 고 동일한 저장소에 배치 될 수 있습니다.

이 솔루션의 ffmpeg 응용 프로그램 패키지와 사용 됩니다. Ffmpeg 패키지를 다운로드할 수 있습니다 [여기](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)합니다.

![Git 리포지토리 구조의 예](media/batch-ci-cd/git-repository.jpg)

이 리포지토리에 4 개의 주요 섹션이 있습니다.

* 합니다 **arm 템플릿** 코드와 인프라를 저장 하는 폴더
* 합니다 **hpc 응용 프로그램** ffmpeg의 이진 파일이 포함 된 폴더
* 합니다 **파이프라인** 빌드 파이프라인에 대 한 정의가 포함 된 폴더입니다.
* **선택 사항**: 합니다 **클라이언트 응용 프로그램** 는.NET 응용 프로그램에 대 한 코드를 저장 하는 폴더입니다. 이 샘플에서는 사용 하지 마십시오 우리가 있지만 고유한 프로젝트를 실행할 HPC Batch 응용 프로그램의 클라이언트 응용 프로그램을 통해를 만들 수 있습니다.

> [!NOTE]
> 이것이 코드 베이스를 구조체의 한 예입니다. 이 방법은 응용 프로그램, 인프라 및 파이프라인 코드 같은 저장소에 저장 됩니다을 보여 주기 위해 사용 됩니다.

소스 코드를 설정 했으므로 첫 번째 빌드를 시작할 수 있습니다.

## <a name="continuous-integration"></a>연속 통합

[Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), Azure DevOps 서비스 내에서 사용 하면 응용 프로그램에 대 한 빌드, 테스트 및 배포 파이프라인을 구현 합니다.

파이프라인의이 단계에서는 테스트 소프트웨어의 적절 한 부분을 빌드하여 코드 유효성을 검사 하려면 일반적으로 실행 됩니다. 테스트 및 실행 하는 추가 작업의 종류와 수는 더 광범위 한 빌드에 따라 달라 집니다 및 전략을 릴리스 합니다.

## <a name="preparing-the-hpc-application"></a>HPC 응용 프로그램 준비

이 예제에서는 중점적으로 **hpc 응용 프로그램** 폴더입니다. 합니다 **hpc 응용 프로그램** 폴더는 ffmpeg 소프트웨어를 Azure Batch 계정 내에서 실행 됩니다.

1. Azure DevOps 조직에서 Azure 파이프라인 빌드 섹션으로 이동 합니다. 만들기는 **새 파이프라인**합니다.

    ![새 빌드 파이프라인 만들기](media/batch-ci-cd/new-build-pipeline.jpg)

1. 빌드 파이프라인을 만드는 데는 두 가지 옵션이 있습니다.

    a. [비주얼 디자이너를 사용 하 여](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)입니다. 이 사용 하려면 "비주얼 디자이너를 사용"에서 클릭 합니다 **새 파이프라인** 페이지입니다.

    b. [YAML을 사용 하 여 빌드](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)합니다. Azure 리포지토리 또는 GitHub 페이지에서 옵션을 새 파이프라인을 클릭 하 여 새 YAML 파이프라인을 만들 수 있습니다. 또는 아래 예제에서는 원본 제어에 저장 하 고 비주얼 디자이너를 클릭 하 고 YAML 템플릿을 사용 하 여 기존 YAML 파일을 참조할 수 있습니다.

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

1. 필요에 따라 빌드 구성 되 면 선택 **저장 및 큐**합니다. 연속 통합을 사용 하도록 설정 하는 경우 (에 **트리거** 섹션), 리포지토리에 새 커밋이 수행 될 경우에 자동으로 빌드를 트리거할 빌드에 설정 된 조건을 충족 합니다.

    ![기존 빌드 파이프라인의 예](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 로 이동 하 여 Azure DevOps 빌드 진행 상황을 라이브 업데이트를 확인 합니다 **빌드** Azure 파이프라인의 섹션입니다. 빌드 정의에서 적절 한 빌드를 선택 합니다.

    ![빌드에서 라이브 출력 보기](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> HPC Batch 응용 프로그램을 실행 하는 클라이언트 응용 프로그램을 사용 하는 경우 해당 응용 프로그램에 대 한 별도 빌드 정의 만들기 해야 합니다. 다양 한 방법 가이드에서 찾을 수 있습니다 합니다 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) 설명서.

## <a name="continuous-deployment"></a>지속적인 배포

Azure 파이프라인은 또한 응용 프로그램과 기본 인프라를 배포 하는 데 사용 합니다. [릴리스 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) 연속 배포를 사용 하도록 설정 하 고 릴리스 프로세스를 자동화 하는 구성 요소입니다.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>응용 프로그램을 배포 하 고 기본 인프라

인프라를 배포 하는 단계는 여러 가지가 있습니다. 사용한 것 처럼 [연결 된 템플릿](../azure-resource-manager/resource-group-linked-templates.md), 해당 템플릿을 공용 끝점 (HTTP 또는 HTTPS)에서 액세스 가능 해야 합니다. GitHub에서 리포지토리 또는 Azure Blob Storage 계정 또는 다른 저장소 위치 수 있습니다. 템플릿 업로드 된 아티팩트 수 그대로 보안, 개인 모드에서 보유할 수 있지만 특정 형태의 공유 액세스 서명 (SAS) 토큰을 사용 하 여 액세스할. 다음 예제에서는 Azure Storage blob에서 템플릿 사용 하 여 인프라를 배포 하는 방법에 설명 합니다.

1. 만들기는 **새 릴리스 정의**, 빈 정의 선택 합니다. 그런 다음 파이프라인에 대 한 관련 값으로 새로 만든된 환경 이름을 바꾸는 해야 합니다.

    ![초기 릴리스 파이프라인](media/batch-ci-cd/Release-0.jpg)

1. HPC 응용 프로그램에 대 한 출력을 가져오려면 빌드 파이프라인에 대 한 종속성을 만듭니다.

    > [!NOTE]
    > 다시 한 번 확인 합니다 **소스 별칭**처럼 릴리스 정의 내에서 작업을 만들 때 필요 합니다.

    ![적절 한 빌드 파이프라인에서는 HPCApplicationPackage에 대 한 아티팩트 링크를 만들려면](media/batch-ci-cd/Release-1.jpg)

1. 이 이번에는 Azure 리포지토리 아티팩트가 다른 아티팩트에 대 한 링크를 만듭니다. 이 리포지토리에 저장 된 Resource Manager 템플릿의 액세스 해야 합니다. Resource Manager 템플릿을 컴파일이 필요 하지 않습니다, 대로 빌드 파이프라인을 통해 적용할 필요가 없습니다.

    > [!NOTE]
    > 다시 한 번 확인 합니다 **소스 별칭**처럼 릴리스 정의 내에서 작업을 만들 때 필요 합니다.

    ![Azure 리포지토리를 아티팩트 링크를 만듭니다](media/batch-ci-cd/Release-2.jpg)

1. 로 이동 합니다 **변수** 섹션입니다. 이 여러 태스크로 동일한 정보를 입력 하지 파이프라인에 여러 가지 변수를 만드는 것이 좋습니다. 이들은 어떻게 영향을 주는지 배포 하 고이 예제에 사용 된 변수입니다.

    * **applicationStorageAccountName**: HPC 응용 프로그램 이진 파일을 저장할 저장소 계정의 이름
    * **batchAccountApplicationName**: Azure Batch 계정에서 응용 프로그램의 이름
    * **batchAccountName**: Azure Batch 계정의 이름
    * **batchAccountPoolName**: 처리를 수행 하는 Vm의 풀의 이름
    * **batchApplicationId**: Azure Batch 응용 프로그램에 대 한 고유 ID
    * **batchApplicationVersion**: Batch 응용 프로그램 (즉, ffmpeg 이진)의 의미 체계 버전
    * **location**: 배포할 Azure 리소스의 위치
    * **resourceGroupName**: 만들려는 리소스 그룹의 이름 및 리소스에 배포 될 위치
    * **storageAccountName**: 연결 된 Resource Manager 템플릿을 저장할 저장소 계정의 이름

    ![변수 Azure 파이프라인 릴리스에 대 한 설정의 예](media/batch-ci-cd/Release-4.jpg)

1. 개발 환경에 대 한 작업으로 이동 합니다. 에 스냅숏, 아래 6 가지 작업을 볼 수 있습니다. 이러한 작업은: ffmpeg zip 형식의 파일을 다운로드, 중첩 된 Resource Manager 템플릿을 호스트, Resource Manager 템플릿을 해당 저장소 계정에 복사, batch 계정 및 필요한 종속성을 배포, 응용 프로그램을 만들 저장소 계정을 배포 합니다. Azure Batch 계정 및 Azure Batch 계정에 응용 프로그램 패키지를 업로드 합니다.

    ![Azure Batch는 HPC 응용 프로그램을 릴리스 하는 데 사용 하는 작업의 예](media/batch-ci-cd/Release-3.jpg)

1. 추가 된 **파이프라인 아티팩트 다운로드 (미리 보기)** 작업 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** ApplicationPackage 에이전트 다운로드
    * **다운로드할 아티팩트의 이름입니다.** hpc 응용 프로그램
    * **다운로드 경로**: $ (system.defaultworkingdirectory)

1. 아티팩트를 저장할 저장소 계정을 만듭니다. 솔루션에서 기존 저장소 계정을 사용할 수 있지만 자체 포함 된 샘플 및 콘텐츠의 격리의 경우 (특히: Resource Manager 템플릿)이 아티팩트에 대 한 전용된 저장소 계정을 진행 중인 합니다.

    추가 된 **Azure 리소스 그룹 배포** 작업 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Resource Manager 템플릿에 대 한 저장소 계정 배포
    * **Azure 구독:** 적절 한 Azure 구독 선택
    * **작업**: 리소스 그룹을 만들기 또는 업데이트
    * **리소스 그룹**: $(resourceGroupName)
    * **위치**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **템플릿 매개 변수 재정의**:-accountName $(storageAccountName)

1. 저장소 계정에 원본 제어에서 아티팩트를 업로드 합니다. 이 작업을 수행 하는 Azure 파이프라인 작업은입니다. 이 작업의 일환으로, 저장소 계정 컨테이너 URL 및 SAS 토큰을 Azure 파이프라인의 변수에 출력 수 수 있습니다. 즉,이 에이전트 단계 전체에 걸쳐 다시 사용할 수 있습니다.

    추가 된 **Azure 파일 복사** 작업 하 고 다음 속성을 설정 합니다.
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Azure 연결 형식**: Azure 리소스 관리자
    * **Azure 구독:** 적절 한 Azure 구독 선택
    * **대상 유형**: Azure Blob
    * **RM 저장소 계정**: $(storageAccountName)
    * **컨테이너 이름**: 템플릿
    * **저장소 컨테이너 URI**: templateContainerUri
    * **저장소 컨테이너 SAS 토큰**: templateContainerSasToken

1. 오 케 스트레이 터 템플릿을 배포 합니다. 앞에서 오 케 스트레이 터 템플릿을 회수, SAS 토큰을 외에도 저장소 계정 컨테이너 URL에 대 한 매개 변수 되었는지 확인할 수 있습니다. Resource Manager 템플릿에서 필요한 변수를 릴리스 정의의 변수 섹션에 저장 하거나 또는 다른 Azure 파이프라인 작업 (예를 들어 Azure Blob 복사 작업의 일부)에서 설정한는 유의 해야 합니다.

    추가 된 **Azure 리소스 그룹 배포** 작업 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch를 배포 합니다.
    * **Azure 구독:** 적절 한 Azure 구독 선택
    * **작업**: 리소스 그룹을 만들기 또는 업데이트
    * **리소스 그룹**: $(resourceGroupName)
    * **위치**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **템플릿 매개 변수 재정의**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

일반적인 방법은 Azure Key Vault 작업을 사용 하는 것입니다. 서비스 주체 (Azure 구독에 연결)에 적절 한 액세스 정책을 설정 하는 경우 Azure Key Vault에서 비밀을 다운로드할 수 있습니다 및 파이프라인에서 변수로 사용할 수 있습니다. 암호의 이름은 연결된 된 값을 사용 하 여 설정 됩니다. 예를 들어, sshPassword의 비밀 $(sshPassword) 릴리스 정의에서 사용 하 여 참조할 수 없습니다.

1. 다음 단계에서는 Azure CLI를 호출합니다. 첫 번째 Azure Batch에서 응용 프로그램을 만드는 데 사용 됩니다. 와 연결 된 패키지를 업로드 합니다.

    추가 된 **Azure CLI** 작업 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch 계정에서 응용 프로그램 만들기
    * **Azure 구독:** 적절 한 Azure 구독 선택
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 두 번째 단계는 응용 프로그램에 연결 된 패키지를 업로드 하는 데 사용 됩니다. 경우에 ffmpeg 파일입니다.

    추가 된 **Azure CLI** 작업 하 고 다음 속성을 설정 합니다.
    * **표시 이름:** Azure Batch 계정에 패키지 업로드
    * **Azure 구독:** 적절 한 Azure 구독 선택
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 응용 프로그램 패키지의 버전 번호를 변수에 설정 됩니다. 이전 버전의 패키지를 덮어씁니다를 작동 하는 경우 및 Azure Batch에 푸시된 패키지의 버전 번호를 수동으로 제어 하려는 경우에 유용 합니다.

1. 선택 하 여 새 릴리스를 만듭니다 **릴리스 > 새 릴리스가 생성**합니다. 트리거된 상태를 보려면 새 릴리스에 대 한 링크를 선택 합니다.

1. 선택 하 여 에이전트에서 라이브 출력을 볼 수는 **로그** 환경 아래에 있는 단추입니다.

    ![릴리스 상태 보기](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>테스트 환경

환경 설정 되 면 다음 테스트를 성공적으로 완료할 수를 확인 합니다.

PowerShell 명령 프롬프트에서 Azure CLI를 사용 하 여 새 Azure Batch 계정에 연결 합니다.

* 사용 하 여 Azure 계정에 로그인 `az login` 인증 지침을 따릅니다.
* Batch 계정을 인증 합니다. `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>사용 가능한 응용 프로그램 목록

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>풀 올바른지 확인

```azurecli
az batch pool list
```

값을 확인 `currentDedicatedNodes` 이 명령의 출력에서 합니다. 이 값은 다음 테스트에 조정 됩니다.

#### <a name="resize-the-pool"></a>풀 크기 조정

계산 노드에서 작업 및 태스크 테스트에 사용할 수 있는, 사용 가능한 노드에서보다 및 크기 조정 완료 될 때까지 현재 상태를 확인 하려면 풀 목록 명령을 사용 하 여 확인 되므로 풀 크기 조정

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 외에.NET 및 Python을 사용 하 여 ffmpeg를 활용 하는 두 개의 자습서입니다. 간단한 응용 프로그램을 통해 Batch 계정과 상호 작용 하는 방법에 대 한 자세한 내용은 다음이 자습서를 참조 하세요.

* [Python API를 사용 하 여 Azure Batch를 사용 하 여 병렬 워크 로드 실행](tutorial-parallel-python.md)
* [.NET API를 사용 하 여 Azure Batch를 사용 하 여 병렬 워크 로드 실행](tutorial-parallel-dotnet.md)
