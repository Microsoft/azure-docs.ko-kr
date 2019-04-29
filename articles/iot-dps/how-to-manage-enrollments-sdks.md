---
title: Azure 디바이스 프로비전 서비스 SDK를 사용하여 디바이스 등록 관리 | Microsoft Docs
description: IoT Hub Device Provisioning 서비스에서 서비스 SDK를 사용하여 디바이스 등록을 관리하는 방법
author: yzhong94
ms.author: yizhon
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: c73a40e46d86632732454ae16ea4f83e3ffa0281
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627272"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Azure Device Provisioning Service SDK로 디바이스 등록을 관리하는 방법
*디바이스 등록*은 특정 시점에 Device Provisioning Service에 등록할 수 있는 단일 디바이스 또는 디바이스 그룹의 레코드를 만듭니다. 등록 레코드에는 원하는 IoT Hub를 포함하여 해당 등록의 일부로 해당 디바이스에 대한 초기 원하는 구성을 포함합니다. 이 문서에서는 Azure IoT 프로비전 서비스 SDK를 사용하여 프로그래밍 방식으로 프로비전 서비스에 대한 디바이스 등록을 관리하는 방법을 보여줍니다.  SDK는 Azure IoT SDK와 같은 리포지토리의 GitHub에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 디바이스 프로비전 서비스 인스턴스의 연결 문자열 가져오기
* 사용되는 [증명 메커니즘](concepts-security.md#attestation-mechanism)에 대한 디바이스 보안 아티팩트를 가져옵니다.
    * [**TPM(신뢰할 수 있는 플랫폼 모듈)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * 개별 등록: 물리적 디바이스 또는 TPM 시뮬레이터의 등록 ID 및 TPM 인증 키.
        * 등록 그룹은 TPM 증명에 적용되지 않습니다.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * 개별 등록: 물리적 디바이스 또는 SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 에뮬레이터의 [리프 인증서](/azure/iot-dps/concepts-security).
        * 등록 그룹: 물리적 디바이스에서 디바이스 인증서를 생성하는 데 사용하는 [CA/루트 인증서](/azure/iot-dps/concepts-security#root-certificate) 또는 [중간 인증서](/azure/iot-dps/concepts-security#intermediate-certificate).  SDK DICE 에뮬레이터에서 생성할 수도 있습니다.
* 정확한 API 호출은 언어 차이로 인해 달라질 수 있습니다. 자세한 내용은 GitHub에 제공하는 샘플을 검토하세요.
   * [Java 프로비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js 프로비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET 프로비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>디바이스 등록 만들기
프로비전 서비스를 사용하여 두 가지 방법으로 디바이스를 등록할 수 있습니다.

* **등록 그룹**은 [루트 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) 또는 [중간 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)에서 서명된 X.509 인증서의 일반적인 증명 메커니즘을 공유하는 디바이스 그룹에 대한 항목입니다. 원하는 초기 구성을 공유하는 다수의 디바이스 또는 동일한 테넌트로 이동하는 디바이스에 대한 등록 그룹을 사용하는 것이 좋습니다. X.509 증명 메커니즘을 *등록 그룹*으로 사용하는 디바이스만을 등록할 수 있습니다. 

    이 워크플로를 따르는 SDK로 등록 그룹을 만들 수 있습니다.

    1. 등록 그룹의 경우 증명 메커니즘은 X.509 루트 인증서를 사용합니다.  루트 인증서와 함께 서비스 SDK API ```X509Attestation.createFromRootCertificate```를 호출하여 등록에 대한 증명을 만듭니다.  X.509 루트 인증서는 PEM 파일 또는 문자열로 제공됩니다.
    1. 만든 ```attestation```을 사용하는 새 ```EnrollmentGroup``` 변수 및 고유한 ```enrollmentGroupId```를 만듭니다.  필요에 따라 ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```와 같은 매개 변수를 설정할 수 있습니다.
    2. ```EnrollmentGroup```을 통해 백엔드 애플리케이션에서 서비스 SDK API ```createOrUpdateEnrollmentGroup```를 호출하여 등록 그룹을 만듭니다.

* **개별 등록**은 등록할 수 있는 단일 디바이스에 대한 항목입니다. 개별 등록은 증명 메커니즘으로 X.509 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 고유한 초기 구성이 필요한 디바이스 또는 증명 메커니즘으로 TPM 또는 가상 TPM을 통해 SAS 토큰만을 사용할 수 있는 디바이스의 경우 개별 등록을 사용하는 것이 좋습니다. 개별 등록은 지정된 원하는 IoT Hub 디바이스 ID가 있을 수 있습니다.

    이 워크플로를 따르는 SDK로 개별 등록을 만들 수 있습니다.
    
    1. TPM 또는 X.509일 수 있는 ```attestation``` 메커니즘을 선택합니다.
        1. **TPM**: 물리적 디바이스 또는 TPM 시뮬레이터의 인증 키를 입력으로 사용하면 서비스 SDK API ```TpmAttestation```을 호출하여 등록에 대한 증명을 만들 수 있습니다. 
        2. **X.509**: 클라이언트 인증서를 입력으로 사용하면 서비스 SDK API ```X509Attestation.createFromClientCertificate```를 호출하여 등록에 대한 증명을 만들 수 있습니다.
    2. 만든 ```attestation```을 사용하는 새 ```IndividualEnrollment``` 변수 및 고유한 ```registrationId```를 사용자 디바이스에 있거나 TPM 시뮬레이터에서 생성된 입력으로 만듭니다.  필요에 따라 ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```와 같은 매개 변수를 설정할 수 있습니다.
    3. ```IndividualEnrollment```를 통해 백엔드 애플리케이션에서 서비스 SDK API ```createOrUpdateIndividualEnrollment```를 호출하여 개별 등록을 만듭니다.

등록을 성공적으로 만든 후 디바이스 프로비전 서비스가 등록 결과 반환합니다. 이 워크플로는 [앞에서 언급된](#prerequisites) 샘플에 설명되어 있습니다.

## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트

등록 항목을 만든 후 등록을 업데이트할 수도 있습니다.  잠재적 시나리오에는 원하는 속성 업데이트, 증명 메서드 업데이트 또는 디바이스 액세스 취소가 포함됩니다.  개별 등록 및 그룹 등록을 위한 여러 API가 있으나 증명 메커니즘에는 차이가 없습니다.

이 워크플로를 따르는 등록 항목을 업데이트할 수 있습니다.
* **개별 등록**:
    1. 서비스 SDK API ```getIndividualEnrollment```을 통해 우선 프로비전 서비스에서 최신 등록을 가져옵니다.
    2. 필요에 따라 최신 등록의 매개 변수를 수정합니다. 
    3. 최신 등록을 사용하면서 서비스 SDK API ```createOrUpdateIndividualEnrollment```를 호출하여 등록 항목을 업데이트합니다.
* **그룹 등록**:
    1. 서비스 SDK API ```getEnrollmentGroup```을 통해 우선 프로비전 서비스에서 최신 등록을 가져옵니다.
    2. 필요에 따라 최신 등록의 매개 변수를 수정합니다.
    3. 최신 등록을 사용하면서 서비스 SDK API ```createOrUpdateEnrollmentGroup```을 호출하여 등록 항목을 업데이트합니다.

이 워크플로는 [앞에서 언급된](#prerequisites) 샘플에 설명되어 있습니다.

## <a name="remove-an-enrollment-entry"></a>등록 항목 제거

* **개별 등록**은 ```registrationId```를 사용하여 서비스 SDK API ```deleteIndividualEnrollment```를 호출하면 삭제할 수 있습니다.
* **그룹 등록**은 ```enrollmentGroupId```를 사용하여 서비스 SDK API ```deleteEnrollmentGroup```을 호출하면 삭제할 수 있습니다.

이 워크플로는 [앞에서 언급된](#prerequisites) 샘플에 설명되어 있습니다.

## <a name="bulk-operation-on-individual-enrollments"></a>개별 등록에서의 대량 작업

대량 작업을 수행하면 이 워크플로를 따르는 여러 개별 등록을 생성, 업데이트 또는 제거할 수 있습니다.

1. 여러 ```IndividualEnrollment```를 포함하는 변수를 만듭니다.  이 변수의 구현은 모든 언어마다 다릅니다.  자세한 내용은 GitHub에서의 대량 작업 샘플을 검토하세요.
2. 원하는 작업에 대한 ```BulkOperationMode```를 통한 서비스 SDK API ```runBulkOperation``` 및 개별 등록에 대한 변수를 호출합니다. 만들기, 업데이트, updateIfMatchEtag 및 삭제의 네 가지 모드가 지원됩니다.

작업을 성공적으로 수행하면 Device Provisioning Service는 대량 작업 결과를 반환합니다.

이 워크플로는 [앞에서 언급된](#prerequisites) 샘플에 설명되어 있습니다.
