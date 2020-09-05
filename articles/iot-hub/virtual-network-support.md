---
title: 가상 네트워크에 대한 Azure IoT Hub 지원
description: IoT Hub에서 가상 네트워크 연결 패턴을 사용하는 방법
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: jlian
ms.openlocfilehash: 3c097260812e72dfaa3678a4aade556a337e6a6c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272903"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Private Link 및 관리 ID를 사용하는 가상 네트워크에 대한 IoT Hub 지원

기본적으로 IoT Hub의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 갖는 퍼블릭 엔드포인트에 매핑됩니다. 이 IoT Hub 퍼블릭 엔드포인트는 여러 고객이 공유하며, 광역 네트워크와 온-프레미스 네트워크에 있는 IoT 디바이스가 여기에 액세스할 수 있습니다.

![IoT Hub 퍼블릭 엔드포인트](./media/virtual-network-support/public-endpoint.png)

[메시지 라우팅](./iot-hub-devguide-messages-d2c.md), [파일 업로드](./iot-hub-devguide-file-upload.md), [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)를 비롯한 IoT Hub 기능을 사용할 때도 IoT Hub에서 고객 소유 Azure 리소스에 대한 퍼블릭 엔드포인트를 통한 연결이 필요합니다. 이러한 연결 경로는 종합적으로 IoT Hub에서 고객 리소스까지의 송신 트래픽을 구성합니다.

사용자가 소유하고 운영하는 VNet을 통해 Azure 리소스(IoT Hub 포함)의 연결을 제한하는 것이 좋습니다. 그 이유는 다음과 같습니다.

* 공용 인터넷에 대한 연결 노출을 방지하여 IoT 허브의 네트워크 격리를 도입

* 온-프레미스 네트워크 자산에서 프라이빗 연결 환경을 사용하도록 설정하여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송되도록 합니다.

* 중요한 온-프레미스 네트워크에서의 반출 공격을 방지합니다. 

