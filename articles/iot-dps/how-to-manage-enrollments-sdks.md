---
title: Azure DPS Sdk를 사용 하 여 장치 등록 관리
description: 서비스 Sdk를 사용 하 여 IoT Hub Device Provisioning Service (DPS)에서 장치 등록을 관리 하는 방법
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975077"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Azure 장치 프로 비전 서비스 Sdk를 사용 하 여 장치 등록을 관리 하는 방법
*장치 등록* 은 특정 시점에 장치 프로 비전 서비스에 등록할 수 있는 단일 장치 또는 장치 그룹의 레코드를 만듭니다. 등록 레코드에는 원하는 IoT hub를 포함 하 여 해당 등록의 일부로 장치에 대 한 초기 desired 구성이 포함 됩니다. 이 문서에서는 Azure IoT 프로 비전 서비스 Sdk를 사용 하 여 프로그래밍 방식으로 프로 비전 서비스에 대 한 장치 등록을 관리 하는 방법을 보여 줍니다.  Sdk는 Azure IoT Sdk와 동일한 리포지토리의 GitHub에서 사용할 수 있습니다.

## <a name="prerequisites"></a>Előfeltételek
* 장치 프로 비전 서비스 인스턴스에서 연결 문자열을 가져옵니다.
* 사용 되는 [증명 메커니즘](concepts-security.md#attestation-mechanism) 에 대 한 장치 보안 아티팩트를 가져옵니다.
    * [**TPM (신뢰할 수 있는 플랫폼 모듈)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * 개별 등록: 물리적 장치 또는 TPM 시뮬레이터의 등록 ID 및 TPM 인증 키입니다.
        * 등록 그룹은 TPM 증명에 적용 되지 않습니다.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * 개별 등록: 물리적 장치 또는 SDK [주사위](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 에뮬레이터의 [리프 인증서](/azure/iot-dps/concepts-security)
        * 등록 그룹: 물리적 장치에서 장치 인증서를 생성 하는 데 사용 되는 [CA/루트 인증서](/azure/iot-dps/concepts-security#root-certificate) 또는 [중간 인증서](/azure/iot-dps/concepts-security#intermediate-certificate)입니다.  SDK 주사위 에뮬레이터에서 생성할 수도 있습니다.
* 정확한 API 호출은 언어 차이로 인해 달라질 수 있습니다. 자세한 내용은 GitHub에 제공 된 샘플을 검토 하세요.
   * [Java 프로 비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js 프로 비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET 프로 비전 서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>장치 등록 만들기
프로 비전 서비스를 사용 하 여 장치를 등록 하는 방법에는 두 가지가 있습니다.

* **등록 그룹** 은 [루트 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) 또는 [중간 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)로 서명 된 x.509 인증서의 일반적인 증명 메커니즘을 공유 하는 장치 그룹에 대 한 항목입니다. 원하는 초기 구성을 공유 하는 많은 수의 장치 또는 동일한 테넌트로 이동 하는 장치에 대해 등록 그룹을 사용 하는 것이 좋습니다. X.509 증명 메커니즘을 사용 하는 장치만 *등록 그룹*으로 등록할 수 있습니다. 

    이 워크플로를 따르는 Sdk를 사용 하 여 등록 그룹을 만들 수 있습니다.

    1. 등록 그룹의 경우 증명 메커니즘은 x.509 루트 인증서를 사용 합니다.  루트 인증서를 사용 하 여 서비스 SDK API ```X509Attestation.createFromRootCertificate```를 호출 하 여 등록에 대 한 증명을 만듭니다.  X.509 루트 인증서는 PEM 파일 또는 문자열로 제공 됩니다.
    1. 만든 ```attestation```와 고유 ```enrollmentGroupId```를 사용 하 여 새 ```EnrollmentGroup``` 변수를 만듭니다.  필요에 따라 ```Device ID```, ```IoTHubHostName``````ProvisioningStatus```와 같은 매개 변수를 설정할 수 있습니다.
    2. ```EnrollmentGroup```를 사용 하 여 백 엔드 응용 프로그램에서 서비스 SDK API ```createOrUpdateEnrollmentGroup```를 호출 하 여 등록 그룹을 만듭니다.

* **개별 등록** 은 등록할 수 있는 단일 장치에 대 한 항목입니다. 개별 등록는 x.509 인증서 또는 SAS 토큰 (실제 또는 가상 TPM) 중 하나를 증명 메커니즘으로 사용할 수 있습니다. 고유한 초기 구성이 필요한 장치 또는 증명 메커니즘으로 TPM 또는 가상 TPM을 통해 SAS 토큰만을 사용할 수 있는 장치에 대해 개별 등록를 사용 하는 것이 좋습니다. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    이 워크플로를 따르는 Sdk를 사용 하 여 개별 등록을 만들 수 있습니다.
    
    1. TPM 또는 x.509 수 있는 ```attestation``` 메커니즘을 선택 합니다.
        1. **Tpm**: 물리적 장치 또는 TPM 시뮬레이터에서 인증 키를 입력으로 사용 하 여 서비스 SDK API ```TpmAttestation```를 호출 하 여 등록에 대 한 증명을 만들 수 있습니다. 
        2. **X.509**: 클라이언트 인증서를 입력으로 사용 하 여 서비스 SDK API ```X509Attestation.createFromClientCertificate```를 호출 하 여 등록에 대 한 증명을 만들 수 있습니다.
    2. 만든 ```attestation```를 사용 하 여 새 ```IndividualEnrollment``` 변수를 만들고 장치에 있거나 TPM 시뮬레이터에서 생성 된 고유한 ```registrationId``` 입력으로 사용 합니다.  필요에 따라 ```Device ID```, ```IoTHubHostName``````ProvisioningStatus```와 같은 매개 변수를 설정할 수 있습니다.
    3. ```IndividualEnrollment```를 사용 하 여 백 엔드 응용 프로그램에서 서비스 SDK API ```createOrUpdateIndividualEnrollment```를 호출 하 여 개별 등록을 만듭니다.

등록을 성공적으로 만든 후에는 장치 프로 비전 서비스에서 등록 결과를 반환 합니다. 이 워크플로는 [앞에서 언급](#prerequisites)한 샘플에서 보여 줍니다.

## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트

등록 항목을 만든 후 등록을 업데이트 하는 것이 좋습니다.  잠재적 시나리오에는 desired 속성을 업데이트 하거나, 증명 메서드를 업데이트 하거나, 장치 액세스를 취소 하는 작업이 포함 됩니다.  개별 등록 및 그룹 등록에 대 한 Api는 다르지만 증명 메커니즘에는 차이가 없습니다.

이 워크플로를 따라 등록 항목을 업데이트할 수 있습니다.
* **Egyéni regisztráció**:
    1. 서비스 SDK API ```getIndividualEnrollment```를 사용 하 여 먼저 프로 비전 서비스에서 최신 등록을 가져옵니다.
    2. 필요에 따라 최신 등록의 매개 변수를 수정 합니다. 
    3. 최신 등록을 사용 하 여 서비스 SDK API ```createOrUpdateIndividualEnrollment```를 호출 하 여 등록 항목을 업데이트 합니다.
* **그룹 등록**:
    1. 서비스 SDK API ```getEnrollmentGroup```를 사용 하 여 먼저 프로 비전 서비스에서 최신 등록을 가져옵니다.
    2. 필요에 따라 최신 등록의 매개 변수를 수정 합니다.
    3. 최신 등록을 사용 하 여 서비스 SDK API ```createOrUpdateEnrollmentGroup```를 호출 하 여 등록 항목을 업데이트 합니다.

이 워크플로는 [앞에서 언급](#prerequisites)한 샘플에서 보여 줍니다.

## <a name="remove-an-enrollment-entry"></a>등록 항목 제거

* ```registrationId```를 사용 하 여 ```deleteIndividualEnrollment``` 서비스 SDK API를 호출 하 여 **개별 등록** 을 삭제할 수 있습니다.
* ```enrollmentGroupId```를 사용 하 여 ```deleteEnrollmentGroup``` 서비스 SDK API를 호출 하면 **그룹 등록** 을 삭제할 수 있습니다.

이 워크플로는 [앞에서 언급](#prerequisites)한 샘플에서 보여 줍니다.

## <a name="bulk-operation-on-individual-enrollments"></a>개별 등록 대량 작업

다음 워크플로를 수행 하 여 여러 개별 등록를 만들거나 업데이트 하거나 제거 하는 대량 작업을 수행할 수 있습니다.

1. 여러 ```IndividualEnrollment```를 포함 하는 변수를 만듭니다.  이 변수의 구현은 언어 마다 다릅니다.  자세한 내용은 GitHub에서 대량 작업 샘플을 검토 하세요.
2. 원하는 작업에 대 한 ```BulkOperationMode```와 개별 등록에 대 한 변수를 사용 하 여 ```runBulkOperation``` 서비스 SDK API를 호출 합니다. 만들기, 업데이트, updateIfMatchEtag 및 삭제의 네 가지 모드가 지원 됩니다.

작업을 성공적으로 수행한 후에는 장치 프로 비전 서비스에서 대량 작업 결과를 반환 합니다.

이 워크플로는 [앞에서 언급](#prerequisites)한 샘플에서 보여 줍니다.
