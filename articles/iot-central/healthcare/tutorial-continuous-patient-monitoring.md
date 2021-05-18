---
title: 자습서 - Azure IoT Central을 사용하여 지속적인 환자 모니터링 앱 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure IoT Central 애플리케이션 템플릿을 사용하여 지속적인 환자 모니터링 애플리케이션을 빌드하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 07cd77eb5546143936af1fc963f0212112fc6eb7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108743366"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>자습서: 지속적인 환자 모니터링 앱 템플릿 배포 및 연습

이 자습서에서는 IoT Central 지속적인 환자 모니터링 애플리케이션 템플릿을 배포하여 시작하는 방법을 보여 줍니다. 템플릿을 배포하고 사용하는 방법에 대해 알아봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 템플릿 만들기
> * 애플리케이션 템플릿 살펴보기

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 권장됩니다. 대신 무료 7일 평가판을 사용할 수 있습니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.

## <a name="create-an-application-template"></a>애플리케이션 템플릿 만들기

[Azure IoT Central 애플리케이션 관리자 웹 사이트](https://apps.azureiotcentral.com/)로 이동합니다. 왼쪽 탐색 모음에서 **빌드** 를 선택한 다음, **의료** 탭을 선택합니다.

:::image type="content" source="media/app-manager-health.png" alt-text="의료 앱 템플릿":::

**앱 만들기** 단추를 선택하여 애플리케이션 만들기를 시작한 다음, Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다. **새 애플리케이션** 페이지로 이동합니다.

![의료 애플리케이션 만들기](media/app-manager-health-create.png)

![의료 애플리케이션 만들기 청구 정보](media/app-manager-health-create-billinginfo.png)

애플리케이션을 만드는 방법은 다음과 같습니다.

1. Azure IoT Central은 사용자가 선택한 템플릿을 기반으로 애플리케이션 이름을 자동으로 제안합니다. 이 이름을 그대로 적용해도 되고, **지속적인 환자 모니터링** 처럼 친숙한 애플리케이션 이름을 직접 입력해도 됩니다. 또한 Azure IoT Central은 애플리케이션 이름을 기반으로 고유한 URL 접두사를 자동으로 생성합니다. 원하는 경우 이 URL 접두사를 더욱 기억하기 쉬운 것으로 자유롭게 변경할 수 있습니다.

2. *무료* 가격 책정 플랜 또는  *표준* 가격 책정 플랜 중 무엇을 사용하여 애플리케이션을 만들지 선택할 수 있습니다. 무료 플랜을 사용하여 만드는 애플리케이션은 7일 후 만료되며, 최대 5대의 디바이스에서 사용할 수 있습니다. 만료되기 전에는 언제든지 무료 플랜에서 표준 플랜으로 애플리케이션을 이동할 수 있습니다. 무료 플랜을 선택한 경우 연락처 정보를 입력하고 Microsoft에서 정보 및 팁을 받을 것인지 여부를 선택합니다. 표준 플랜을 사용하여 만든 애플리케이션은 최대 2개의 디바이스에서 사용할 수 있으며 요금 청구를 위해 Azure 구독 정보를 입력해야 합니다.

3. 페이지 맨 아래에서 **만들기** 를 선택하여 애플리케이션을 배포합니다.

## <a name="walk-through-the-application-template"></a>애플리케이션 템플릿 살펴보기

### <a name="dashboards"></a>대시보드

앱 템플릿을 배포한 후에는 가장 먼저 **Lamna 입원 환자 모니터링 대시보드** 로 이동됩니다. Lamna Healthcare는 Woodgrove 병원과 Burkville 병원을 포함하고 있는 가상의 병원 시스템입니다. Woodgrove 병원 운영자 대시보드에서 다음을 수행할 수 있습니다.

* 디바이스의 **배터리 잔량** 이나 **연결** 상태 같은 디바이스 원격 분석 데이터 및 속성을 확인합니다.

* 스마트 바이탈 패치 디바이스의 **평면도** 와 위치를 확인합니다.

* 새 환자의 스마트 바이탈 패치를 **다시 프로비저닝** 합니다.

* 병원의 진료 팀이 환자를 추적할 때 볼 수 있는 **공급자 대시보드** 의 예를 살펴봅니다.

* 디바이스가 입원 환자에게 사용되고 있는지 아니면 원격 시나리오에 사용되고 있는지 나타내도록 디바이스의 **환자 상태** 를 변경합니다.

:::image type="content" source="media/lamna-in-patient.png" alt-text="입원 환자 상태":::

**원격 환자 대시보드로 이동** 을 선택하여 Burkville 병원 운영자 대시보드를 볼 수도 있습니다. 이 대시보드에는 비슷한 작업, 원격 분석 데이터 및 정보 세트가 포함되어 있습니다. 또한 사용 중인 여러 디바이스를 확인하고, 각각에 대해 **펌웨어를 업데이트** 하도록 선택할 수 있습니다.

:::image type="content" source="media/lamna-remote.png" alt-text="원격 운영자 대시보드":::

### <a name="device-templates"></a>디바이스 템플릿

**디바이스 템플릿** 을 선택하면 템플릿에 두 가지 디바이스 유형이 표시됩니다.

* **스마트 바이탈 패치**: 이 디바이스는 다양한 활력징후를 측정하는 패치를 나타냅니다. 병원 내외부에서 환자를 모니터링하는 데 사용됩니다. 템플릿을 선택하면 패치에서 보내는 디바이스 데이터(예: 배터리 수준 및 디바이스 온도)와 환자 상태 데이터(예: 호흡수 및 혈압)가 모두 표시됩니다.

* **스마트 무릎 보호대**: 이 디바이스는 환자가 무릎 인공 관절 수술에서 회복할 때 사용하는 무릎 보호대를 나타냅니다. 이 템플릿을 선택하면 디바이스 데이터, 동작 범위 및 가속과 같은 기능이 표시됩니다.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="스마트 패치 템플릿":::

### <a name="device-groups"></a>Device groups

디바이스 그룹을 사용하여 디바이스 세트를 논리적으로 그룹화한 다음, 해당 디바이스에서 대량 쿼리 또는 작업을 실행할 수 있습니다.

디바이스 그룹 탭을 선택하면 애플리케이션의 각 디바이스 템플릿에 대한 기본 디바이스 그룹이 표시됩니다. **프로비저닝 디바이스** 및 **오래된 펌웨어가 있는 디바이스** 라는 두 개의 샘플 디바이스 그룹도 추가로 만들어집니다. 이러한 샘플 디바이스 그룹을 입력으로 사용하여 애플리케이션에서 일부 [작업](#jobs)을 실행할 수 있습니다.

### <a name="rules"></a>규칙

**규칙** 을 선택하면 템플릿에 다음 세 가지 규칙이 표시됩니다.

* **무릎 보호대 온도 높음**: 이 규칙은 스마트 무릎 보호대의 디바이스 온도가 5분 범위 동안 95&deg;F보다 높을 때 트리거됩니다. 환자 및 진료 팀에게 경고하고 디바이스를 원격으로 냉각시키려면 이 규칙을 사용합니다.

* **낙상 감지됨**: 이 규칙은 환자의 낙상이 감지되면 트리거됩니다. 낙상 환자를 지원하기 위해 운영 팀을 배치하는 작업을 구성하려면 이 규칙을 사용합니다.

* **패치 배터리 부족**: 이 규칙은 디바이스의 배터리 수준이 10% 미만이면 트리거됩니다. 환자에게 디바이스를 충전하도록 요구하는 알림을 트리거하려면 이 규칙을 사용합니다.

:::image type="content" source="media/brace-temp-rule.png" alt-text="규칙.":::

### <a name="jobs"></a>작업

작업을 사용하면 [디바이스 그룹](#device-groups)을 입력으로 사용하여 디바이스 세트에서 대량 작업을 실행할 수 있습니다. 애플리케이션 템플릿에는 운영자가 실행할 수 있는 다음 두 가지 샘플 작업이 있습니다.

* **무릎 보호대 펌웨어 업데이트**: 이 작업은 **오래된 펌웨어가 있는 디바이스** 디바이스 그룹에서 디바이스를 찾고, 해당 디바이스를 최신 펌웨어 버전으로 업데이트하는 명령을 실행합니다. 이 샘플 작업에서는 디바이스에서 **업데이트** 명령을 처리한 다음, 클라우드에서 펌웨어 파일을 가져올 수 있다고 가정합니다.  

* **디바이스 다시 프로비저닝**: 최근에 병원으로 반환된 디바이스 세트가 있습니다. 이 작업은 **프로비저닝 디바이스** 디바이스 그룹에서 디바이스를 찾고, 해당 디바이스를 다음 환자 세트에 다시 프로비저닝하는 명령을 실행합니다.

### <a name="devices"></a>디바이스

**디바이스** 탭을 선택한 다음, **스마트 무릎 보호대** 인스턴스를 선택합니다. 선택한 특정 디바이스에 대한 정보를 검색할 수 있는 세 가지 보기가 있습니다. 이러한 보기는 디바이스에 대한 디바이스 템플릿을 빌드할 때 만들어지고 게시됩니다. 따라서 이러한 보기는 연결하거나 시뮬레이션하는 모든 디바이스에서 일관됩니다.

**대시보드** 보기는 운영자 지향 원격 분석 및 디바이스 속성에 대한 개요를 제공합니다.

**속성** 탭을 사용하면 클라우드 속성을 편집하고 디바이스 속성을 읽고 쓸 수 있습니다.

**명령** 탭을 사용하면 디바이스에서 명령을 실행할 수 있습니다.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="무릎 보호대 대시보드":::

### <a name="data-export"></a>데이터 내보내기

데이터 내보내기를 사용하면 [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)을 포함한 다른 Azure 서비스에 디바이스 데이터를 지속적으로 내보낼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 **관리 > 애플리케이션 설정** 으로 이동한 후 **삭제** 를 클릭하여 애플리케이션을 삭제합니다.

:::image type="content" source="media/admin-delete.png" alt-text="리소스 정리":::

## <a name="next-steps"></a>다음 단계

다음 문서로 넘어가서 IoT Central 애플리케이션에 연결하는 공급자 대시보드를 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [공급자 대시보드 빌드](tutorial-health-data-triage.md)