* [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Azure 전역의 연결 패턴을 설정합니다.

이 문서에서는 IoT Hub에서의 수신 연결을 위해 [Azure Private Link](../private-link/private-link-overview.md)를 사용하고 IoT Hub에서 다른 Azure 리소스까지로의 송신 연결을 위해 신뢰할 수 있는 Microsoft 서비스 예외를 사용하여 이 목표를 달성하는 방법을 설명합니다.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Azure Private Link를 사용한 IoT Hub로의 수신 연결

프라이빗 엔드포인트는 Azure 리소스에 연결할 수 있는 고객 소유의 VNet 내에 할당된 프라이빗 IP 주소입니다. Azure Private Link를 통해 IoT 허브의 프라이빗 엔드포인트를 설정하여 VNet 내부 서비스가 IoT Hub의 퍼블릭 엔드포인트로 트래픽을 보내지 않아도 IoT Hub에 도달하도록 허용할 수 있습니다. 마찬가지로, 온-프레미스 디바이스는 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 피어링을 사용하여 VNet 및 IoT Hub(프라이빗 엔드포인트를 통해)에 연결할 수 있습니다. 따라서 [IoT Hub IP 필터](./iot-hub-ip-filtering.md)를 사용하고 [기본 제공 엔드포인트에 데이터를 보내지 않도록 라우팅을 구성](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint)하여 IoT 허브의 퍼블릭 엔드포인트에 대한 연결을 제한하거나 완전히 차단할 수 있습니다. 이 방법은 디바이스의 프라이빗 엔드포인트를 사용하여 허브에 대한 연결을 유지합니다. 이 설정은 주요 온-프레미스 네트워크에 있는 디바이스에 적용되며, 광역 네트워크에 배포된 디바이스에는 권장되지 않습니다.

![IoT Hub 퍼블릭 엔드포인트](./media/virtual-network-support/virtual-network-ingress.png)

계속하기 전에 다음과 같은 전제 조건을 충족하는지 확인하세요.

* 프라이빗 엔드포인트를 만들 [Azure VNet](../virtual-network/quick-create-portal.md)과 서브넷을 만들었습니다.

* 온-프레미스 네트워크에서 작동하는 디바이스의 경우, VNet에 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 프라이빗 피어링을 설정합니다.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub 수신을 위한 프라이빗 엔드포인트 설정

개인 끝점은 장치-클라우드 메시지와 같은 IoT Hub 장치 Api 및 서비스 Api (예: 장치 만들기 및 업데이트)에 대해 작동 합니다.

1. Azure portal에서 **네트워킹**, **프라이빗 엔드포인트 연결**을 선택하고 **+ 프라이빗 엔드포인트**를 클릭합니다.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="IoT Hub의 프라이빗 엔드포인트를 추가할 곳을 보여 주는 스크린샷":::

1. 새 프라이빗 엔드포인트를 만들 구독, 리소스 그룹, 이름, 영역을 지정합니다. 프라이빗 엔드포인트는 허브와 같은 영역에 만드는 것이 가장 좋습니다.

1. **다음: 리소스**를 클릭하고 IoT Hub 리소스의 구독을 지정한 다음 리소스 유형으로 **“Microsoft.Devices/IotHubs”** 를 선택하고, **리소스**로 IoT Hub 이름을 선택하고, 대상 하위 리소스로 **iotHub**를 선택합니다.

1. **다음: 구성**을 클릭하고 프라이빗 엔드포인트를 만들 가상 네트워크와 서브넷을 지정합니다. 원하는 경우 Azure 프라이빗 DNS 영역과의 통합 옵션을 선택합니다.

1. **다음: 태그**를 클릭하고 선택적으로 리소스의 태그를 지정합니다.

1. **검토 + 만들기**를 클릭하여 프라이빗 링크 리소스를 만듭니다.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>기본 제공 이벤트 허브 호환 엔드포인트는 프라이빗 엔드포인트를 통한 액세스를 지원하지 않음

[기본 제공 이벤트 허브 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)는 프라이빗 엔드포인트를 통한 액세스를 지원하지 않습니다. IoT 허브의 프라이빗 엔드포인트가 구성된 경우 이 엔드포인트는 수신 연결 전용입니다. 기본 제공 이벤트 허브 호환 엔드포인트의 데이터를 사용하는 것은 공용 인터넷을 통해서만 가능합니다. 

IoT Hub의 [IP 필터](iot-hub-ip-filtering.md)도 기본 제공 엔드포인트에 대한 공용 액세스를 제어하지 않습니다. IoT 허브에 대한 공용 네트워크 액세스를 완전히 차단하려면 다음을 수행해야 합니다. 

1. IoT Hub에 대한 프라이빗 엔드포인트 액세스 구성
1. [공용 네트워크 액세스를 끄거나](iot-hub-public-network-access.md) ip 필터를 사용 하 여 모든 ip 차단
1. [데이터를 전송 하지 않도록 라우팅을 설정](iot-hub-devguide-messages-d2c.md) 하 여 기본 제공 이벤트 허브 끝점 사용을 중지 합니다.
1. [대체 경로](iot-hub-devguide-messages-d2c.md#fallback-route) 끄기
1. [신뢰할 수 있는 Microsoft 서비스](#egress-connectivity-from-iot-hub-to-other-azure-resources)를 사용하여 다른 Azure 리소스에 대한 송신 구성

### <a name="pricing-for-private-link"></a>Private Link의 가격

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub에서 다른 Azure 리소스로의 송신 연결

IoT Hub는 Azure Blob Storage, 이벤트 허브, 서비스 버스 리소스에 연결하여 해당 리소스의 퍼블릭 엔드포인트를 통해 [메시지 라우팅](./iot-hub-devguide-messages-d2c.md), [파일 업로드](./iot-hub-devguide-file-upload.md) 및 [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)를 수행할 수 있습니다. 리소스를 VNet 블록에 바인딩하면 기본적으로 리소스에 대한 연결이 차단됩니다. 그 결과 이 구성은 IoT Hub가 리소스로 데이터를 보내는 작업을 방지합니다. 이 문제를 해결하려면 IoT Hub에서 **신뢰할 수 있는 Microsoft 서비스** 옵션을 통해 스토리지 계정, 이벤트 허브 또는 서비스 버스 리소스에 대한 연결을 설정하세요.

### <a name="turn-on-managed-identity-for-iot-hub"></a>IoT Hub에 대해 관리 ID 켜기

다른 서비스가 신뢰할 수 있는 Microsoft 서비스로서 IoT 허브를 발견할 수 있도록 하려면 IoT 허브에 시스템에서 할당한 관리 ID가 있어야 합니다.

1. IoT Hub 포털에서 **ID**로 이동합니다.

1. **상태**에서 **켜기**를 선택하고 **저장**을 클릭합니다.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="IoT Hub의 관리 ID를 켜는 방법을 보여 주는 스크린샷":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>ARM 템플릿을 사용 하 여 만들 때 IoT Hub에 관리 id 할당

리소스 프로 비전 시간에 관리 되는 id를 IoT hub에 할당 하려면 아래 ARM 템플릿을 사용 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

리소스, 및에 대 한 값을 대체 한 후 `name` `location` Azure CLI를 사용 하 `SKU.name` `SKU.tier` 여 다음을 사용 하 여 기존 리소스 그룹에 리소스를 배포할 수 있습니다.

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

리소스를 만든 후 Azure CLI를 사용 하 여 허브에 할당 된 관리 서비스 id를 검색할 수 있습니다.

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>관리 ID의 가격

신뢰할 수 있는 Microsoft 자사 서비스 예외 기능은 무료입니다. 프로비저닝된 스토리지 계정, 이벤트 허브 또는 서비스 버스 리소스의 요금은 개별적으로 적용됩니다.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>라우팅을 위한 스토리지 계정 엔드포인트로의 송신 연결

IoT Hub는 고객 소유 스토리지 계정으로 메시지를 라우팅할 수 있습니다. 방화벽 제한 사항이 있는 경우에도 라우팅 기능이 스토리지 계정에 액세스할 수 있도록 하려면 IoT Hub에 [관리 ID](#turn-on-managed-identity-for-iot-hub)가 있어야 합니다. 관리 ID를 프로비저닝한 후에는 아래 단계에 따라 허브의 리소스 ID에 스토리지 계정에 액세스할 수 있는 RBAC 권한을 부여합니다.

1. Azure Portal에서 스토리지 계정의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가**를 클릭합니다.

2. **Storage Blob 데이터 기여자**를 **역할**로 선택하고([기여자나 스토리지 계정 기여자가 ‘아닌’](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)), **Azure AD 사용자, 그룹 또는 서비스 주체**를 **액세스 할당 중**으로 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 스토리지 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동합니다.

5. **사용자 지정 엔드포인트** 섹션으로 이동하여 **추가**를 클릭합니다. 엔드포인트 형식으로 **스토리지**를 선택합니다.

6. 표시되는 페이지에서 엔드포인트의 이름을 지정하고, Blob Storage에서 사용할 컨테이너를 선택하고, 인코딩과 파일 이름 형식을 지정합니다. **Id 기반** 을 저장소 끝점에 대 한 **인증 형식** 으로 선택 합니다. **만들기** 단추를 클릭합니다.

이제 사용자 지정 스토리지 엔드포인트가 허브의 시스템 할당 ID를 사용하도록 설정되었고 방화벽 제한에도 불구하고 스토리지 리소스에 액세스할 권한을 갖게 되었습니다. 이제 이 엔드포인트를 사용하여 라우팅 규칙을 설정할 수 있습니다.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>라우팅을 위한 이벤트 허브 엔드포인트로의 송신 연결

IoT Hub는 메시지를 고객 소유 이벤트 허브 네임스페이스로 라우팅하도록 구성할 수 있습니다. 방화벽 제한 사항이 있는 경우에도 라우팅 기능이 이벤트 허브 리소스에 액세스할 수 있도록 하려면 IoT Hub에 관리 ID가 있어야 합니다. 관리 ID를 만든 후에는 아래 단계에 따라 허브의 리소스 ID에 이벤트 허브에 액세스할 수 있는 RBAC 권한을 부여합니다.

1. Azure Portal에서 이벤트 허브의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가**를 클릭합니다.

2. **Event Hubs 데이터 보낸 사람**을 **역할**로 선택하고, **Azure AD 사용자, 그룹 또는 서비스 주체**를 **액세스 할당 중**으로 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 이벤트 허브의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이벤트 허브에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동합니다.

5. **사용자 지정 엔드포인트** 섹션으로 이동하여 **추가**를 클릭합니다. 엔드포인트 형식으로 **이벤트 허브**를 선택합니다.

6. 표시되는 페이지에서 엔드포인트의 이름을 지정하고, 이벤트 허브 네임스페이스와 인스턴스를 선택하고, **만들기** 단추를 클릭합니다.

이제 사용자 지정 이벤트 허브 엔드포인트가 허브의 시스템 할당 ID를 사용하도록 설정되었고 방화벽 제한에도 불구하고 이벤트 허브 리소스에 액세스할 권한을 갖게 되었습니다. 이제 이 엔드포인트를 사용하여 라우팅 규칙을 설정할 수 있습니다.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>라우팅을 위한 서비스 버스 엔드포인트로의 송신 연결

IoT Hub는 메시지를 고객 소유 서비스 버스 네임스페이스로 라우팅하도록 구성할 수 있습니다. 방화벽 제한 사항이 있는 경우에도 라우팅 기능이 서비스 버스 리소스에 액세스할 수 있도록 하려면 IoT Hub에 관리 ID가 있어야 합니다. 관리 ID를 프로비저닝한 후에는 아래 단계에 따라 허브의 리소스 ID에 서비스 버스에 액세스할 수 있는 RBAC 권한을 부여합니다.

1. Azure Portal에서 서비스 버스의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가**를 클릭합니다.

2. **Service Bus 데이터 보낸 사람**을 **역할**로 선택하고, **Azure AD 사용자, 그룹 또는 서비스 주체**를 **액세스 할당 중**으로 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 서비스 버스의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이 서비스 버스에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **메시지 라우팅** 탭으로 이동합니다.

5. **사용자 지정 엔드포인트** 섹션으로 이동하여 **추가**를 클릭합니다. 엔드포인트 형식으로 **서비스 버스 큐** 또는 **Service Bus 토픽**(해당하는 바에 따라)을 선택합니다.

6. 표시되는 페이지에서 엔드포인트의 이름을 지정하고, 서비스 버스 네임스페이스와 큐 또는 토픽(해당하는 바에 따라)을 선택합니다. **만들기** 단추를 클릭합니다.

이제 사용자 지정 서비스 버스 엔드포인트가 허브의 시스템 할당 ID를 사용하도록 설정되었고 방화벽 제한에도 불구하고 서비스 버스 리소스에 액세스할 권한을 갖게 되었습니다. 이제 이 엔드포인트를 사용하여 라우팅 규칙을 설정할 수 있습니다.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>파일 업로드를 위한 스토리지 계정으로의 송신 연결

IoT Hub의 파일 업로드 기능에서는 디바이스가 고객 소유 스토리지 계정으로 파일을 업로드할 수 있도록 허용합니다. 파일 업로드가 기능하도록 하려면 디바이스와 IoT Hub가 모두 스토리지 계정에 연결되어 있어야 합니다. 스토리지 계정에서 방화벽 제한이 적용되어 있는 경우, 디바이스는 스토리지 계정의 지원되는 메커니즘([프라이빗 엔드포인트](../private-link/create-private-endpoint-storage-portal.md), [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [직접 방화벽 구성](../storage/common/storage-network-security.md))을 사용하여 연결을 설정해야 합니다. 마찬가지로, 스토리지 계정에서 방화벽 제한이 적용되어 있는 경우, IoT Hub는 신뢰할 수 있는 Microsoft 서비스 예외를 통해 스토리지 리소스에 액세스하도록 구성되어야 합니다. 이를 위해 IoT Hub에는 관리 ID가 있어야 합니다. 관리 ID를 프로비저닝한 후에는 아래 단계에 따라 허브의 리소스 ID에 스토리지 계정에 액세스할 수 있는 RBAC 권한을 부여합니다.

1. Azure Portal에서 스토리지 계정의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가**를 클릭합니다.

2. **Storage Blob 데이터 기여자**를 **역할**로 선택하고([기여자나 스토리지 계정 기여자가 ‘아닌’](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)), **Azure AD 사용자, 그룹 또는 서비스 주체**를 **액세스 할당 중**으로 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 스토리지 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다.

4. IoT Hub의 리소스 페이지에서 **파일 업로드** 탭으로 이동합니다.

5. 표시되는 페이지에서 Blob Storage에서 사용할 컨테이너를 선택하고, **파일 알림 설정**, **SAS TTL**, **기본 TTL** 및 **최대 전달 횟수**를 원하는 대로 구성합니다. **Id 기반** 을 저장소 끝점에 대 한 **인증 형식** 으로 선택 합니다. **만들기** 단추를 클릭합니다.

이제 파일 업로드를 위한 스토리지 엔드포인트가 허브의 시스템 할당 ID를 사용하도록 설정되었고 방화벽 제한에도 불구하고 스토리지 리소스에 액세스할 권한을 갖게 되었습니다.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>대량 디바이스 가져오기/내보내기를 위한 스토리지 계정으로의 송신 연결

IoT Hub는 고객이 제공한 스토리지 Blob에서/(으)로 디바이스의 정보를 대량으로 [가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)하는 기능을 지원합니다. 대량 가져오기/내보내기가 기능하도록 하려면 디바이스와 IoT Hub가 모두 스토리지 계정에 연결되어 있어야 합니다.

이 기능을 사용하려면 IoT Hub에서 스토리지 계정으로의 연결이 필요합니다. 방화벽 제한 사항이 있는 경우에도 서비스 버스 리소스에 액세스할 수 있도록 하려면 IoT Hub에 관리 ID가 있어야 합니다. 관리 ID를 프로비저닝한 후에는 아래 단계에 따라 허브의 리소스 ID에 서비스 버스에 액세스할 수 있는 RBAC 권한을 부여합니다.

1. Azure Portal에서 스토리지 계정의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가**를 클릭합니다.

2. **Storage Blob 데이터 기여자**를 **역할**로 선택하고([기여자나 스토리지 계정 기여자가 ‘아닌’](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)), **Azure AD 사용자, 그룹 또는 서비스 주체**를 **액세스 할당 중**으로 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 단추를 클릭합니다.

3. 스토리지 계정의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다.

이제 Azure IoT REST API를 사용하여 [가져오기 내보내기 작업을 만들어서](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) 대량 가져오기/내보내기 기능을 사용하는 방법에 대한 정보를 확인할 수 있습니다. 요청 본문에 `storageAuthenticationType="identityBased"`를 제공하고 `inputBlobContainerUri="https://..."` 및 `outputBlobContainerUri="https://..."`를 스토리지 계정의 입력 및 출력 URL로 사용해야 합니다.

Azure IoT Hub SDK는 서비스 클라이언트의 레지스트리 관리자에서도 이 기능을 지원합니다. 다음 코드 조각은 C# SDK를 사용하여 가져오기 작업 또는 내보내기 작업을 시작하는 방법을 보여 줍니다.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

이 버전의 Azure IoT SDK를 C#, Java, Node.js에 대한 가상 네트워크 지원과 함께 사용하려면

1. `EnableStorageIdentity`라는 환경 변수를 만들고 값을 `1`로 설정합니다.

2. SDK를 다운로드합니다.  [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Python의 경우 GitHub에서 제한된 버전을 다운로드합니다.

1. [GitHub 릴리스 페이지](https://aka.ms/vnetpythonsdk)로 이동합니다.

2. 다음 파일을 다운로드합니다. 파일은 릴리스 페이지 하단의 **자산** 아래에서 찾을 수 있습니다.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. 터미널을 열고 다운로드한 파일이 있는 폴더로 이동합니다.

4. 다음 명령을 실행하여 가상 네트워크에 대한 지원이 포함된 Python Service SDK를 설치합니다.
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>다음 단계

IoT Hub 기능에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)
* [파일 업로드](./iot-hub-devguide-file-upload.md)
* [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md) 
