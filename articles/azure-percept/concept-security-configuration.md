---
title: Azure Percept 방화벽 구성 및 보안 권장 사항
description: Azure Percept 방화벽 구성 및 보안 권장 사항에 대 한 자세한 정보
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604416"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Azure Percept 방화벽 구성 및 보안 권장 사항

Azure Percept를 사용 하 여 방화벽 및 일반적인 보안 모범 사례를 구성 하는 방법에 대 한 자세한 내용은 아래 지침을 검토 하세요.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept에 대 한 방화벽 구성

네트워킹 설정에서 Azure Percept 진한 장치에서 연결을 명시적으로 허용 해야 하는 경우 다음 구성 요소 목록을 검토 하세요.

다음 검사 목록은 방화벽 규칙의 시작점입니다.

|URL (* = 와일드 카드)|아웃바운드 TCP 포트|사용량|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Azure 진한 SOM 인증 및 권한 부여|
|*. auth.projectsantacruz.azure.net|443|Azure 진한 SOM 인증 및 권한 부여|

또한 [Azure IoT Edge에서 사용 하는 연결](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices)의 목록을 검토 합니다.

## <a name="additional-recommendations-for-deployment-to-production"></a>프로덕션 환경에 대 한 추가 권장 사항

Azure Percept는 기본 제공 되는 다양 한 보안 기능을 제공 합니다. Microsoft는 현재 릴리스에 포함 된 강력한 보안 기능 외에도 프로덕션 배포를 고려할 때 다음과 같은 지침을 제안 합니다.

- 장치 자체의 강력한 물리적 보호
- 저장소 데이터 암호화가 사용 되도록 설정 되어 있는지 확인
- 지속적으로 장치 상태를 모니터링 하 고 경고에 신속 하 게 응답
- 장치에 대 한 액세스 권한이 있는 관리자 수 제한

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Percept 보안에 대 한 자세한 정보](./overview-percept-security.md)