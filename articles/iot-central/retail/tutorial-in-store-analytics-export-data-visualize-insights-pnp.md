---
title: 자습서 - Azure IoT Central에서 데이터 내보내기 및 인사이트 시각화
description: 이 자습서에서는 IoT Central에서 데이터를 내보내고 Power BI 대시보드에서 인사이트를 시각화하는 방법을 알아봅니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022089"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>자습서: Azure IoT Central에서 데이터 내보내기 및 Power BI에서 인사이트 시각화



이전 두 자습서에서 **스토어 내 분석 - 체크 아웃** 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 만들고 사용자 지정했습니다. 이 자습서에서는 디바이스에서 수집된 원격 분석을 내보내도록 IoT Central 애플리케이션을 구성합니다. 그런 다음, Power BI를 사용하여 스토어 관리자가 원격 분석에서 파생된 인사이트를 시각화할 수 있는 사용자 지정 대시보드를 만듭니다.

이 자습서에서는 다음 작업 방법을 배웁니다.
> [!div class="checklist"]
> * 원격 분석을 이벤트 허브로 내보내도록 IoT Central 애플리케이션을 구성합니다.
> * Logic Apps를 사용하여 데이터를 이벤트 허브에서 Power BI 스트리밍 데이터 세트로 보냅니다.
> * 스트리밍 데이터 세트의 데이터를 시각화하는 Power BI 대시보드를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 이전의 두 자습서를 완료하려면 [Azure IoT Central에서 스토어 내 분석 애플리케이션을 만들고](./tutorial-in-store-analytics-create-app-pnp.md), [Azure IoT Central에서 운영자 대시보드를 사용자 지정하고 디바이스를 관리합니다](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* Power BI 계정 Power BI 계정이 없는 경우 시작하기 전에 [Power BI Pro 평가판](https://app.powerbi.com/signupredirect?pbi_source=web)에 가입합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이벤트 허브와 논리 앱을 만들기 전에 이를 관리할 리소스 그룹을 만들어야 합니다. 리소스 그룹은 **스토어 내 분석 - 체크 아웃** IoT Central 애플리케이션과 동일한 위치에 있어야 합니다. 리소스 그룹을 만들려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 영역에서 **리소스 그룹**을 선택합니다. 그런 다음, **추가**를 선택합니다.
1. **구독**에서 IoT Central 애플리케이션을 만드는 데 사용한 Azure 구독 이름을 선택합니다.
1. **리소스 그룹** 이름에서 _retail-store-analysis_*를 입력합니다.
1. **지역**에서 IoT Central 애플리케이션에 대해 선택한 것과 동일한 지역을 선택합니다.
1. **검토 + 만들기**를 선택합니다.
1. **검토 + 만들기** 창에서 **만들기**를 선택합니다.

이제 구독에 **retail-store-analysis**라는 리소스 그룹이 있습니다.

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

원격 분석을 내보내도록 소매점 모니터링 애플리케이션을 구성하려면 먼저 내보낸 데이터를 받을 이벤트 허브를 만들어야 합니다. 다음 단계에서는 이벤트 허브를 만드는 방법을 보여 줍니다.

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**를 선택합니다.
1. **Marketplace 검색**에서_Event Hubs_를 입력한 다음, **Enter**를 누릅니다.
1. **Event Hubs** 페이지에서 **만들기**를 선택합니다.
1. **네임스페이스 만들기** 페이지에서 다음 단계를 수행합니다.
    * 고유한 네임스페이스 이름(예: _yourname-Retail-store-analysis_)을 입력합니다. 그러면 시스템에서 사용 가능한 이름인지 확인합니다.
    * **기본** 가격 책정 계층을 선택합니다.
    * IoT Central 애플리케이션을 만드는 데 사용한 것과 동일한 **구독**을 선택합니다.
    * **retail-store-analysis** 리소스 그룹을 선택합니다.
    * IoT Central 애플리케이션에 사용한 것과 동일한 위치를 선택합니다.
    * **만들기**를 선택합니다. 시스템에서 리소스를 프로비저닝할 때까지 몇 분 정도 기다려야 할 수도 있습니다.
1. 포털에서 **retail-store-analysis** 리소스 그룹으로 이동합니다. 배포가 완료될 때가지 기다립니다. **새로 고침**을 선택하여 배포 상태를 업데이트 해야 할 수도 있습니다. 또한 **알림**에서 이벤트 허브 네임스페이스 만들기의 상태를 확인할 수 있습니다.
1. **retail-store-analysis** 리소스 그룹에서 **Event Hubs 네임스페이스**를 선택합니다. 포털에서 **Event Hubs 네임스페이스** 홈페이지가 표시됩니다.

이제 **Event Hubs 네임스페이스**가 있으므로 IoT Central 애플리케이션에서 사용할 **Event Hub**를 만들 수 있습니다.

1. 포털의 **Event Hubs 네임스페이스** 홈페이지에서 **+ Event Hub**를 선택합니다.
1. **Event Hub 만들기** 페이지에서 이름으로 _store-telemetry_를 입력한 다음, **만들기**를 선택합니다.

이제 IoT Central 애플리케이션에서 데이터 내보내기를 구성할 때 사용할 수 있는 이벤트 허브가 있습니다.

![이벤트 허브](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>데이터 내보내기 구성

이제 이벤트 허브가 있으므로 연결된 디바이스에서 원격 분석을 내보내도록 **스토어 내 분석 - 체크 아웃** 애플리케이션을 구성할 수 있습니다. 다음 단계에서는 내보내기를 구성하는 방법을 보여 줍니다.

1. **스토어 내 분석 - 체크 아웃** IoT Central 애플리케이션에 로그인합니다.
1. 왼쪽 창에서 **데이터 내보내기**를 선택합니다.
1. **새로 만들기 > Azure Event Hubs**를 차례로 선택합니다.
1. **표시 이름**으로 _원격 분석 내보내기_를 입력합니다.
1. **Event Hubs 네임스페이스**를 선택합니다.
1. **store-telemetry** 이벤트 허브를 선택합니다.
1. **내보낼 데이터** 섹션에서 **디바이스** 및 **디바이스 템플릿**을 끕니다.
1. **저장**을 선택합니다.

데이터 내보내기에서 원격 분석을 이벤트 허브로 보내기 시작할 때까지 몇 분 정도 걸릴 수 있습니다. 내보내기 상태는 **데이터 내보내기** 페이지에서 확인할 수 있습니다.

![연속 데이터 내보내기 구성](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Power BI 데이터 세트 만들기

Power BI 대시보드에는 소매점 모니터링 애플리케이션의 데이터가 표시됩니다. 이 솔루션에서는 Power BI 스트리밍 데이터 세트를 Power BI 대시보드의 데이터 원본으로 사용합니다. 이 섹션에서는 논리 앱이 이벤트 허브에서 데이터를 전달할 수 있도록 스트리밍 데이터 세트의 스키마를 정의합니다. 다음 단계에서는 환경 센서용 두 개의 스트리밍 데이터 세트와 선점 센서용 하나의 스트리밍 데이터 세트를 만드는 방법을 보여 줍니다.

1. **Power BI** 계정에 로그인합니다.
1. **작업 영역**, **작업 영역 만들기**를 차례로 선택합니다.
1. **작업 영역 만들기** 페이지에서 **작업 영역 이름**으로 _스토어 내 분석 - 체크 아웃_을 입력합니다.
1. **스토어 내 분석 - 체크 아웃 작업 영역 시작** 페이지의 아래쪽으로 스크롤하여 **건너뛰기**를 선택합니다.
1. 작업 영역 페이지에서 **만들기 > 스트리밍 데이터 세트**를 차례로 선택합니다.
1. **새 스트리밍 데이터 세트** 페이지에서 **API**, **다음**을 차례로 선택합니다.
1. **데이터 세트 이름**으로 _영역 1 센서_를 입력합니다.
1. 다음 표에 있는 세 개의 **스트림의 값**을 입력합니다.

    | 값 이름  | 값 형식 |
    | ----------- | ---------- |
    | 타임스탬프   | DateTime   |
    | 습도    | Number     |
    | 온도 | Number     |

1. **기록 데이터 분석**을 켭니다.
1. **만들기**, **완료**를 차례로 선택합니다.
1. **영역 1 센서** 스트리밍 데이터 세트와 동일한 스키마와 설정을 사용하여 **영역 2 센서**라는 다른 스트리밍 데이터 세트를 만듭니다.

이제 두 개의 스트리밍 데이터 세트가 있습니다. 논리 앱에서 원격 분석을 **스토어 내 분석 - 체크 아웃** 애플리케이션에 연결된 두 개의 환경 센서에서 이러한 두 데이터 세트로 라우팅합니다.

![영역 데이터 세트](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

이 솔루션에서는 필터를 Power BI의 스트리밍 데이터에 적용할 수 없으므로 각 센서에 대해 하나의 스트리밍 데이터 세트를 사용합니다.

또한 선점 원격 분석용 스트리밍 데이터 세트도 필요합니다.

1. 작업 영역 페이지에서 **만들기 > 스트리밍 데이터 세트**를 차례로 선택합니다.
1. **새 스트리밍 데이터 세트** 페이지에서 **API**, **다음**을 차례로 선택합니다.
1. **데이터 세트 이름**으로 _선점 센서_를 입력합니다.
1. 다음 표에 있는 5개의 **스트림의 값**을 입력합니다.

    | 값 이름     | 값 형식 |
    | -------------- | ---------- |
    | 타임스탬프      | DateTime   |
    | 큐 길이 1 | Number     |
    | 큐 길이 2 | Number     |
    | 드웰 시간 1   | Number     |
    | 드웰 시간 2   | Number     |

1. **기록 데이터 분석**을 켭니다.
1. **만들기**, **완료**를 차례로 선택합니다.

이제 시뮬레이션된 선점 센서의 값을 저장하는 세 번째 스트리밍 데이터 세트가 있습니다. 이 센서는 스토어에 있는 두 체크 아웃의 큐 길이와 고객이 이러한 큐에서 대기하는 시간을 보고합니다.

![선점 데이터 세트](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>논리 앱 만들기

이 솔루션에서 논리 앱은 이벤트 허브에서 원격 분석을 읽고 데이터를 구문 분석한 다음, 만든 Power BI 스트리밍 데이터 세트로 보냅니다.

논리 앱을 만들기 전에 [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app-pnp.md) 자습서에서 IoT Central 애플리케이션에 연결한 두 개의 RuuviTag 센서의 디바이스 ID가 필요합니다.

1. **스토어 내 분석 - 체크 아웃** IoT Central 애플리케이션에 로그인합니다.
1. 왼쪽 창에서 **디바이스**를 선택합니다. 그런 다음, **RuuviTag**를 선택합니다.
1. **디바이스 ID**를 적어 둡니다. 다음 스크린샷에서 ID는 **f5dcf4ac32e8** 및 **e29ffc8d5326**입니다.

    ![디바이스 ID](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

다음 단계에서는 Azure Portal에서 논리 앱을 만드는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 화면의 왼쪽 위에서 **리소스 만들기**를 선택합니다.
1. **Marketplace 검색**에서 _Logic App_을 입력한 다음, **Enter**를 누릅니다.
1. **Logic App** 페이지에서 **만들기**를 선택합니다.
1. **Logic App 만들기** 페이지에서 다음을 수행합니다.
    * 고유한 논리 앱 이름(예: _yourname-retail-store-analysis_)을 입력합니다.
    * IoT Central 애플리케이션을 만드는 데 사용한 것과 동일한 **구독**을 선택합니다.
    * **retail-store-analysis** 리소스 그룹을 선택합니다.
    * IoT Central 애플리케이션에 사용한 것과 동일한 위치를 선택합니다.
    * **만들기**를 선택합니다. 시스템에서 리소스를 프로비저닝할 때까지 몇 분 정도 기다려야 할 수도 있습니다.
1. Azure Portal에서 새 논리 앱으로 이동합니다.
1. **Logic Apps 디자이너** 페이지에서 아래로 스크롤하여 **비어 있는 논리 앱**을 선택합니다.
1. **커넥터 및 트리거 검색**에서 _Event Hubs_를 입력합니다.
1. **트리거**에서 **이벤트가 Event Hub에서 사용 가능한 경우**를 선택합니다.
1. **연결 이름**으로 _스토어 원격 분석_을 입력하고, **Event Hubs 네임스페이스**를 선택합니다.
1. **RootManageSharedAccess** 정책, **만들기**를 차례로 선택합니다.
1. **이벤트가 Event Hub에서 사용 가능한 경우** 작업에서 다음을 수행합니다.
    * **Event Hub 이름**에서 **store-telemetry**를 선택합니다.
    * **콘텐츠 형식**에서**application/json**을 선택합니다.
    * **간격**을 3으로 설정하고, **빈도**를 초 단위로 설정합니다.
1. **저장**을 선택하여 논리 앱을 저장합니다.

논리를 논리 앱 디자인에 추가하려면 **코드 보기**를 선택합니다.

1. `"actions": {},`를 다음 JSON으로 바꿉니다. 두 자리 표시자(`[YOUR RUUVITAG DEVICE ID 1]` 및 `[YOUR RUUVITAG DEVICE ID 2]`)를 적어 둔 두 RuuviTag 디바이스의 ID로 바꿉니다.

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. **저장**을 선택한 다음, **디자이너**를 선택하여 추가한 논리의 시각적 버전을 확인합니다.

    ![논리 앱 디자인](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. **DeviceID로 전환**을 선택하여 작업을 펼칩니다. 그런 다음, **영역 1 환경**, **작업 추가**를 차례로 선택합니다.
1. **커넥터 및 작업 검색**에서 **Power BI**를 입력한 다음, **Enter** 키를 누릅니다.
1. **데이터 세트에 행 추가(미리 보기)** 작업을 선택합니다.
1. **로그인**을 선택하고, 프롬프트의 지시에 따라 Power BI 계정에 로그인합니다.
1. 로그인 프로세스가 완료되면 **데이터 세트에 행 추가** 작업에서 다음을 수행합니다.
    * 작업 영역으로 **스토어 내 분석 - 체크 아웃**을 선택합니다.
    * 데이터 세트로 **영역 1 센서**를 선택합니다.
    * 테이블로 **RealTimeData**를 선택합니다.
    * **새 매개 변수 추가**를 선택한 다음, **타임스탬프**, **습도** 및 **온도** 필드를 선택합니다.
    * **타임스탬프** 필드를 선택한 다음, **동적 콘텐츠** 목록에서 **x-opt-enquetime**을 선택합니다.
    * **습도** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **humidity**를 선택합니다.
    * **온도** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **temperature**를 선택합니다.
    * **저장**을 선택하여 변경 내용을 저장합니다. **영역 1 환경** 작업은 다음 스크린샷과 같습니다. ![영역 1 환경](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. **영역 2 환경** 작업, **작업 추가**를 차례로 선택합니다.
1. **커넥터 및 작업 검색**에서 **Power BI**를 입력한 다음, **Enter** 키를 누릅니다.
1. **데이터 세트에 행 추가(미리 보기)** 작업을 선택합니다.
1. **데이터 세트에 행 추가 2** 작업에서 다음을 수행합니다.
    * 작업 영역으로 **스토어 내 분석 - 체크 아웃**을 선택합니다.
    * 데이터 세트로 **영역 2 센서**를 선택합니다.
    * 테이블로 **RealTimeData**를 선택합니다.
    * **새 매개 변수 추가**를 선택한 다음, **타임스탬프**, **습도** 및 **온도** 필드를 선택합니다.
    * **타임스탬프** 필드를 선택한 다음, **동적 콘텐츠** 목록에서 **x-opt-enquetime**을 선택합니다.
    * **습도** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **humidity**를 선택합니다.
    * **온도** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **temperature**를 선택합니다.
    **저장**을 선택하여 변경 내용을 저장합니다.  **영역 2 환경** 작업은 다음 스크린샷과 같습니다. ![영역 2 환경](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. **선점** 작업, **인터페이스 ID로 전환** 작업을 차례로 선택합니다.
1. **드웰 시간 인터페이스** 작업, **작업 추가**를 차례로 선택합니다.
1. **커넥터 및 작업 검색**에서 **Power BI**를 입력한 다음, **Enter** 키를 누릅니다.
1. **데이터 세트에 행 추가(미리 보기)** 작업을 선택합니다.
1. **데이터 세트에 행 추가** 작업에서 다음을 수행합니다.
    * 작업 영역으로 **스토어 내 분석 - 체크 아웃**을 선택합니다.
    * 데이터 세트로 **선점 센서**를 선택합니다.
    * 테이블로 **RealTimeData**를 선택합니다.
    * **새 매개 변수 추가**를 선택한 다음, **타임스탬프**, **드웰 시간 1** 및 **드웰 시간 2** 필드를 선택합니다.
    * **타임스탬프** 필드를 선택한 다음, **동적 콘텐츠** 목록에서 **x-opt-enquetime**을 선택합니다.
    * **드웰 시간 1** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **DwellTime1**을 선택합니다.
    * **드웰 시간 2** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **DwellTime2**를 선택합니다.
    * **저장**을 선택하여 변경 내용을 저장합니다. **드웰 시간 인터페이스** 작업은 다음 스크린샷과 같습니다. ![선점 작업](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. **사람 수 인터페이스** 작업, **작업 추가**를 차례로 선택합니다.
1. **커넥터 및 작업 검색**에서 **Power BI**를 입력한 다음, **Enter** 키를 누릅니다.
1. **데이터 세트에 행 추가(미리 보기)** 작업을 선택합니다.
1. **데이터 세트에 행 추가** 작업에서 다음을 수행합니다.
    * 작업 영역으로 **스토어 내 분석 - 체크 아웃**을 선택합니다.
    * 데이터 세트로 **선점 센서**를 선택합니다.
    * 테이블로 **RealTimeData**를 선택합니다.
    * **새 매개 변수 추가**를 선택한 다음, **타임스탬프**, **큐 길이 1** 및 **큐 길이 2** 필드를 선택합니다.
    * **타임스탬프** 필드를 선택한 다음, **동적 콘텐츠** 목록에서 **x-opt-enquetime**을 선택합니다.
    * **큐 길이 1** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **count1**을 선택합니다.
    * **큐 길이 2** 필드를 선택한 다음, **원격 분석 구문 분석** 옆에 있는 **자세히 보기**를 선택합니다. 그런 다음, **count2**를 선택합니다.
    * **저장**을 선택하여 변경 내용을 저장합니다. **사람 수 인터페이스** 작업은 다음 스크린샷과 같습니다. ![선점 작업](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

논리 앱이 자동으로 실행됩니다. 각 실행의 상태를 확인하려면 Azure Portal에서 논리 앱에 대한 **개요** 페이지로 이동합니다.

## <a name="create-a-power-bi-dashboard"></a>Power BI 대시보드 만들기

이제 IoT Central 애플리케이션에서 이벤트 허브를 통해 원격 분석을 전달합니다. 그런 다음, 논리 앱에서 이벤트 허브 메시지를 구문 분석하여 Power BI 스트리밍 데이터 세트에 추가합니다. 이제 원격 분석을 시각화하는 Power BI 대시보드를 만들 수 있습니다.

1. **Power BI** 계정에 로그인합니다.
1. **작업 영역 > 스토어 내 분석 - 체크 아웃**을 차례로 선택합니다.
1. **만들기 > 대시보드**를 차례로 선택합니다.
1. 대시보드 이름으로 **스토어 분석**을 입력하고, **만들기**를 선택합니다.

### <a name="add-line-charts"></a>꺾은선형 차트 추가

4개의 꺾은선형 차트 타일을 추가하여 두 환경 센서의 온도와 습도를 표시합니다. 다음 표의 정보를 사용하여 타일을 만듭니다. 각 타일을 추가하려면 먼저 **...(기타 옵션) > 타일 추가**를 차례로 선택합니다. **사용자 지정 스트리밍 데이터**, **다음**을 차례로 선택합니다.

| 설정 | 차트 #1 | 차트 #2 | 차트 #3 | 차트 #4 |
| ------- | -------- | -------- | -------- | -------- |
| 데이터 세트 | 영역 1 센서 | 영역 1 센서 | 영역 2 센서 | 영역 2 센서 |
| 시각화 형식 | 꺾은선형 차트 | 꺾은선형 차트 | 꺾은선형 차트 | 꺾은선형 차트 |
| 축 | 타임스탬프 | 타임스탬프 | 타임스탬프 | 타임스탬프 |
| 값 | 온도 | 습도 | 온도 | 습도 |
| 기간 | 60분 | 60분 | 60분 | 60분 |
| 제목 | 온도(1시간) | 습도(1시간) | 온도(1시간) | 습도(1시간) |
| 부제 | 영역 1 | 영역 1 | 영역 2 | 영역 2 |

다음 스크린샷에서는 첫 번째 차트의 설정을 보여 줍니다.

![꺾은선형 차트 설정](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>환경 데이터를 표시하는 카드 추가

4개의 카드 타일을 추가하여 두 환경 센서에 있는 가장 최근의 온도 및 습도 값을 표시합니다. 다음 표의 정보를 사용하여 타일을 만듭니다. 각 타일을 추가하려면 먼저 **...(기타 옵션) > 타일 추가**를 차례로 선택합니다. **사용자 지정 스트리밍 데이터**, **다음**을 차례로 선택합니다.

| 설정 | 카드 #1 | 카드 #2 | 카드 #3 | 카드 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 데이터 세트 | 영역 1 센서 | 영역 1 센서 | 영역 2 센서 | 영역 2 센서 |
| 시각화 형식 | 카드 | 카드 | 카드 | 카드 |
| 필드 | 온도 | 습도 | 온도 | 습도 |
| 제목 | 온도(F) | 습도(%) | 온도(F) | 습도(%) |
| 부제 | 영역 1 | 영역 1 | 영역 2 | 영역 2 |

다음 스크린샷에서는 첫 번째 카드의 설정을 보여 줍니다.

![카드 설정](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>체크 아웃 선점 데이터를 표시하는 타일 추가

4개의 카드 타일을 추가하여 스토어에 있는 두 체크 아웃의 큐 길이와 드웰 시간을 표시합니다. 다음 표의 정보를 사용하여 타일을 만듭니다. 각 타일을 추가하려면 먼저 **...(기타 옵션) > 타일 추가**를 차례로 선택합니다. **사용자 지정 스트리밍 데이터**, **다음**을 차례로 선택합니다.

| 설정 | 카드 #1 | 카드 #2 | 카드 #3 | 카드 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 데이터 세트 | 선점 센서 | 선점 센서 | 선점 센서 | 선점 센서 |
| 시각화 형식 | 묶은 세로 막대형 차트 | 묶은 세로 막대형 차트 | 계기 | 계기 |
| 축    | 타임스탬프 | 타임스탬프 | 해당 없음 | 해당 없음 |
| 값 | 드웰 시간 1 | 드웰 시간 2 | 큐 길이 1 | 큐 길이 2 |
| 기간 | 60분 | 60분 |  해당 없음 | 해당 없음 |
| 제목 | 드웰 시간 | 드웰 시간 | 큐 길이 | 큐 길이 |
| 부제 | 체크 아웃 1 | 체크 아웃 2 | 체크 아웃 1 | 체크 아웃 2 |

다음 스크린샷과 같이 대시보드의 타일 크기를 조정하고 다시 정렬합니다.

![Power BI 대시보드](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

몇 가지 추가 그래픽 리소스를 추가하여 대시보드를 추가로 사용자 지정할 수 있습니다.

![Power BI 대시보드](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>리소스 정리

IoT Central 애플리케이션이 완료되면 애플리케이션에 로그인하고, **관리** 섹션의 **애플리케이션 설정** 페이지로 이동하여 삭제할 수 있습니다.

애플리케이션을 유지하면서도 관련 비용을 줄이려면 원격 분석을 이벤트 허브로 보내는 데이터 내보내기를 사용하지 않도록 설정합니다.

**retail-store-analysis**라는 리소스 그룹을 삭제하여 Azure Portal에서 이벤트 허브와 논리 앱을 삭제할 수 있습니다.

작업 영역의 Power BI 설정 페이지에서 작업 영역을 삭제하여 Power BI 데이터 세트와 대시보드를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 세 가지 자습서에서는 **스토어 내 분석 - 체크 아웃** IoT Central 애플리케이션 템플릿을 사용하는 엔드투엔드 솔루션을 보여 주었습니다. 디바이스를 애플리케이션에 연결하고, IoT Central을 사용하여 디바이스를 모니터링하고, Power BI를 사용하여 디바이스 원격 분석에서 인사이트를 볼 수 있는 대시보드를 빌드했습니다. 추천되는 다음 단계는 다른 IoT Central 애플리케이션 템플릿 중 하나를 살펴보는 것입니다.

> [!div class="nextstepaction"]
> * [IoT Central을 사용하여 에너지 산업 솔루션 빌드](../energy/overview-iot-central-energy.md)
> * [IoT Central을 사용하여 정부 기관 솔루션 빌드](../government/overview-iot-central-government.md)
> * [IoT Central을 사용하여 의료 산업 솔루션 빌드](../healthcare/overview-iot-central-healthcare.md)
