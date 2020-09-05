---
title: IoT 플러그 앤 플레이 장치를 인증 하는 방법 | Microsoft Docs
description: 장치 빌더는 인증을 위해 테스트를 실행 하 고 장치를 제출 하는 방법을 알아봅니다.
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bdb6bf166e84bb9134bbd14454899bcefbf0a887
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949901"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>IoT 플러그 앤 플레이 장치를 인증 하는 방법

IoT 플러그 앤 플레이 장치 인증 프로그램에는 장치가 IoT 플러그 앤 플레이 인증 요구 사항을 충족 하는지 확인 하는 도구가 포함 되어 있습니다. 또한이 도구를 통해 조직에서는 IoT 플러그 앤 플레이 장치의 가용성에 대 한 인식을 지원할 수 있습니다. 이러한 인증 된 장치는 IoT 솔루션에 맞게 조정 되 고 출시 시간을 단축 하는 데 도움이 됩니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure CLI에 대 한 Azure IoT 명령줄 도구 확장을 설치 합니다.
- IoT 플러그 앤 플레이 테스트를 실행 하 여 개발 단계에서 장치 응용 프로그램의 유효성을 검사 합니다.  
- Azure 인증 장치 포털을 사용 하 여 장치 응용 프로그램의 유효성 검사

## <a name="prepare-your-device"></a>장치 준비

IoT 플러그 앤 플레이에서 실행 되는 응용 프로그램 코드는 다음을 수행 해야 합니다.

- [DPS (장치 프로 비전 서비스)](../iot-dps/about-iot-dps.md)를 사용 하 여 Azure IoT Hub에 연결 합니다.
- [IoT 플러그 인 재생 규칙](concepts-convention.md) 에 따라 원격 분석, 속성 및 명령을 구현 합니다.

응용 프로그램은 운영 체제와 별도로 설치 되거나 장치에 플래시 되는 펌웨어 이미지에 운영 체제와 함께 제공 되는 소프트웨어입니다.

