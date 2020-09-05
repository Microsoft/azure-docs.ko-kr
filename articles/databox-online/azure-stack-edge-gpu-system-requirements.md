---
title: Microsoft Azure Stack Edge 시스템 요구 사항 | Microsoft Docs
description: Azure Stack Edge에 대 한 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 7754c57563ec5acb9028c2ace217f318fea5e959
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256282"
---
# <a name="system-requirements-for-azure-stack-edge-with-gpu"></a>GPU를 사용 하는 Azure Stack Edge에 대 한 시스템 요구 사항 

이 문서에서는 Microsoft Azure Stack Edge 솔루션 및 Azure Stack에 지에 연결 하는 클라이언트에 대 한 중요 한 시스템 요구 사항을 설명 합니다. Azure Stack Edge를 배포 하기 전에 정보를 신중 하 게 검토 하는 것이 좋습니다. 배포 및 후속 작업 중에 필요하면 이 정보를 다시 참조할 수 있습니다.

Azure Stack Edge에 대 한 시스템 요구 사항은 다음과 같습니다.

- **호스트의 소프트웨어 요구 사항**: 지원되는 플랫폼, 로컬 구성 UI용 브라우저, SMB 클라이언트, 디바이스에 액세스하는 클라이언트에 대한 추가 요구 사항을 설명합니다.
- **디바이스의 네트워킹 요구 사항**: 물리적 디바이스가 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.

## <a name="supported-os-for-clients-connected-to-device"></a>디바이스에 연결된 클라이언트에 지원되는 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>디바이스에 액세스하는 클라이언트에 지원되는 프로토콜

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>지원 되는 계층화 된 저장소 계정

Azure Stack에서 관리 되는 경우 다음과 같은 계층화 된 저장소 계정이 SMB/NFS/REST 인터페이스에서 지원 됩니다.

|유형  |스토리지 계정  |설명  |
|---------|---------|---------|
|표준     |GPv1: 블록 Blob         |         |
|    |  Blob storage: 블록 Blob       | NAS 에서만 지원 됨     |

* 페이지 blob 및 Azure Files 현재 Azure Stack에서 지원 되지 않습니다.
* * 핫 및 콜드 계층이 Azure Stack에 없습니다. 데이터를 업로드 한 후 Azure PowerShell를 사용 하 여 데이터를 보관 계층으로 이동 합니다. 단계별 지침을 보려면 Azure PowerShell를 사용 하 여 [blob 계층 설정]() 으로 이동 합니다.

## <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>로컬 웹 UI에 지원되는 브라우저

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>네트워킹 포트 요구 사항

### <a name="port-requirements-for-azure-stack-edge"></a>Azure Stack Edge에 대 한 포트 요구 사항

다음 테이블에는 SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *Out* 또는 *아웃 바운드* 는 인터넷으로의 아웃 바운드와 같이 배포 이외에 Azure Stack Edge 장치가 외부에서 데이터를 전송 하는 방향을 가리킵니다.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge에 대한 포트 요구 사항

Azure IoT Edge는 지원되는 IoT Hub 프로토콜을 사용하여 온-프레미스 Edge 디바이스에서 Azure 클라우드로의 아웃바운드 통신을 허용합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다(예: 클라우드에서 디바이스로 메시지 전송).

Azure IoT Edge 런타임을 호스트하는 서버의 포트 구성에 대한 다음 표를 사용하세요.

| 포트 번호 | 인 또는 아웃 | 포트 범위 | 필수 | 지침 |
|----------|-----------|------------|----------|----------|
| TCP 443(HTTPS)| 아웃       | WAN        | 예      | IoT Edge 프로비전을 위해 아웃바운드로 엽니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다.|

