---
title: Azure Portal을 사용하여 IoT Hub 만들기 | Microsoft 문서
description: Azure Portal을 통해 Azure IoT Hub를 만들고 관리하고 삭제하는 방법입니다. 가격 책정 계층, 보안, 배율 및 메시징 구성에 대한 정보가 포함됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 21758d112cd7eaf45b8b279cf2a6f6060c44c9c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742034"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 IoT 허브를 만들고 관리하는 방법에 대해 설명합니다.

이 자습서의 단계를 사용하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub의 설정 변경

만든 기존 IoT 허브의 설정은 IoT Hub 창에서 변경할 수 있습니다.

![IoT Hub에 대한 설정을 보여 주는 스크린샷](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

IoT 허브에 설정할 수 있는 몇 가지 속성은 다음 과같습니다.

**가격 책정 및 규모**: 이 속성을 사용 하 여 다른 계층으로 마이그레이션하거나 단위 IoT Hub의 수를 설정할 수 있습니다. 

**작업 모니터링**: 장치-클라우드 메시지 또는 클라우드-장치 메시지와 관련 된 이벤트에 대 한 로깅 같은 끄거나 다른 모니터링 범주를 설정 합니다.

**IP 필터**: 허용 또는 IoT hub에서 거부 될 IP 주소를 지정 합니다.

**Properties**: 복사 하 고 리소스 ID, 리소스 그룹, 위치 등 다른 곳에서 사용할 수 있는 속성의 목록을 제공 합니다.

### <a name="shared-access-policies"></a>공유 액세스 정책

**설정** 섹션에서 **공유 액세스 정책**을 클릭하여 공유 액세스 정책 목록을 보거나 수정할 수도 있습니다. 이러한 정책은 디바이스와 서비스에서 IoT Hub에 연결할 수 있는 권한을 정의합니다. 

**추가**를 클릭하여 **공유 액세스 정책 추가** 블레이드를 엽니다.  다음 그림과 같이 새 정책 이름과 이 정책에 연결하려는 권한을 입력할 수 있습니다.

![공유 액세스 정책 추가를 보여 주는 스크린샷](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* **레지스트리 읽기** 및 **레지스트리 쓰기** 정책은 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 쓰기 옵션을 선택하면 읽기 옵션도 자동으로 선택됩니다.

* **서비스 연결** 정책은 **디바이스-클라우드 수신**과 같은 서비스 엔드포인트에 액세스할 수 있는 권한을 부여합니다. 

* **디바이스 연결** 정책은 IoT Hub 디바이스 쪽 엔드포인트를 사용하여 메시지를 주고받기 위한 권한을 부여합니다.

**만들기** 를 클릭하여 새로 만들어진 이 정책을 기존 목록에 추가합니다.

## <a name="message-routing-for-an-iot-hub"></a>IoT 허브에 대한 메시지 라우팅

**메시지** 아래의 **메시지 라우팅**을 클릭하여 허브에 대한 경로 및 사용자 지정 엔드포인트를 정의하는 [메시지 라우팅] 창을 확인합니다. [메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 사용하면 데이터를 디바이스에서 엔드포인트로 보내는 방법을 관리할 수 있습니다. 첫 번째 단계는 새 경로를 추가하는 것입니다. 그런 다음, 이 경로에 기존 엔드포인트를 추가하거나 Blob Storage와 같이 지원되는 유형 중 하나를 새로 만들 수 있습니다. 

![메시지 라우팅 창](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>경로

[경로]는 [메시지 라우팅] 창의 첫 번째 탭입니다. 새 경로를 추가하려면 **+ 추가**를 클릭합니다. 다음과 같은 화면이 표시됩니다. 

![새 경로 추가를 보여 주는 스크린샷](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

허브 이름을 지정합니다. 이름은 해당 허브에 대한 경로 목록 내에서 고유해야 합니다. 

**엔드포인트**의 경우 드롭다운 목록에서 하나를 선택하거나 새로 추가할 수 있습니다. 이 예에서는 저장소 계정과 컨테이너를 이미 사용할 수 있습니다. 이러한 항목을 엔드포인트로 추가하려면 [엔드포인트] 드롭다운 옆에 있는 **+ 추가**를 클릭하고 **Blob Storage**를 선택합니다. 다음 화면에서는 저장소 계정 및 컨테이너가 지정된 위치를 보여 줍니다.

![라우팅 규칙에 대한 저장소 엔드포인트의 추가를 보여 주는 스크린샷](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

**컨테이너 선택**을 클릭하여 저장소 계정과 컨테이너를 선택합니다. 이러한 필드가 선택되었으면 [엔드포인트] 분할 창으로 돌아갑니다. 나머지 필드에는 기본값을 사용하고, **만들기**를 선택하여 저장소 계정에 대한 엔드포인트를 만들고 이를 라우팅 규칙에 추가합니다.

**데이터 원본**에 대해 [디바이스 원격 분석 메시지]를 선택합니다. 

다음으로, 라우팅 쿼리를 추가합니다. 이 예에서는 `critical`과 동일한 값이 있는 `level`이라는 애플리케이션 속성이 있는 메시지가 저장소 계정으로 라우팅됩니다.

![새 경로 규칙 저장을 보여 주는 스크린샷](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

**저장**을 클릭하여 라우팅 규칙을 저장합니다. [메시지 라우팅] 창으로 돌아가고 새 라우팅 규칙이 표시됩니다.

### <a name="custom-endpoints"></a>사용자 지정 엔드포인트

**사용자 지정 엔드포인트** 탭을 클릭합니다. 이미 만든 사용자 지정 엔드포인트가 표시됩니다. 여기서는 새 엔드포인트를 추가하거나 기존 엔드포인트를 삭제할 수 있습니다. 

> [!NOTE]
> 경로를 삭제하더라도 해당 경로에 할당된 엔드포인트는 삭제되지 않습니다. 엔드포인트를 삭제하려면 [사용자 지정 엔드포인트] 탭을 클릭하고, 삭제하려는 엔드포인트를 선택한 다음,[삭제]를 클릭합니다.
>

[참조 - IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)에서 사용자 지정 엔드포인트에 대해 자세히 알아볼 수 있습니다.

IoT 허브에 대해 최대 10개의 사용자 지정 엔드포인트를 정의할 수 있습니다. 

라우팅을 통해 사용자 지정 엔드포인트를 사용하는 방법에 대한 전체 예제를 보려면 [IoT 허브로 메시지 라우팅](tutorial-routing.md)을 참조하세요.

## <a name="find-a-specific-iot-hub"></a>특정 IoT 허브 찾기

구독에서 특정 IoT 허브를 찾는 두 가지 방법은 다음과 같습니다.

1. IoT 허브가 속한 리소스 그룹을 알고 있으면 **리소스 그룹**을 클릭한 다음, 목록에서 해당 리소스 그룹을 선택합니다. 리소스 그룹 화면에는 IoT 허브를 포함하여 해당 그룹의 모든 리소스가 표시됩니다. 찾고 있는 허브를 클릭합니다.

2. **모든 리소스**를 클릭합니다. **모든 리소스** 창에는 기본적으로 `All types`로 지정된 드롭다운 목록이 있습니다. 드롭다운 목록을 클릭하고 `Select all`을 선택 취소합니다. `IoT Hub`을 찾아서 확인합니다. 드롭다운 목록 상자를 클릭하여 닫습니다. 그러면 항목이 필터링되어 IoT 허브만 표시됩니다.

## <a name="delete-the-iot-hub"></a>IoT Hub 삭제

Iot 허브를 삭제하려면 삭제하려는 IoT 허브를 찾은 다음, IoT 허브 이름 아래에 있는 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT Hub로 메시지 라우팅](tutorial-routing.md)
* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)