이 인증 프로세스는 장치 구현이 [DTDL (디지털 Twins 정의 언어](https://github.com/Azure/opendigitaltwins-dtdl) ) 장치 모델에 정의 된 원격 분석, 속성 및 명령과 일치 하는지 확인 하 고 [Azure Iot 공용 모델 리포지토리에서](concepts-model-repository.md)해당 모델을 사용할 수 있는지 확인 하 여 장치가 IoT 플러그 앤 플레이 호환 되는지 확인 합니다.

인증 요구 사항을 충족 하려면 장치가 다음 조건을 충족 해야 합니다.

- [DPS](../iot-dps/about-iot-dps.md)를 사용 하 여 Azure IoT Hub에 연결 합니다.
- IoT 플러그 앤 플레이 규칙에 따라 원격 분석, 속성 또는 명령을 구현 합니다.
- [Dtdl v2](https://aka.ms/dtdl) 모델과의 장치 상호 작용에 대해 설명 합니다.
- [Azure IoT 공용 모델 리포지토리에](https://devicemodels.azureiotsolutions.com/) 모델 및 모든 필수 인터페이스를 게시 합니다.
- Dps 프로 비전 페이로드의 [dps 등록](concepts-developer-guide.md#dps-payload) 중에 모델 ID를 보냅니다.
- [Mqtt 연결](concepts-developer-guide.md#model-id-announcement)중에 모델 ID를 알립니다.

## <a name="test-with-the-azure-iot-extension-cli"></a>Azure IoT 확장 CLI를 사용 하 여 테스트

Azure [IOT CLI 확장](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest) 을 사용 하면 Azure 인증 장치 포털을 통해 인증을 위해 장치를 제출 하기 전에 장치 구현이 모델과 일치 하는지 확인할 수 있습니다.

다음 단계는 CLI를 사용 하 여 인증 테스트를 준비 하 고 실행 하는 방법을 보여 줍니다.

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI에 대 한 Azure IoT 확장을 설치 합니다.

사용자 환경에서 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 설정 하려면 설치 지침을 참조 하세요.

Azure IoT 확장을 설치 하려면 다음 명령을 실행 합니다.

```azurecli
az extension add --name azure-iot
```

자세한 내용은 [Azure IoT에 대 한 Azure CLI](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest)를 참조 하세요.

### <a name="create-a-new-product-test"></a>새 제품 테스트 만들기

다음 명령을 사용 하 여 DPS 대칭 키 증명 방법으로 테스트를 만듭니다.

- 테스트할 새 제품을 만들고 테스트 구성을 생성 합니다. 출력에는 장치에서 프로 비전에 사용 해야 하는 DPS 정보 (기본 키, 장치 ID 및 ID 범위)가 표시 됩니다.
- 모델을 설명 하는 DTDL 파일이 포함 된 폴더를 지정 합니다.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> CLI를 사용 하는 경우 구독에 [로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 해야 합니다.

명령의 JSON 출력에는 `primaryKey` `registrationId` 장치를 연결할 때 사용할, 및가 포함 `scopeID` 됩니다.

예상 출력:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>디바이스 연결

이전 명령에서 DPS 정보 출력을 사용 하 여 테스트 IoT Hub 인스턴스에 장치를 연결 합니다.

### <a name="manage-and-configure-the-product-tests"></a>제품 테스트 관리 및 구성

장치가 연결 되어 IoT hub와 상호 작용할 준비가 되 면 제품 테스트 구성 파일을 생성 합니다. 파일을 만들려면 다음을 수행 합니다.

- `id`이전 명령의 출력에서 테스트를 사용 합니다.
- `--wait`매개 변수를 사용 하 여 테스트 사례를 가져옵니다.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

예상 출력:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

명령을 사용 하 여 `az iot product test case update` 테스트 구성 파일을 수정할 수 있습니다.

### <a name="run-the-tests"></a>테스트 실행

테스트 구성을 생성 한 후 다음 단계는 테스트를 실행 하는 것입니다. `devicetestId`이전 명령과 동일한를 매개 변수로 사용 하 여 테스트를 실행 합니다. 테스트 결과를 확인 하 여 모든 테스트에 상태가 있는지 확인 `Passed` 합니다.

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

테스트 실행 출력 예제

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Azure 인증 장치 포털을 사용 하 여 테스트

다음 단계에서는 [Azure 인증 장치 포털](https://aka.ms/acdp) 을 사용 하 여 등록 하 고, 제품 세부 정보를 등록 하 고, 시작 가이드를 제출 하 고, 인증 테스트를 실행 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서를 작성할 당시에는 포털에서 [Azure IoT 장치 카탈로그의 인증 된에 대 한](https://aka.ms/devicecatalog)게시를 지원 하지 않습니다.

### <a name="onboarding"></a>온보딩

[인증 포털](https://aka.ms/acdp)을 사용 하려면 회사 또는 학교 테 넌 트에서 Azure Active Directory를 사용 해야 합니다.

Azure IoT 공용 모델 리포지토리에 모델을 게시 하려면 계정이 [Microsoft 파트너 네트워크](https://partner.microsoft.com)의 멤버 여야 합니다. 시스템은 Microsoft 파트너 네트워크 ID가 존재 하는지 확인 하 고, 계정이 완전히 점검 되었다는 장치 카탈로그에 게시 합니다.

### <a name="company-profile"></a>회사 프로필

왼쪽 탐색 메뉴에서 회사 프로필을 관리할 수 있습니다. 회사 프로필에는 회사 URL, 전자 메일 주소 및 회사 로고가 포함 됩니다. 인증 작업을 실행 하기 전에이 페이지에서 프로그램 계약을 수락 해야 합니다.

회사 프로필 정보는 장치 카탈로그의 장치 설명 전시에 사용 됩니다.

### <a name="create-new-project"></a>새 프로젝트 만들기

장치를 인증 하려면 먼저 새 프로젝트를 만들어야 합니다.

[인증 포털로](https://aka.ms/acdp)이동 합니다. **프로젝트** 페이지에서 *+ 새 프로젝트 만들기*를 선택 합니다. 그런 다음 프로젝트 이름, 장치 이름을 입력 하 고 장치 클래스를 선택 합니다.

인증 프로세스 중에 제공 하는 제품 정보는 다음 네 가지 범주로 구분 됩니다.

- 장치 정보. 이름, 설명, 인증 및 운영 체제와 같은 장치에 대 한 정보를 수집 합니다.
- **시작** 가이드를 참조 하세요. 장치를 게시 하기 전에 시스템 관리자가 승인할 PDF 문서로 가이드를 제출 해야 합니다.
- 마케팅 정보 장치에 대해 고객이 사용할 준비가 된 마케팅 정보를 제공 합니다. 마케팅 정보에는 설명, 사진 및 배포자가 포함 됩니다.
- 추가 업계 인증. 이 선택적 섹션에서는 장치에서 가져온 다른 인증에 대 한 추가 정보를 제공할 수 있습니다.

### <a name="connect-and-test"></a>연결 및 테스트

연결 및 테스트 단계는 장치가 IoT 플러그 앤 플레이 인증 요구 사항을 충족 하는지 확인 합니다.

다음 세 가지 단계를 완료 해야 합니다.

1. 인터페이스를 연결 하 고 검색 합니다. 장치는 DPS를 통해 Azure IoT 인증 서비스에 연결 해야 합니다. 인증 방법 (x.509 인증서, 대칭 키 또는 신뢰할 수 있는 플랫폼 모듈)을 선택 하 여 DPS 정보를 사용 하 여 장치 응용 프로그램을 사용 하 고 업데이트 합니다.
1. 인터페이스를 검토 합니다. 인터페이스를 검토 하 고 각 인터페이스에 테스트에 적합 한 페이로드 입력이 있는지 확인 합니다.
1. 테스트. 시스템은 각 장치 모델을 테스트 하 여 모델에 설명 된 원격 분석, 속성 및 명령이 IoT 플러그 앤 플레이 규칙을 따르는지 확인 합니다. 테스트가 완료 되 면 **로그 보기** 링크를 선택 하 여 장치에서 원격 분석을 확인 하 고 IoT Hub 장치 쌍 속성으로 전송 된 원시 데이터를 확인 합니다.

## <a name="next-steps"></a>다음 단계

이제 장치 제출이 완료 되 면에서 장치 인증 팀에 문의 [iotcert@microsoft.com](mailto:iotcert@microsoft.com) 하 여 다음 단계를 계속 진행할 수 있습니다. 여기에는 Microsoft 파트너 네트워크 멤버 자격 유효성 검사 및 시작 가이드의 검토가 포함 됩니다. 모든 요구 사항이 충족 되 면 장치가 [Azure IoT 장치 카탈로그에 대해 인증](https://aka.ms/devicecatalog)됨에 포함 되도록 선택할 수 있습니다.
