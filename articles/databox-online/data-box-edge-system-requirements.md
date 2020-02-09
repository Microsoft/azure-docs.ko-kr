---
title: Microsoft Azure Data Box Edge 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Edge의 소프트웨어 및 네트워킹 요구 사항 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757869"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure Data Box Edge 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box Edge 솔루션 및 Azure Data Box Edge에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box Edge를 배포하기 전에 정보를 신중하게 검토하는 것이 좋습니다. 배포 및 후속 작업 중에 필요하면 이 정보를 다시 참조할 수 있습니다.

Data Box Edge를 사용하기 위한 시스템 요구 사항은 다음과 같습니다.

- **호스트의 소프트웨어 요구 사항**: 지원되는 플랫폼, 로컬 구성 UI용 브라우저, SMB 클라이언트, 디바이스에 액세스하는 클라이언트에 대한 추가 요구 사항을 설명합니다.
- **디바이스의 네트워킹 요구 사항**: 물리적 디바이스가 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.

## <a name="supported-os-for-clients-connected-to-device"></a>디바이스에 연결된 클라이언트에 지원되는 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>디바이스에 액세스하는 클라이언트에 지원되는 프로토콜

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>로컬 웹 UI에 지원되는 브라우저

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>네트워킹 포트 요구 사항

### <a name="port-requirements-for-data-box-edge"></a>Data Box Edge에 대한 포트 요구 사항

다음 테이블에는 SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃* 또는 *아웃바운드*는 배포 후 데이터를 외부로 보내는 Data Box Edge 디바이스에서 방향을 참조합니다.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge에 대한 포트 요구 사항

Azure IoT Edge는 지원되는 IoT Hub 프로토콜을 사용하여 온-프레미스 Edge 디바이스에서 Azure 클라우드로의 아웃바운드 통신을 허용합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다(예: 클라우드에서 디바이스로 메시지 전송).

Azure IoT Edge 런타임을 호스트하는 서버의 포트 구성에 대한 다음 표를 사용하세요.

| 포트 번호 | 인 또는 아웃 | 포트 범위 | 필수 | 지침 |
|----------|-----------|------------|----------|----------|
| TCP 443(HTTPS)| 아웃       | WAN        | 예      | IoT Edge 프로비전을 위해 아웃바운드로 엽니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다.|

전체 정보를 보려면 [IoT Edge 배포에 대한 방화벽 및 포트 구성 규칙](https://docs.microsoft.com/azure/iot-edge/troubleshoot)으로 이동하세요.

## <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. Data Box Edge 디바이스 및 Data Box Edge 서비스는 Azure Service Bus, Azure Active Directory Access Control, 스토리지 계정 및 Microsoft 업데이트 서버 등의 다른 Microsoft 애플리케이션에 의존합니다. 이러한 애플리케이션과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 애플리케이션과 연결된 URL 패턴은 달라질 수 있습니다. 이렇게 변경하려면 네트워크 관리자가 Data Box Edge에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

Data Box Edge 고정 IP 주소에 따라 대부분의 경우 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> - 디바이스(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> - 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로 설정해야 합니다.

### <a name="url-patterns-for-gateway-feature"></a>게이트웨이 기능용 URL 패턴

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>컴퓨팅 기능용 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 컨테이너 레지스트리(필수)               |
| https://\*.azurecr.io                     | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 
| https://\*.azure-devices.net              | IoT Hub 액세스(필수)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government 게이트웨이의 URL 패턴

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 계산에 대한 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft 컨테이너 레지스트리(필수)               |
| https://\*.azure-devices.us              | IoT Hub 액세스(필수)           |
| https://\*.azurecr.us                    | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 

## <a name="internet-bandwidth"></a>인터넷 대역폭

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>계산 크기 조정 고려 사항

솔루션을 개발 하 고 테스트 하는 동안 사용자 환경을 사용 하 여 Data Box Edge 장치에 충분 한 용량이 있는지 확인 하 고 장치에서 최적의 성능을 얻을 수 있습니다.

고려해 야 할 요인은 다음과 같습니다.

- **컨테이너 세부** 정보-다음 사항을 고려 합니다.

    - 워크 로드에 얼마나 많은 컨테이너가 있나요? 많은 경량 컨테이너와 리소스를 많이 사용 하는 컨테이너를 포함할 수 있습니다.
    - 이러한 컨테이너에 할당 된 리소스와 사용 중인 리소스는 무엇 인가요?
    - 컨테이너를 공유 하는 계층은 몇 개입니까?
    - 사용 하지 않는 컨테이너가 있나요? 중지 된 컨테이너는 여전히 디스크 공간을 차지 합니다.
    - 컨테이너가 작성 된 언어는 무엇 인가요?
- **처리 된 데이터 크기** -컨테이너에서 처리할 데이터의 양은 얼마 인가요? 이 데이터는 디스크 공간을 사용 하 고 데이터는 메모리에서 처리 되나요?
- **예상 성능** -솔루션의 원하는 성능 특성은 무엇 인가요? 

솔루션의 성능을 이해 하 고 구체화 하려면 다음을 사용할 수 있습니다.

- Azure Portal에서 사용할 수 있는 계산 메트릭입니다. Data Box Edge 리소스로 이동한 후 **모니터링 > 메트릭**으로 이동 합니다. 에 **지 계산 메모리 사용량과** 에 **지 계산-백분율 CPU** 를 확인 하 여 사용 가능한 리소스와 리소스가 사용 되는 방식을 파악 합니다.
- 다음과 같이 장치의 PowerShell 인터페이스를 통해 사용할 수 있는 모니터링 명령입니다.

    - `dkrdbe stats` 하 여 컨테이너 리소스 사용 통계의 라이브 스트림을 가져옵니다. 명령은 CPU, 메모리 사용량, 메모리 제한 및 네트워크 IO 메트릭을 지원 합니다.
    - 사용 된 디스크 공간 크기에 대한 정보를 가져올 `dkrdbe system df`. 
    - 사용 하지 않는 이미지를 정리 하 고 공간을 확보 `dkrdbe image prune` 합니다.
    - `dkrdbe ps --size` 하 여 실행 중인 컨테이너의 대략적인 크기를 확인 합니다. 

    사용 가능한 명령에 대한 자세한 내용은 [계산 모듈 모니터링 및 문제 해결](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)을 참조 하세요.

마지막으로, 데이터 집합에 대한 솔루션의 유효성을 검사 하 고 프로덕션 환경에 배포 하기 전에 Data Box Edge에 대한 성능을 수량화 합니다.


## <a name="next-step"></a>다음 단계

- [Azure Data Box Edge 배포](data-box-edge-deploy-prep.md)
