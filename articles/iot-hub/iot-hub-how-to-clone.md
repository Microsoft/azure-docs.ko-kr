---
title: Azure IoT hub를 복제 하는 방법
description: Azure IoT hub를 복제 하는 방법
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429145"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT hub를 다른 지역에 복제 하는 방법

이 문서에서는 IoT Hub를 복제 하는 방법을 설명 하 고 시작 하기 전에 대답 해야 하는 몇 가지 질문을 제공 합니다. IoT hub를 복제할 수 있는 몇 가지 이유는 다음과 같습니다.
 
* 회사를 한 지역에서 다른 지역으로 이동 하는 경우 (예: 유럽에서 북아메리카으로 또는 그 반대로) 리소스와 데이터를 새 위치에 지리적으로 가깝게 이동 하 여 허브를 이동 해야 합니다.

* 개발 및 프로덕션 환경에 대 한 허브를 설정 합니다.

* 다중 허브 고가용성의 사용자 지정 구현을 수행 하려고 합니다. 자세한 내용은 [고가용성 및 재해 복구 IoT Hub의 지역 간 HA를 수행 하는 방법 섹션](iot-hub-ha-dr.md#achieve-cross-region-ha)을 참조 하세요.

* 허브에 대해 구성 된 [파티션](iot-hub-scaling.md#partitions) 수를 늘려야 합니다. 허브를 처음 만들 때 설정 되며 변경할 수 없습니다. 이 문서의 정보를 사용 하 여 허브를 복제 하 고 클론을 만들 때 파티션 수를 늘릴 수 있습니다.

허브를 복제 하려면 원본 허브에 대 한 관리자 액세스 권한이 있는 구독이 필요 합니다. 새 허브는 새 리소스 그룹 및 지역, 원본 허브와 동일한 구독 또는 새 구독에도 배치할 수 있습니다. 허브 이름은 전역적으로 고유 해야 하기 때문에 동일한 이름을 사용할 수 없습니다.

> [!NOTE]
> 지금은 IoT hub를 자동으로 복제 하는 데 사용할 수 있는 기능이 없습니다. 주로 수동 프로세스 이므로 오류가 발생 하기 쉽습니다. 허브 복제의 복잡성은 허브의 복잡성에 비례 합니다. 예를 들어 메시지 라우팅을 사용 하지 않고 IoT hub를 복제 하는 것은 매우 간단 합니다. 메시지 라우팅을 하나의 복잡도로 추가 하는 경우 허브를 복제 하는 것이 보다 덜 복잡 합니다. 또한 라우팅 엔드포인트에 사용 되는 리소스를 이동 하는 경우에는 magniture의 또 다른 순서가 더 복잡해 집니다. 

## <a name="things-to-consider"></a>고려할 사항

IoT hub를 복제 하기 전에 고려해 야 할 몇 가지 사항이 있습니다.

* 원본 위치에서 사용할 수 있는 모든 기능을 새 위치 에서도 사용할 수 있는지 확인 합니다. 일부 서비스는 미리 보기 상태 이며 모든 기능을 어디서 나 사용할 수 있는 것은 아닙니다.

* 복제 된 버전을 만들고 확인 하기 전에 원래 리소스를 제거 하지 마십시오. 허브를 제거 하면 영원히 사라지고 설정이 나 데이터를 검사 하 여 허브가 올바르게 복제 되었는지 확인 하는 방법이 없습니다.

* 많은 리소스에는 전역적으로 고유한 이름이 필요 하므로 복제 된 버전에 다른 이름을 사용 해야 합니다. 또한 복제 된 허브가 속한 리소스 그룹에 다른 이름을 사용 해야 합니다. 

* 원본 IoT hub에 대 한 데이터는 마이그레이션되지 않습니다. 여기에는 원격 분석 메시지, C2D (클라우드-장치) 명령 및 일정 및 기록과 같은 작업 관련 정보가 포함 됩니다. 메트릭 및 로깅 결과도 마이그레이션되지 않습니다. 

* Azure Storage로 라우팅되는 데이터 또는 메시지의 경우 원본 저장소 계정에 데이터를 그대로 두거나, 새 지역의 새 저장소 계정으로 데이터를 전송 하거나, 이전 데이터를 그대로 두고 새 데이터의 새 위치에 새 저장소 계정을 만들 수 있습니다. Blob 저장소에서 데이터를 이동 하는 방법에 대 한 자세한 내용은 [AzCopy 시작](../storage/common/storage-use-azcopy-v10.md)을 참조 하세요.

* Event Hubs 및 Service Bus 토픽 및 큐에 대 한 데이터를 마이그레이션할 수 없습니다. 이는 지정 시간 데이터 이며 메시지가 처리 된 후 저장 되지 않습니다.

* 마이그레이션의 가동 중지 시간을 예약 해야 합니다. 새 허브에 장치를 복제 하는 데 시간이 걸립니다. Import/Export 방법을 사용 하는 경우 벤치 마크 테스트는 50만 장치를 이동 하는 데 2 시간 정도 소요 될 수 있으며, 백만 대의 장치를 이동 하는 데 4 시간 정도 소요 될 수 있습니다. 

* 장치를 종료 하거나 변경 하지 않고도 새 허브에 장치를 복사할 수 있습니다. 

    * 장치를 원래 DPS를 사용 하 여 프로 비전 한 경우 다시 프로 비전 하면 각 장치에 저장 된 연결 정보를 업데이트 합니다. 
    
    * 그렇지 않으면 Import/Export 메서드를 사용 하 여 장치를 이동한 다음 새 허브를 사용 하도록 장치를 수정 해야 합니다. 예를 들어 쌍 desired 속성에서 IoT Hub 호스트 이름을 사용 하도록 장치를 설정할 수 있습니다. 장치는 해당 IoT Hub 호스트 이름으로 이동 하 고, 이전 허브에서 장치의 연결을 끊은 후 새 허브에 다시 연결 합니다.
    
* 새 리소스와 함께 사용할 수 있도록 사용 중인 인증서를 업데이트 해야 합니다. 또한 다른 곳에서 DNS 테이블에 정의 된 허브가 있을 수 있습니다 .이 경우 해당 DNS 정보를 업데이트 해야 합니다.

## <a name="methodology"></a>방법

이 방법은 IoT hub를 한 지역에서 다른 지역으로 이동 하는 데 권장 되는 일반적인 방법입니다. 메시지 라우팅의 경우 리소스가 새 지역으로 이동 되지 않는다고 가정 합니다. 자세한 내용은 [메시지 라우팅에 대 한 섹션](#how-to-handle-message-routing)을 참조 하세요.

   1. 허브 및 해당 설정을 리소스 관리자 템플릿으로 내보냅니다. 
   
   1. 모든 이름 및 복제 된 허브의 위치를 업데이트 하는 것과 같이 템플릿에 필요한 변경 작업을 수행 합니다. 메시지 라우팅 엔드포인트에 사용 되는 템플릿의 모든 리소스에 대해 해당 리소스에 대 한 템플릿의 키를 업데이트 합니다.
   
   1. 새 위치에서 새 리소스 그룹으로 템플릿을 가져옵니다. 그러면 복제본이 생성 됩니다.

   1. 필요에 따라 디버깅 합니다. 
   
   1. 템플릿으로 내보내지 않은 항목을 추가 합니다. 
   
       예를 들어 소비자 그룹을 템플릿으로 내보내지 않습니다. 소비자 그룹을 템플릿에 수동으로 추가 하거나 허브를 만든 후에 [Azure Portal](https://portal.azure.com) 을 사용 해야 합니다. [Azure Resource Manager 템플릿을 사용 하 여 IoT Hub 메시지 라우팅을 구성 하](tutorial-routing-config-message-routing-rm-template.md)는 문서의 템플릿에 소비자 그룹을 한 개 추가 하는 예제가 있습니다.
       
   1. 원본 허브에서 클론으로 장치를 복사 합니다. [IoT hub에 등록 된 장치 관리](#managing-the-devices-registered-to-the-iot-hub)섹션에서 설명 합니다.

## <a name="how-to-handle-message-routing"></a>메시지 라우팅을 처리 하는 방법

허브에서 [사용자 지정 라우팅을](iot-hub-devguide-messages-read-custom.md)사용 하는 경우 허브에 대 한 템플릿을 내보내는 경우에는 라우팅 구성이 포함 되지만 리소스 자체는 포함 되지 않습니다. 라우팅 리소스를 새 위치로 이동할지 아니면 새 위치로 유지할지를 선택 하 고 "있는 그대로" 계속 사용 해야 합니다. 

예를 들어 미국 서 부에서 저장소 계정으로 메시지를 라우팅하는 허브 (미국 서 부)가 있고 허브를 미국 동부로 이동 하려는 경우를 가정해 보겠습니다. 허브를 이동 하 고 여전히 메시지를 미국 서 부의 저장소 계정으로 라우팅하도록 하거나 허브를 이동 하 고 저장소 계정을 이동할 수도 있습니다. 다른 지역의 엔드포인트 리소스에 대 한 라우팅 메시지에서 약간의 성능 저하가 발생할 수 있습니다.

라우팅 엔드포인트에 사용 되는 리소스도 이동 하지 않는 경우 메시지 라우팅을 사용 하는 허브를 매우 쉽게 이동할 수 있습니다. 

허브에서 메시지 라우팅을 사용 하는 경우 두 가지 옵션을 선택할 수 있습니다. 

1. 라우팅 엔드포인트에 사용 되는 리소스를 새 위치로 이동 합니다.

    * [Azure Portal](https://portal.azure.com) 에서 수동으로 또는 리소스 관리자 템플릿을 사용 하 여 새 리소스를 직접 만들어야 합니다. 

    * 전역적으로 고유한 이름이 있으므로 새 위치에서 리소스를 만들 때 모든 리소스의 이름을 바꾸어야 합니다. 
     
    * 새 허브를 만들기 전에 새 허브의 템플릿에서 리소스 이름과 리소스 키를 업데이트 해야 합니다. 새 허브가 만들어질 때 리소스가 있어야 합니다.

1. 라우팅 엔드포인트에 사용 되는 리소스를 이동 하지 않습니다. "내부"를 사용 합니다.

   * 템플릿을 편집 하는 단계에서 각 라우팅 리소스의 키를 검색 하 고 새 허브를 만들기 전에 템플릿에 배치 해야 합니다. 

   * 허브는 여전히 원래 라우팅 리소스를 참조 하 고 구성 된 대로 메시지를 라우팅합니다.

   * 허브와 라우팅 엔드포인트 리소스가 동일한 위치에 있지 않기 때문에 성능이 저하 됩니다.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>허브를 다른 지역으로 마이그레이션 준비

이 섹션에서는 허브 마이그레이션에 대 한 구체적인 지침을 제공 합니다.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>원본 허브를 찾아 리소스 템플릿으로 내보냅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **리소스 그룹** 으로 이동 하 여 이동 하려는 허브가 포함 된 리소스 그룹을 선택 합니다. **리소스** 로 이동 하 여 해당 방식으로 허브를 찾을 수도 있습니다. 허브를 선택 합니다.

1. 허브에 대 한 속성 및 설정 목록에서 **템플릿 내보내기** 를 선택 합니다. 

   ![IoT Hub에 대 한 템플릿을 내보내기 위한 명령을 보여 주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. **다운로드** 를 선택 하 여 템플릿을 다운로드 합니다. 파일을 다시 찾을 수 있는 위치에 저장 합니다. 

   ![IoT Hub에 대 한 템플릿을 다운로드 하는 명령을 보여 주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>템플릿 보기 

1. 다운로드 폴더 (또는 템플릿을 내보낼 때 사용한 폴더)로 이동 하 고 zip 파일을 찾습니다. Zip 파일을 열고 `template.json`라는 파일을 찾습니다. 이를 선택 하 고 Ctrl + C를 선택 하 여 템플릿을 복사 합니다. Zip 파일에 없는 다른 폴더로 이동 하 여 파일을 붙여 넣습니다 (Ctrl + V). 이제 편집할 수 있습니다.
 
    다음 예는 라우팅 구성이 없는 일반 허브에 대 한 예입니다. 영역 **westus**에서 **ContosoTestHub29358** 이라는 S1 계층 허브 (1 단위 포함)입니다. 내보낸 템플릿은 다음과 같습니다.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>템플릿 편집 

템플릿을 사용 하 여 새 지역에 새 허브를 만들려면 먼저 일부 변경 작업을 수행 해야 합니다. [VS Code](https://code.visualstudio.com) 또는 텍스트 편집기를 사용 하 여 템플릿을 편집 합니다.

#### <a name="edit-the-hub-name-and-location"></a>허브 이름 및 위치를 편집 합니다.

1. 위쪽에서 매개 변수 섹션을 제거 합니다. 여러 매개 변수를 포함 하지 않기 때문에 허브 이름만 사용 하는 것이 훨씬 더 간단 합니다. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 이전 단계에서 제거한 이름을 매개 변수에서 검색 하지 않고 실제 (새) 이름을 사용 하도록 변경 합니다. 

    새 허브의 경우 원래 허브의 이름과 문자열 *클론* 을 사용 하 여 새 이름을 만듭니다. 허브 이름 및 위치를 정리 하 여 시작 합니다.
    
    이전 버전:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    새 버전: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    다음으로 **경로** 에 대 한 값에 이전 허브 이름이 포함 되어 있음을 알 수 있습니다. 새 항목을 사용 하도록 변경 합니다. 이러한 값은 **eventHubEndpoints** 아래의 **이벤트** 와 **OperationsMonitoringEvents**에 있는 경로 값입니다.

    완료 되 면 이벤트 허브 엔드포인트 섹션은 다음과 같이 표시 됩니다.

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>이동 하지 않는 라우팅 리소스의 키를 업데이트 합니다.

라우팅이 구성 된 허브에 대 한 리소스 관리자 템플릿을 내보내면 해당 리소스에 대 한 키가 내보낸 템플릿에서 제공 되지 않습니다. 배치는 별표로 표시 됩니다. 포털에서 해당 리소스로 이동 하 고 새 허브의 템플릿을 가져오고 허브를 만들기 **전에** 키를 검색 하 여이를 채워야 합니다. 

1. 라우팅 리소스에 필요한 키를 검색 하 여 템플릿에 넣습니다. [Azure Portal](https://portal.azure.com)의 리소스에서 키를 검색할 수 있습니다. 

   예를 들어 메시지를 저장소 컨테이너로 라우팅하는 경우 포털에서 저장소 계정을 찾습니다. 설정 섹션에서 **액세스 키**를 선택한 다음 키 중 하나를 복사 합니다. 템플릿을 처음 내보낼 때의 열쇠는 다음과 같습니다.

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 저장소 계정에 대 한 계정 키를 검색 한 후에는 별표 위치에서 `AccountKey=****` 절의 템플릿에 템플릿에 넣습니다. 

1. Service bus 큐의 경우 SharedAccessKeyName과 일치 하는 공유 액세스 키를 가져옵니다. 다음은 json의 키 및 `SharedAccessKeyName`입니다.

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Service Bus 토픽 및 이벤트 허브 연결에도 동일 하 게 적용 됩니다.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>새 위치에 새 라우팅 리소스 만들기

이 섹션은 허브에서 라우팅 엔드포인트에 사용 하는 리소스를 이동 하는 경우에만 적용 됩니다.

라우팅 리소스를 이동 하려면 새 위치에 리소스를 수동으로 설정 해야 합니다. [Azure Portal](https://portal.azure.com)사용 하 여 라우팅 리소스를 만들거나, 메시지 라우팅에서 사용 하는 각 리소스에 대 한 리소스 관리자 템플릿을 내보내고 편집 하 고 가져올 수 있습니다. 리소스를 설정한 후에는 허브의 템플릿 (라우팅 구성 포함)을 가져올 수 있습니다.

1. 라우팅에 사용 되는 각 리소스를 만듭니다. [Azure Portal](https://portal.azure.com)를 사용 하 여 수동으로이 작업을 수행 하거나 리소스 관리자 템플릿을 사용 하 여 리소스를 만들 수 있습니다. 템플릿을 사용 하려는 경우 다음 단계를 수행 합니다.

    1. 라우팅에 사용 되는 각 리소스에 대해 리소스 관리자 템플릿으로 내보냅니다.
    
    1. 리소스의 이름과 위치를 업데이트 합니다. 

    1. 리소스 간의 상호 참조를 업데이트 합니다. 예를 들어 새 저장소 계정에 대 한 템플릿을 만드는 경우 해당 템플릿의 저장소 계정 이름과이를 참조 하는 다른 템플릿을 업데이트 해야 합니다. 대부분의 경우 허브에 대 한 템플릿의 라우팅 섹션은 리소스를 참조 하는 유일한 다른 템플릿입니다. 

    1. 각 리소스를 배포 하는 각 템플릿을 가져옵니다.

    라우팅에 사용 되는 리소스를 설정 하 고 실행 하면 계속할 수 있습니다.

1. IoT hub에 대 한 템플릿에서 각 라우팅 리소스의 이름을 새 이름으로 변경 하 고 필요한 경우 위치를 업데이트 합니다. 

이제 경로를 처리 하는 방법에 따라 이전 허브와 거의 똑같이 보이는 새 허브를 만드는 템플릿이 있습니다.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>이동--템플릿을 로드 하 여 새 지역에 새 허브를 만듭니다.

템플릿을 사용 하 여 새 위치에 새 허브를 만듭니다. 이동 하려는 라우팅 리소스가 있는 경우 새 위치에 리소스를 설정 하 고 템플릿의 참조를 일치 하도록 업데이트 해야 합니다. 라우팅 리소스를 이동 하지 않는 경우 업데이트 된 키를 사용 하 여 템플릿에 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **리소스 만들기**를 선택합니다. 

1. 검색 상자에서 "템플릿 배포"를 입력 하 고 Enter 키를 선택 합니다.

1. **템플릿 배포 (사용자 지정 템플릿을 사용 하 여 배포)를**선택 합니다. 그러면 템플릿 배포 화면으로 이동 합니다. **만들기**를 선택합니다. 다음 화면이 표시됩니다.

   ![사용자 고유의 템플릿을 빌드하기 위한 명령을 보여 주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. **편집기에서 사용자 고유의 템플릿 빌드**를 선택 하 여 파일에서 템플릿을 업로드할 수 있습니다. 

1. **파일 로드**를 선택 합니다. 

   ![템플릿 파일을 업로드 하는 명령을 보여 주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 편집한 새 템플릿을 찾아 선택한 다음 **열기**를 선택 합니다. 편집 창에서 템플릿을 로드 합니다. **저장**을 선택합니다. 

   ![템플릿 로딩을 보여 주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 다음 필드를 입력합니다.

   **구독**: 사용할 구독을 선택 합니다.

   **리소스 그룹**: 새 위치에 새 리소스 그룹을 만듭니다. 새 설정을 이미 설치한 경우 새 항목을 만드는 대신 선택할 수 있습니다.

   **위치**: 기존 리소스 그룹을 선택한 경우이는 리소스 그룹의 위치와 일치 하도록 채워집니다. 새 리소스 그룹을 만든 경우 해당 그룹의 위치가 됩니다.

   **동의 함 확인란**: 기본적으로 사용자가 만들고 있는 리소스에 대 한 요금을 지불 하는 것에 동의 하는 것입니다.

1. **구매** 단추를 선택합니다.

이제 포털에서 템플릿의 유효성을 검사 하 고 복제 된 허브를 배포 합니다. 라우팅 구성 데이터가 있는 경우이 데이터는 새 허브에 포함 되지만 이전 위치의 리소스를 가리킵니다.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>IoT hub에 등록 된 장치 관리

이제 복제를 실행 하 고 나면 원본 허브의 모든 장치를 복제로 복사 해야 합니다. 

여러 가지 방법으로이를 수행할 수 있습니다. 처음에는 [장치 프로 비전 서비스 (DPS)](/azure/iot-dps/about-iot-dps)를 사용 하 여 장치를 프로 비전 했거나 아직 하지 않았습니다. 수행한 경우에는이 방법이 어렵습니다. 그렇게 하지 않으면 매우 복잡할 수 있습니다. 

장치를 프로 비전 하는 데 DPS를 사용 하지 않은 경우 다음 섹션을 건너뛰고 [Import/Export를 사용 하 여 장치를 새 허브로 이동](#using-import-export-to-move-the-devices-to-the-new-hub)하는 것부터 시작할 수 있습니다.

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS를 사용 하 여 새 허브에 장치 다시 프로 비전

DPS를 사용 하 여 장치를 새 위치로 이동 하려면 장치를 [다시 프로 비전 하는 방법](../iot-dps/how-to-reprovision.md)을 참조 하세요. 작업이 완료 되 면 [Azure Portal](https://portal.azure.com) 에서 장치를 보고 새 위치에 있는지 확인할 수 있습니다.

[Azure Portal](https://portal.azure.com)를 사용 하 여 새 허브로 이동 합니다. 허브를 선택한 다음 **IoT 장치**를 선택 합니다. 복제 된 허브에 다시 프로 비전 된 장치가 표시 됩니다. 복제 된 허브에 대 한 속성을 볼 수도 있습니다. 

라우팅을 구현한 경우를 테스트 하 고 메시지를 리소스로 올바르게 라우트 하는지 확인 합니다.

### <a name="committing-the-changes-after-using-dps"></a>DPS를 사용한 후 변경 내용 커밋

이 변경 내용은 DPS 서비스에 의해 커밋 되었습니다.

### <a name="rolling-back-the-changes-after-using-dps"></a>DPS를 사용한 후 변경 사항을 롤백합니다. 

변경 내용을 롤백하려면 새 허브에서 기존 허브로 장치를 다시 프로 비전 합니다.

이제 허브와 해당 장치 마이그레이션이 완료 되었습니다. [정리](#clean-up)로 건너뛸 수 있습니다.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>가져오기-내보내기를 사용 하 여 장치를 새 허브로 이동

응용 프로그램은 .NET Core를 대상으로 하므로 Windows 또는 Linux에서 실행할 수 있습니다. 샘플을 다운로드 하 고, 연결 문자열을 검색 하 고, 실행 하려는 비트의 플래그를 설정 하 고, 실행할 수 있습니다. 코드를 열지 않고도이 작업을 수행할 수 있습니다.

### <a name="downloading-the-sample"></a>샘플 다운로드

1. 이 페이지의 C# iot 샘플을 사용 합니다. [에 대 한 C#Azure iot 샘플 ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)입니다. Zip 파일을 다운로드 하 고 컴퓨터에 압축을 풉니다. 

1. 관련 코드는/iot-hub/Samples/service/ImportExportDevicesSample.에 있습니다. 응용 프로그램을 실행 하기 위해 코드를 보거나 편집할 필요는 없습니다.

1. 응용 프로그램을 실행 하려면 3 개의 연결 문자열과 5 개의 옵션을 지정 합니다. 이 데이터를 명령줄 인수로 전달 하거나 환경 변수를 사용 하거나 둘의 조합을 사용 합니다. 에서 명령줄 인수로 옵션을 전달 하 고, 연결 문자열을 환경 변수로 전달 합니다. 

   이는 연결 문자열이 길고 ungainly 변경 될 가능성은 거의 없기 때문입니다. 그러나 옵션을 변경 하 고 응용 프로그램을 두 번 이상 실행할 수 있습니다. 환경 변수의 값을 변경 하려면 사용 중인 명령 창과 Visual Studio 또는 VS Code를 닫아야 합니다. 

### <a name="options"></a>옵션

응용 프로그램을 실행할 때 지정 하는 5 가지 옵션은 다음과 같습니다. 이는 명령줄에서 1 분 안에 배치 합니다.

*   **Adddevices** (인수 1)--생성 되는 가상 장치를 추가 하려면 true로 설정 합니다. 이러한 추가는 원본 허브에 추가 됩니다. 또한 **Numtoadd** (인수 2)를 설정 하 여 추가 하려는 장치 수를 지정 합니다. 허브에 등록할 수 있는 장치의 최대 수는 100만입니다. 이 옵션의 목적은 테스트를 위한 것입니다. 특정 수의 장치를 생성 한 다음 다른 허브에 복사할 수 있습니다.

*   **copydevices** (인수 3)--한 허브에서 다른 허브로 장치를 복사 하려면 true로 설정 합니다. 

*   **deleteSourceDevices** (인수 4)--원본 허브에 등록 된 모든 장치를 삭제 하려면 true로 설정 합니다. 이를 실행 하기 전에 모든 장치가 전송 될 때까지 대기를 권장 합니다. 장치를 삭제 한 후에는 다시 가져올 수 없습니다.

*   **Deletedestdevices** (인수 5)--대상 허브 (클론)에 등록 된 모든 장치를 삭제 하려면 true로 설정 합니다. 장치를 두 번 이상 복사 하려는 경우이 작업을 수행 하는 것이 좋습니다. 

기본 명령은 *dotnet run* 이 됩니다. 그러면 .net에서 로컬 .csproj 파일을 빌드한 다음 실행 하도록 지시 합니다. 명령줄 인수를 실행 하기 전에 끝에 추가 합니다. 

명령줄은 다음 예제와 같습니다.

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>연결 문자열에 환경 변수 사용

1. 샘플을 실행 하려면 이전 및 새 IoT hub에 대 한 연결 문자열과 임시 작업 파일에 사용할 수 있는 저장소 계정에 대 한 연결 문자열이 필요 합니다. 이러한 값은 환경 변수에 저장 됩니다.

1. 연결 문자열 값을 가져오려면 [Azure Portal](https://portal.azure.com)에 로그인 합니다. 

1. 메모장과 같이 검색할 수 있는 위치에 연결 문자열을 배치 합니다. 다음을 복사 하는 경우 연결 문자열은 이동 하는 위치에 직접 붙여 넣을 수 있습니다. 등호 앞뒤에 공백을 추가 하지 않거나 변수 이름을 변경 합니다. 또한 연결 문자열 주위에 큰따옴표가 필요 하지 않습니다. 저장소 계정 연결 문자열 주위에 따옴표를 입력 하면 작동 하지 않습니다.

   Windows의 경우 환경 변수를 설정 하는 방법은 다음과 같습니다.

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux의 경우 환경 변수를 정의 하는 방법입니다.

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. IoT hub 연결 문자열의 경우 포털의 각 허브로 이동 합니다. 허브에 대 한 **리소스** 를 검색할 수 있습니다. 리소스 그룹을 알고 있는 **경우 리소스 그룹**으로 이동 하 여 리소스 그룹을 선택 하 고 해당 리소스 그룹의 자산 목록에서 허브를 선택할 수 있습니다. 

1. 허브에 대 한 설정에서 **공유 액세스 정책** 을 선택한 다음 **iothubowner** 를 선택 하 고 연결 문자열 중 하나를 복사 합니다. 대상 허브에 대해 동일한 작업을 수행 합니다. 적절 한 SET 명령에 추가 합니다.

1. 저장소 계정 연결 문자열에 대해 **리소스** 또는 리소스 **그룹** 에서 저장소 계정을 찾아 엽니다. 
   
1. 설정 섹션에서 **액세스 키** 를 선택 하 고 연결 문자열 중 하나를 복사 합니다. 적절 한 SET 명령에 대해 텍스트 파일에 연결 문자열을 입력 합니다. 

이제 SET 명령이 있는 파일에 환경 변수를 사용 하 고 명령줄 인수를 알고 있습니다. 샘플을 실행 해 보겠습니다.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>예제 응용 프로그램 실행 및 명령줄 인수 사용

1. 명령 프롬프트 창을 엽니다. Windows를 선택 하 고 `command prompt` 입력 하 여 명령 프롬프트 창을 가져옵니다.

1. 환경 변수를 설정 하는 명령을 한 번에 하나씩 복사 하 고 명령 프롬프트 창에 붙여넣은 다음 Enter 키를 선택 합니다. 작업이 완료 되 면 명령 프롬프트 창에 `SET`를 입력 하 여 환경 변수와 해당 값을 확인 합니다. 이러한 항목을 명령 프롬프트 창에 복사한 후에는 새 명령 프롬프트 창을 열지 않는 한 다시 복사 하지 않아도 됩니다.

1. 명령 프롬프트 창에서, 사용자가 있을 때까지 디렉터리를 변경 합니다./Svexportdevicessname. 그런 후에 다음을 입력 하 고 명령줄 인수를 포함 합니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Dotnet 명령은 응용 프로그램을 빌드하고 실행 합니다. 응용 프로그램을 실행할 때 옵션을 전달 하므로 응용 프로그램을 실행할 때마다 해당 값을 변경할 수 있습니다. 예를 들어 한 번 실행 하 고 새 장치를 만든 다음 다시 실행 하 고 해당 장치를 새 허브로 복사 하는 등의 방법을 사용할 수 있습니다. 동일한 실행의 모든 단계를 수행할 수도 있지만 복제를 마칠 때까지 장치를 삭제 하지 않는 것이 좋습니다. 1000 장치를 만든 다음 다른 허브에 복사 하는 예제는 다음과 같습니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    장치가 성공적으로 복사 되었는지 확인 한 후 다음과 같이 원본 허브에서 장치를 제거할 수 있습니다.

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Visual Studio를 사용 하 여 샘플 응용 프로그램 실행

1. Visual Studio에서 응용 프로그램을 실행 하려면 현재 디렉터리를 IoTHubServiceSamples 파일이 있는 폴더로 변경 합니다. 그런 다음 명령 프롬프트 창에서이 명령을 실행 하 여 Visual Studio에서 솔루션을 엽니다. 환경 변수를 설정 하는 동일한 명령 창에서이 작업을 수행 해야 합니다. 따라서 이러한 변수를 알 수 있습니다.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 프로젝트 *가져오기* 를 마우스 오른쪽 단추로 클릭 하 고 **시작 프로젝트로 설정**을 선택 합니다.    
    
1. 5 개 옵션에 대해 ImportExportDevicesSample 폴더의 위쪽에 있는 변수를 설정 합니다.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. F5 키를 선택 하 여 응용 프로그램을 실행 합니다. 실행이 완료 되 면 결과를 볼 수 있습니다.

### <a name="view-the-results"></a>결과 보기 

[Azure Portal](https://portal.azure.com) 에서 장치를 보고 새 위치에 있는지 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)를 사용 하 여 새 허브로 이동 합니다. 허브를 선택한 다음 **IoT 장치**를 선택 합니다. 이전 허브에서 복제 된 허브로 방금 복사한 장치가 표시 됩니다. 복제 된 허브에 대 한 속성을 볼 수도 있습니다. 

1. [Azure Portal](https://portal.azure.com) 에서 Azure storage 계정으로 이동 하 고 `ImportErrors.log`에 대 한 `devicefiles` 컨테이너를 살펴보면 가져오기/내보내기 오류를 확인 합니다. 이 파일이 비어 있는 경우 (크기가 0 인 경우) 오류가 발생 하지 않은 것입니다. 동일한 장치를 두 번 이상 가져오려고 시도 하면 장치를 두 번 이상 거부 하 고 로그 파일에 오류 메시지를 추가 합니다.

### <a name="committing-the-changes"></a>변경 내용 커밋 

이제 허브를 새 위치로 복사 하 고 장치를 새 복제본으로 마이그레이션 했습니다. 이제 장치가 복제 된 허브에서 작동 하도록 변경 해야 합니다.

변경 내용을 커밋하려면 수행 해야 하는 단계는 다음과 같습니다. 

* 각 장치를 업데이트 하 여 IoT Hub 호스트 이름을 변경 하 여 IoT Hub 호스트 이름을 새 허브로 가리키도록 합니다. 장치를 처음 프로 비전 할 때 사용한 것과 동일한 방법을 사용 하 여이 작업을 수행 해야 합니다.

* 새 허브를 가리키도록 이전 허브를 참조 하는 응용 프로그램을 변경 합니다.

* 작업을 완료 한 후 새 허브가 실행 되어야 합니다. 이전 허브에는 활성 장치가 없고 연결 되지 않은 상태 여야 합니다. 

### <a name="rolling-back-the-changes"></a>변경 내용 롤백

변경 내용을 롤백하려면 다음 단계를 수행 합니다.

* 각 장치를 업데이트 하 여 IoT Hub 호스트 이름을 변경 하 여 이전 허브의 IoT Hub 호스트 이름을 가리키도록 합니다. 장치를 처음 프로 비전 할 때 사용한 것과 동일한 방법을 사용 하 여이 작업을 수행 해야 합니다.

* 이전 허브를 가리키도록 새 허브를 참조 하는 응용 프로그램을 변경 합니다. 예를 들어 Azure Analytics를 사용 하는 경우 [Azure Stream Analytics 입력](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)을 다시 구성 해야 할 수 있습니다.

* 새 허브를 삭제 합니다. 

* 라우팅 리소스를 사용 하는 경우에는 이전 허브의 구성이 올바른 라우팅 구성을 가리켜야 하며, 허브를 다시 시작한 후에도 이러한 리소스를 사용 해야 합니다.

### <a name="checking-the-results"></a>결과 확인 

결과를 확인 하려면 새 위치의 허브를 가리키도록 IoT 솔루션을 변경 하 고 실행 합니다. 즉, 이전 허브를 사용 하 여 수행한 새 허브와 동일한 작업을 수행 하 고 올바르게 작동 하는지 확인 합니다. 

라우팅을 구현한 경우를 테스트 하 고 메시지를 리소스로 올바르게 라우트 하는지 확인 합니다.

## <a name="clean-up"></a>정리

새 허브가 실행 중이 고 장치가 제대로 작동 하는지 확신할 때까지 정리 하지 마세요. 또한이 기능을 사용 하는 경우 라우팅을 테스트 해야 합니다. 준비가 되 면 다음 단계를 수행 하 여 이전 리소스를 정리 합니다.

* 이전 허브를 아직 삭제 하지 않은 경우 삭제 합니다. 그러면 허브에서 모든 활성 장치가 제거 됩니다.

* 새 위치로 이동한 라우팅 리소스가 있는 경우 이전 라우팅 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT hub를 새 지역의 새 허브로 복제 하 여 장치를 완료 합니다. IoT Hub에서 id 레지스트리에 대 한 대량 작업을 수행 하는 방법에 대 한 자세한 내용은 [IoT Hub 장치 id를 대량으로 가져오기 및 내보내기](iot-hub-bulk-identity-mgmt.md)를 참조 하세요.

허브에 대 한 IoT Hub 및 개발에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)

* [IoT Hub 라우팅 자습서](tutorial-routing.md)

* [IoT Hub 장치 관리 개요](iot-hub-device-management-overview.md)

* 샘플 응용 프로그램을 배포 하려는 경우 [.Net Core 응용 프로그램 배포](https://docs.microsoft.com/dotnet/core/deploying/index)를 참조 하세요.
