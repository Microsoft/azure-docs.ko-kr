---
title: Azure 대시보드를 프로그래밍 방식으로 만들기
description: Azure Portal 대시보드를 템플릿으로 사용 하 여 프로그래밍 방식으로 Azure 대시보드를 만듭니다. JSON 참조를 포함 합니다.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: 414427c722b3531c994bb99dbd5d1332c5253dfd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900987"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure 대시보드를 프로그래밍 방식으로 만들기

이 문서에서는 Azure 대시보드를 프로그래밍 방식으로 만들고 게시하는 과정을 안내합니다. 아래에 표시된 대시보드는 문서 전체에서 참조됩니다.

![샘플 대시보드](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>개요

[Azure Portal](https://portal.azure.com) 공유 대시보드는 가상 머신 및 저장소 계정과 같은 [리소스](../azure-resource-manager/management/overview.md) 입니다. [AZURE RESOURCE MANAGER REST api](/rest/api/), [Azure CLI](/cli/azure)및 [Azure PowerShell 명령을](/powershell/azure/get-started-azureps)사용 하 여 프로그래밍 방식으로 리소스를 관리할 수 있습니다.  

이러한 Api를 사용 하 여 리소스를 보다 쉽게 관리할 수 있습니다. 이러한 각 API 및 도구는 리소스를 만들고 나열하며 검색, 수정 및 삭제하는 방법을 제공합니다. 대시보드는 리소스 이므로 사용할 선호 하는 API 또는 도구를 선택할 수 있습니다.

어떤 도구를 사용 하 든 대시보드 개체의 JSON 표현을 생성 해야 합니다. 이 개체는 대시보드의 타일에 대한 정보를 포함 합니다. 여기에는 크기, 위치, 바인딩된 리소스 및 사용자 지정이 포함 됩니다.

이 JSON 문서를 작성 하는 가장 실용적인 방법은 Azure Portal를 사용 하는 것입니다. 타일을 대화형으로 추가 하 고 배치할 수 있습니다. 그런 다음 JSON을 내보내고 나중에 스크립트, 프로그램 및 배포 도구에서 사용 하기 위해 결과에서 템플릿을 만듭니다.

## <a name="create-a-dashboard"></a>대시보드 만들기

대시보드를 만들려면 [Azure Portal](https://portal.azure.com) 메뉴에서 **대시보드** 를 선택 하 고 **새 대시보드**를 선택 합니다.

![새 대시보드 명령](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

타일 갤러리를 사용 하 여 타일을 찾아서 추가 합니다. 타일을 끌어서 놓아 추가합니다. 일부 타일은 끌기 핸들을 사용 하 여 크기 조정을 지원 합니다.

![핸들을 끌어 크기 변경](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

다른 항목의 상황에 맞는 메뉴에서 선택할 수 있는 크기는 고정 되어 있습니다.

![크기를 변경 하는 상황에 맞는 메뉴 크기](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>대시보드 공유

대시보드를 구성한 후 다음 단계는 **공유** 명령을 사용 하 여 대시보드를 게시 한 다음 리소스 탐색기를 사용 하 여 JSON을 가져오는 것입니다.

![대시보드 공유](./media/azure-portal-dashboards-create-programmatically/share-command.png)

**공유** 를 선택 하면 게시할 구독 및 리소스 그룹을 선택 하 라는 메시지가 표시 됩니다. 선택한 구독 및 리소스 그룹에 대한 쓰기 권한이 있어야 합니다. 자세한 내용은 [AZURE RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)를 참조 하세요.

![공유 및 액세스 변경](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>대시보드의 JSON 표현 가져오기

게시에는 불과 몇 초만 소요됩니다.  완료 되 면 다음 단계는 [리소스 탐색기](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) 로 이동 하 여 JSON을 인출 합니다.

![찾아보기 리소스 탐색기](./media/azure-portal-dashboards-create-programmatically/search-resource-explorer.png)

리소스 탐색기에서 선택한 구독 및 리소스 그룹으로 이동 합니다. 그런 다음, 새로 게시 된 대시보드 리소스를 선택 하 여 JSON을 표시 합니다.

![리소스 탐색기에서 JSON 보기](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json-detail.png)

## <a name="create-a-template-from-the-json"></a>JSON에서 템플릿 만들기

다음 단계는이 JSON에서 템플릿을 만드는 것입니다. 적절 한 리소스 관리 Api, 명령줄 도구 또는 포털 내에서 프로그래밍 방식으로 해당 템플릿을 사용 합니다.

대시보드 JSON 구조를 완전 하 게 이해 하지 않아도 템플릿을 만들 수 있습니다. 대부분의 경우 각 타일의 구조 및 구성을 유지 하려고 합니다. 그런 다음 타일이 가리키는 Azure 리소스 집합을 매개 변수화 합니다. 내보낸 JSON 대시보드를 확인 하 고 Azure 리소스 Id의 모든 항목을 찾습니다. 예제 대시보드는 모두 단일 Azure 가상 컴퓨터를 가리키는 여러 개의 타일을 포함하고 있습니다. 대시보드는이 단일 리소스를 보기만 하기 때문입니다. 문서 끝에 포함 된 샘플 JSON을 검색 하는 경우 "/구독"에 대해이 ID의 여러 항목을 찾을 수 있습니다.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

나중에 가상 머신에 대해이 대시보드를 게시 하려면 JSON 내에서이 문자열의 모든 항목을 매개 변수화 합니다.

Azure에서 리소스를 만드는 Api에는 두 가지 방법이 있습니다.

* 명령적 Api는 한 번에 하나의 리소스를 만듭니다. 자세한 내용은 [리소스](/rest/api/resources/resources)를 참조하세요.
* 단일 API 호출을 사용 하 여 여러 종속 리소스를 만드는 템플릿 기반 배포 시스템입니다. 자세한 내용은 [리소스 관리자 템플릿 및 Azure PowerShell를 사용 하 여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 참조 하세요.

템플릿 기반 배포는 매개 변수화 및 템플릿을 지원 합니다. 이 문서에서는이 방법을 사용 합니다.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>템플릿 배포를 사용하여 템플릿에서 대시보드를 프로그래밍 방식으로 만듭니다.

Azure는 여러 리소스의 배포를 오케스트레이션하는 기능을 제공합니다. 배포할 리소스 집합과 둘 간의 관계를 표현 하는 배포 템플릿을 만듭니다.  각 리소스의 JSON 형식은 사용자가 하나씩 작성하는 경우와 같습니다. 차이점은 템플릿 언어가 변수, 매개 변수, 기본 함수 등과 같은 몇 가지 개념을 추가 한다는 것입니다. 이 확장 구문은 템플릿 배포 컨텍스트에서만 지원 됩니다. 앞에서 설명한 명령적 Api와 함께 사용 하는 경우에는 작동 하지 않습니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md)를 참조 하세요.

매개 변수화는 템플릿의 매개 변수 구문을 사용 하 여 수행 해야 합니다.  여기에 표시 된 것 처럼 이전에 찾은 리소스 ID의 모든 인스턴스를 바꿉니다.

하드 코드 된 리소스 ID를 사용 하는 예제 JSON 속성:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

템플릿 매개 변수를 기반으로 매개 변수화된 버전으로 변환된 JSON 속성 예제

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

다음과 같이 JSON 템플릿 맨 위에 필요한 템플릿 메타 데이터와 매개 변수를 선언 합니다.

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

이 문서의 끝에 있는 전체 작업 템플릿을 볼 수 있습니다.

템플릿을 구성한 후에는 다음 방법 중 하나를 사용 하 여 템플릿을 배포 합니다.

* [REST API](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Azure Portal 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)

다음은 두 가지 버전의 대시보드 JSON 예제입니다. 첫 번째는 리소스에 이미 바인딩된 포털에서 내보낸 버전입니다. 두 번째는 프로그래밍 방식으로 모든 가상 머신에 연결 하 고 Azure Resource Manager를 사용 하 여 배포할 수 있는 템플릿 버전입니다.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>템플릿 이전 예제 대시보드의 JSON 표현

이 예제에서는이 문서와 함께 수행 되는 경우에 표시 되는 것을 보여 줍니다. 지침이 이미 배포 된 대시보드의 JSON 표현을 내보냈습니다. 이 대시보드가 특정 Azure virtual machine을 가리키고 있음을 보여 주는 하드 코드 된 리소스 식별자입니다.

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

대시보드의 템플릿 버전은 `virtualMachineName`, `virtualMachineResourceGroup`및 `dashboardName`라는 세 개의 매개 변수를 정의 했습니다.  매개 변수를 사용하면 배포할 때마다 다른 Azure 가상 컴퓨터에서 이 대시보드를 가리키도록 할 수 있습니다. Azure virtual machines를 가리키도록이 대시보드를 프로그래밍 방식으로 구성 하 고 배포할 수 있습니다. 이 기능을 테스트 하려면 다음 템플릿을 복사 하 여 [Azure Portal 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)에 붙여 넣습니다.

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
