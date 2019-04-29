---
title: Azure 대시보드를 프로그래밍 방식으로 만들기 | Microsoft Docs
description: 이 문서에서는 Azure 대시보드를 프로그래밍 방식으로 만다는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: b24a0397a1365479907fedc6348caa54508dbbb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552201"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure 대시보드를 프로그래밍 방식으로 만들기

이 문서에서는 Azure 대시보드를 프로그래밍 방식으로 만들고 게시하는 과정을 안내합니다. 아래에 표시된 대시보드는 문서 전체에서 참조됩니다.

![샘플 대시보드](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>개요

Azure에서 공유 대시보드는 가상 머신 및 저장소 계정과 같은 [리소스](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)입니다.  따라서 [Azure Resource Manager REST API](/rest/api/), [Azure CLI](https://docs.microsoft.com/cli/azure), [Azure PowerShell 명령](https://docs.microsoft.com/powershell/azure/get-started-azureps) 및 이러한 API를 기반으로 하는 수많은 [Azure Portal](https://portal.azure.com) 기능을 통해 프로그래밍 방식으로 관리하고 리소스를 보다 쉽게 관리할 수 있습니다.  

이러한 각 API 및 도구는 리소스를 만들고 나열하며 검색, 수정 및 삭제하는 방법을 제공합니다.  대시보드는 리소스이므로 사용할 선호하는 API / 도구를 선택할 수 있습니다.

어떤 도구를 사용하든 관계없이 모든 리소스 생성 API를 호출하기 전에 대시보드 개체의 JSON 표현을 생성해야 합니다. 이 개체에는 대시보드에서 부분(즉, 개체를 포함합니다. 크기, 위치, 바인딩되어 있는 리소스 및 모든 사용자 지정이 포함됩니다.

이 JSON 문서를 작성하는 가장 유용한 방법은 [포털](https://portal.azure.com/)을 사용하여 타일을 대화형으로 추가하고 위치를 지정하는 것입니다. 그런 다음 JSON을 내보냅니다. 마지막으로, 스크립트, 프로그램 및 배포 도구에서 나중에 사용하기 위해 결과에서 템플릿을 만듭니다.

## <a name="create-a-dashboard"></a>대시보드 만들기

새 대시보드를 만들려면 포털의 주 화면에서 새 대시보드 명령을 사용합니다.

![새 대시보드 명령](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

그런 다음 타일 갤러리를 사용하여 타일을 찾아 추가할 수 있습니다. 타일을 끌어서 놓아 추가합니다. 일부 타일은 끌기 핸들을 통한 크기 조정을 지원하지만, 일부는 바로 가기 메뉴에서 볼 수 있는 고정 크기를 지원합니다.

### <a name="drag-handle"></a>끌기 핸들
![끌기 핸들](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>바로 가기 메뉴를 통한 고정 크기
![바로 가기 메뉴 크기 조정](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>대시보드 공유

대시보드를 원하는 대로 구성한 후 다음 단계는 대시보드를 게시하고(공유 명령 사용) 리소스 탐색기를 사용하여 JSON을 가져오는 것입니다.

![공유 명령](./media/azure-portal-dashboards-create-programmatically/share-command.png)

[공유] 명령을 클릭하면 게시할 구독 및 리소스 그룹을 선택하라는 대화 상자가 표시됩니다. 선택한 구독 및 리소스 그룹에 대해 [쓰기 액세스 권한이 있어야](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 합니다.

![공유 및 액세스](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>대시보드의 JSON 표현 가져오기

게시에는 불과 몇 초만 소요됩니다.  완료되었으면 다음 단계는 [리소스 탐색기](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade)로 이동하여 JSON을 가져오는 것입니다.

![리소스 탐색기 찾아보기](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

리소스 탐색기에서 선택한 구독 및 리소스 그룹으로 이동합니다. 다음으로 JSON을 표시하기 위해 새로 게시된 대시보드 리소스를 클릭합니다.

![리소스 탐색기 json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>JSON에서 템플릿 만들기

다음 단계는 적절한 리소스 관리 API, 명령줄 도구를 사용하거나 포털 내에서 프로그래밍 방식으로 다시 사용할 수 있도록 이 JSON에서 템플릿을 만드는 것입니다.

템플릿을 만들기 위해 대시보드 JSON 구조를 완전히 이해할 필요는 없습니다. 대부분의 경우 각 타일의 구조 및 구성을 유지한 후 이들이 가리키는 Azure 리소스 집합을 매개 변수화하려고 합니다. 내보낸 JSON 대시보드를 살펴보고 Azure 리소스 ID의 모든 항목을 찾습니다. 예제 대시보드는 모두 단일 Azure 가상 컴퓨터를 가리키는 여러 개의 타일을 포함하고 있습니다. 이는 대시보드가 이 단일 리소스만 살펴보기 때문입니다. 샘플 json(문서 끝에 포함됨)에서 “/subscriptions”를 검색하는 경우 이 ID 항목이 여러 개 나옵니다.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

향후 가상 머신에 대해 이 대시보드를 게시하려면 이 문자열의 모든 항목을 JSON 내에서 매개 변수화해야 합니다. 

Azure에서 리소스를 만드는 API에는 두 종류가 있습니다. 한 번에 하나의 리소스를 만드는 [명령적 API](https://docs.microsoft.com/rest/api/resources/resources), 한 번의 API 호출로 여러 개의 종속 리소스 생성을 오케스트레이션할 수 있는 [템플릿 기반 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) 시스템입니다. 후자는 기본적으로 매개 변수화 및 템플릿을 지원하므로 예제로 사용합니다.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>템플릿 배포를 사용하여 템플릿에서 대시보드를 프로그래밍 방식으로 만듭니다.

Azure는 여러 리소스의 배포를 오케스트레이션하는 기능을 제공합니다. 이들 간의 관계는 물론 배포할 리소스 집합을 표현하는 배포 템플릿을 만듭니다.  각 리소스의 JSON 형식은 사용자가 하나씩 작성하는 경우와 같습니다. 차이점은 [템플릿 언어](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)는 변수, 매개 변수, 기본 기능 등과 같은 몇 가지 개념을 추가한다는 것입니다. 이 확장된 구문은 템플릿 배포 컨텍스트에서만 지원되며 앞에서 설명한 명령적 API와 함께 사용한 경우에는 작동하지 않습니다.

이 경우 템플릿의 매개 변수 구문을 사용하여 매개 변수화를 수행해야 합니다.  아래 표시된 것처럼 이전에 찾은 모든 리소스 ID 항목을 바꿉니다.

### <a name="example-json-property-with-hard-coded-resource-id"></a>하드 코드된 리소스 ID가 있는 JSON 속성 예제
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>템플릿 매개 변수를 기반으로 매개 변수화된 버전으로 변환된 JSON 속성 예제

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

또한 다음과 같이 json 템플릿 맨 위에 일부 필요한 템플릿 메타데이터와 매개 변수를 선언해야 합니다.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__이 문서의 끝에서 작동 중인 전체 템플릿을 볼 수 있습니다.__

템플릿을 작성한 후에는 [REST API](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create) 또는 [포털의 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)를 사용하여 배포할 수 있습니다.

다음은 두 가지 버전의 대시보드 JSON 예제입니다. 첫 번째는 리소스에 이미 바인딩된 포털에서 내보낸 버전입니다. 두 번째는 모든 VM에 프로그래밍 방식으로 바인딩하고 Azure Resource Manager를 사용하여 배포할 수 있는 템플릿 버전입니다.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>예제 대시보드의 JSON 표현(템플릿 이전)

이미 배포된 대시보드의 JSON 표현을 가져오기 위해 이전 지침을 따르는 경우 표시될 수 있습니다. 하드 코드된 리소스 ID는 이 대시보드가 특정 Azure 가상 머신을 가리키고 있음을 나타냅니다.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>예제 대시보드의 템플릿 표현

대시보드의 템플릿 버전은 __virtualMachineName__, __virtualMachineResourceGroup__ 및 __dashboardName__이라는 세 개의 매개 변수를 정의합니다.  매개 변수를 사용하면 배포할 때마다 다른 Azure 가상 컴퓨터에서 이 대시보드를 가리키도록 할 수 있습니다. 매개 변수화된 ID는 이 대시보드가 프로그래밍 방식으로 구성 및 배포되어 Azure 가상 머신을 가리킬 수 있음을 나타내기 위해 강조 표시됩니다. 이 기능을 테스트하는 가장 쉬운 방법은 다음 템플릿을 복사하여 [Azure Portal의 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)에 붙여넣는 것입니다. 

이 예에서는 자체적으로 대시보드를 배포하지만 템플릿 언어를 사용하면 여러 리소스를 배포하고 하나 이상의 대시보드를 함께 번들링할 수 있습니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
