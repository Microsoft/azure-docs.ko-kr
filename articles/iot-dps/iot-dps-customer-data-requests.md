---
title: 고객 데이터 요청 기능
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626437"
---
# <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약

Azure IoT Hub Device Provisioning Service는 디바이스에서 시작하여 클라우드에서 끝나는 보안을 통해 Azure IoT Hub로 디바이스를 원활하게 자동 프로비전하도록 지원하는 엔터프라이즈 고객을 대상으로 하는 REST API 기반 클라우드 서비스입니다.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

개별 디바이스에는 테넌트 관리자에 의해 등록 ID 및 디바이스 ID가 할당됩니다. 이러한 디바이스에서 제공되는 데이터 및 이러한 디바이스에 대한 데이터는 이러한 ID를 기반으로 합니다. Microsoft는 어떤 정보도 유지 관리하지 않으며, 이러한 디바이스와 개인의 상관 관계를 허용하는 데이터에 액세스할 수 없습니다.

Device Provisioning Service에서 관리되는 디바이스는 대부분 개인 디바이스가 아닙니다(예: 사무실 온도 조절 디바이스 또는 공장 로봇). 그러나 고객은 일부 디바이스를 개인적으로 식별 가능한 것으로 간주할 수 있으며, 재량에 따라 디바이스를 개인과 연결하는 자체 자사 또는 재고 추적 방법을 유지할 수 있습니다. Device Provisioning Service는 디바이스와 연결된 모든 데이터를 개인 데이터인 것처럼 관리 및 저장합니다.

테넌트 관리자는 Azure Portal 또는 서비스의 REST API를 사용하여 디바이스 ID 또는 등록 ID와 연결된 데이터를 내보내거나 삭제하는 방식으로 정보 요청을 이행할 수 있습니다.

> [!NOTE]
> Device Provisioning Service를 통해 Azure IoT Hub에 프로비전된 디바이스에는 Azure IoT Hub 서비스에 저장되는 추가 데이터가 있습니다. 지정된 디바이스에 대한 전체 요청을 완료하려면 [Azure IoT Hub 참조 설명서](../iot-hub/iot-hub-customer-data-requests.md)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

Device Provisioning Service는 등록 및 등록 레코드를 저장합니다. 등록은 프로비전할 수 있는 디바이스에 대한 정보를 포함하고, 등록 레코드는 프로비저닝 프로세스를 이미 통과한 디바이스를 보여줍니다.

테넌트 관리자는 Azure Portal에서 등록을 제거할 수 있습니다. 그러면 연결된 등록 레코드도 모두 제거됩니다.

자세한 내용은 [디바이스 등록을 관리하는 방법](how-to-manage-enrollments.md)을 참조하세요.

REST API를 사용하여 등록 및 등록 레코드에 대한 삭제 작업을 수행할 수도 있습니다.

* 단일 디바이스에 대한 등록 정보를 삭제하려면 [디바이스 등록 - 삭제](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment)를 사용하면 됩니다.
* 디바이스 그룹에 대한 등록 정보를 삭제하려면 [디바이스 등록 그룹 - 삭제](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup)를 사용하면 됩니다.
* 프로비전된 디바이스에 대한 정보를 삭제하려면 [등록 상태 - 등록 상태 삭제](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)를 사용하면 됩니다.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

Device Provisioning Service는 등록 및 등록 레코드를 저장합니다. 등록은 프로비전할 수 있는 디바이스에 대한 정보를 포함하고, 등록 레코드는 프로비저닝 프로세스를 이미 통과한 디바이스를 보여줍니다.

테넌트 관리자는 Azure Portal을 통해 등록 및 등록 레코드를 보고, 복사하여 붙여넣기를 사용하여 내보낼 수 있습니다.

등록을 관리하는 방법에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](how-to-manage-enrollments.md)을 참조하세요.

REST API를 사용하여 등록 및 등록 레코드에 대한 내보내기 작업을 수행할 수도 있습니다.

* 단일 디바이스에 대한 등록 정보를 내보내려면 [디바이스 등록 - 가져오기](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)를 사용하면 됩니다.
* 디바이스 그룹에 대한 등록 정보를 내보내려면 [디바이스 등록 그룹 - 가져오기](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup)를 사용하면 됩니다.
* 이미 프로비전된 디바이스에 대한 정보를 내보내려면 [등록 상태 - 등록 상태 가져오기](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate)를 사용하면 됩니다.

> [!NOTE]
> Microsoft 엔터프라이즈 서비스를 사용하는 경우 Microsoft는 시스템 생성 로그라는 일부 정보를 생성합니다. 일부 Device Provisioning Service 시스템 생성 로그는 테넌트 관리자가 액세스하거나 내보낼 수 없습니다. 이러한 로그는 서비스 내에서 수행된 실제 작업 및 개별 디바이스와 관련된 진단 데이터로 구성됩니다.

## <a name="links-to-additional-documentation"></a>추가 설명서에 대한 링크

Device Provisioning Service API에 대한 전체 설명서는 [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)에 있습니다.

Azure IoT Hub [고객 데이터 요청 기능](../iot-hub/iot-hub-customer-data-requests.md)