전체 정보를 보려면 [IoT Edge 배포에 대한 방화벽 및 포트 구성 규칙](https://docs.microsoft.com/azure/iot-edge/troubleshoot)으로 이동하세요.

## <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. Azure Stack Edge 장치 및 서비스는 Azure Service Bus, Azure Active Directory Access Control, storage 계정, Microsoft 업데이트 서버 등의 다른 Microsoft 응용 프로그램에 종속 됩니다. 이러한 애플리케이션과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 애플리케이션과 연결된 URL 패턴은 달라질 수 있습니다. 이러한 변경을 위해서는 네트워크 관리자가 Azure Stack Edge에 대 한 방화벽 규칙을 필요에 따라 모니터링 하 고 업데이트 해야 합니다.

대부분의 경우 자유롭게 고정 IP 주소 Azure Stack에 따라 아웃 바운드 트래픽에 대 한 방화벽 규칙을 설정 하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> - 디바이스(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> - 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로 설정해야 합니다.

### <a name="url-patterns-for-gateway-feature"></a>게이트웨이 기능용 URL 패턴

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>컴퓨팅 기능용 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 컨테이너 레지스트리(필수)               |
| https://\*.azurecr.io                     | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 
| https://\*.azure-devices.net              | IoT Hub 액세스(필수)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government 게이트웨이의 URL 패턴

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 계산에 대 한 URL 패턴

| URL 패턴                      | 구성 요소 또는 기능                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /mcr.microsoft.com<br></br>https:// \* . cdn.mscr.com | Microsoft 컨테이너 레지스트리(필수)               |
| https:// \* . azure-devices.us              | IoT Hub 액세스(필수)           |
| https:// \* . azurecr.us                    | 개인 및 타사 컨테이너 레지스트리(선택 사항) | 

## <a name="internet-bandwidth"></a>인터넷 대역폭

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>계산 크기 조정 고려 사항

솔루션을 개발 하 고 테스트 하는 동안 사용자 환경을 사용 하 여 Azure Stack Edge 장치에 충분 한 용량이 있는지 확인 하 고 장치에서 최적의 성능을 얻을 수 있습니다.

고려해 야 할 요인은 다음과 같습니다.

- **컨테이너 세부** 정보-다음 사항을 고려 합니다.

    - 컨테이너의 발자국은 무엇 인가요? 컨테이너에서 소비 하는 메모리, 저장소 및 CPU의 양은 얼마나 되나요?
    - 워크 로드에 얼마나 많은 컨테이너가 있나요? 많은 경량 컨테이너와 리소스를 많이 사용 하는 컨테이너를 포함할 수 있습니다.
    - 이러한 컨테이너에 할당 된 리소스는 사용 하는 리소스 (공간)는 무엇 인가요?
    - 컨테이너를 공유 하는 계층은 몇 개입니까? 컨테이너 이미지는 계층의 스택으로 구성 된 파일의 번들입니다. 컨테이너 이미지의 경우 리소스 소비를 계산 하기 위한 계층 수와 개별 크기를 결정 합니다.
    - 사용 하지 않는 컨테이너가 있나요? 중지 된 컨테이너는 여전히 디스크 공간을 차지 합니다.
    - 컨테이너가 작성 된 언어는 무엇 인가요?
- **처리 된 데이터 크기** -컨테이너에서 처리할 데이터의 양은 얼마 인가요? 이 데이터는 디스크 공간을 사용 하 고 데이터는 메모리에서 처리 되나요?
- **예상 성능** -솔루션의 원하는 성능 특성은 무엇 인가요? 

솔루션의 성능을 이해 하 고 구체화 하려면 다음을 사용할 수 있습니다.

- Azure Portal에서 사용할 수 있는 계산 메트릭입니다. Azure Stack Edge 리소스로 이동한 후 **모니터링 > 메트릭**으로 이동 합니다. 에 **지 계산 메모리 사용량과** 에 **지 계산-백분율 CPU** 를 확인 하 여 사용 가능한 리소스와 리소스가 사용 되는 방식을 파악 합니다.
- 계산 모듈을 모니터링 하 고 문제를 해결 하려면 [디버그 Kubernetes 문제](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)를 참조 하세요.

마지막으로, 프로덕션 환경에 배포 하기 전에 데이터 집합에 대 한 솔루션의 유효성을 검사 하 고 Azure Stack에 지에 대 한 성능을 수량화 합니다.

## <a name="next-step"></a>다음 단계

- [Azure Stack Edge 배포](azure-stack-edge-gpu-deploy-prep.md)
