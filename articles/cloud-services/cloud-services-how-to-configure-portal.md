---
title: 클라우드 서비스를 구성하는 방법(포털) | Microsoft Docs
description: Azure에서 Cloud Services를 구성하는 방법에 대해 알아봅니다. 또한 클라우드 서비스 구성을 업데이트하고 역할 인스턴스에 대한 원격 액세스를 구성하는 방법도 알아봅니다. 이 예제는 Azure Portal을 사용합니다.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 4d8d3b93ef2a6347076fada53932b5fc56838d20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435812"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services를 구성하는 방법

Azure Portal에서 클라우드 서비스에 가장 일반적으로 사용되는 설정을 구성할 수 있습니다. 또는 구성 파일을 직접 업데이트하려는 경우 업데이트할 서비스 구성 파일을 다운로드한 후 업데이트된 파일을 업로드하고 구성 변경 내용으로 클라우드 서비스를 업데이트합니다. 어느 방법이든 모든 역할 인스턴스에 구성 업데이트를 적용합니다.

클라우드 서비스 역할의 인스턴스 또는 이에 대한 원격 데스크톱을 관리할 수도 있습니다.

Azure는 각 역할에 둘 이상의 역할 인스턴스가 있는 경우에만 구성 업데이트 중 99.95%의 서비스 가용성을 보장할 수 있습니다. 이에 따라, 가상 머신 하나는 클라이언트 요청을 처리하고 다른 하나는 업데이트를 진행할 수 있습니다. 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## <a name="change-a-cloud-service"></a>클라우드 서비스 변경

[Azure Portal](https://portal.azure.com/)을 연 후 클라우드 서비스로 이동합니다. 여기에서 여러 항목을 관리할 수 있습니다.

![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**설정** 또는 **모든 설정** 링크를 클릭하면 **설정**이 열리며 여기에서 **속성** 및 **구성**을 변경하고 **인증서**를 관리하며 **경고 규칙**을 설정하고 이 클라우드 서비스에 액세스하는 **사용자**를 관리할 수 있습니다.

![Azure 클라우드 서비스 설정](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>게스트 OS 버전 관리

기본적으로 Azure는 Windows Server 2016 같은 게스트 OS를 서비스 구성(.cscfg)에 지정한 OS 제품군 내에서 지원되는 최신 이미지로 주기적으로 업데이트합니다.

특정 OS 버전을 대상으로 해야 하는 경우 **구성**에서 설정할 수 있습니다.

![OS 버전 설정](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> 특정 OS 버전을 선택하면 자동 OS 업데이트가 사용되지 않도록 설정되며 패치는 사용자가 책임지고 진행해야 합니다. 즉, 역할 인스턴스가 업데이트를 수신하도록 해야 합니다. 그러지 않으면 애플리케이션이 보안 취약점에 노출될 수 있습니다.

## <a name="monitoring"></a>모니터링

클라우드 서비스에 경고를 추가할 수 있습니다. **설정** > **경고 규칙** > **경고 추가**를 클릭합니다.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

여기에서 경고를 설정할 수 있습니다. **메트릭** 드롭다운 상자에서 다음 데이터 형식에 대한 경고를 설정할 수 있습니다.

* 디스크 읽기
* 디스크 쓰기
* 네트워크 입력
* 네트워크 출력
* CPU 비율

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>메트릭 타일에서 모니터링 구성

**설정** > **경고 규칙**을 사용하는 대신 클라우드 서비스의 **모니터링** 섹션에서 메트릭 타일 중 하나를 클릭할 수 있습니다.

![클라우드 서비스 모니터링](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

여기에서 타일에 사용되는 차트를 사용자 지정하거나 경고 규칙을 추가할 수 있습니다.

## <a name="reboot-reimage-or-remote-desktop"></a>다시 부팅, 이미지로 다시 설치 또는 원격 데스크톱

[Azure Portal(원격 데스크톱 설정)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 또는 [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)를 통해 원격 데스크톱을 설정할 수 있습니다.

다시 부팅, 이미지로 다시 설치 또는 클라우드 서비스에 원격으로 연결하려면 클라우드 서비스 인스턴스를 선택합니다.

![클라우드 서비스 인스턴스](./media/cloud-services-how-to-configure-portal/cs-instance.png)

원격 데스크톱 연결을 시작하고 인스턴스를 원격으로 다시 부팅하거나 인스턴스를 원격으로 이미지로 다시 설치(새 이미지로 시작)할 수 있습니다.

![클라우드 서비스 인스턴스 단추](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>.cscfg 다시 구성

[서비스 구성(cscfg)](cloud-services-model-and-package.md#cscfg) 파일을 통해 클라우드 서비스를 다시 구성해야 할 수 있습니다. 먼저 .cscfg 파일을 다운로드하고 수정한 후 업로드해야 합니다.

1. **설정** 아이콘 또는 **모든 설정** 링크를 클릭하여 **설정**을 엽니다.

    ![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. **구성** 항목을 클릭합니다.

    ![구성 블레이드](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. **다운로드** 단추를 클릭합니다.

    ![다운로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. 서비스 구성 파일을 업데이트한 후 구성 업데이트를 업로드하고 적용합니다.

    ![업로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. .cscfg 파일을 선택하고 **확인**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy-portal.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name-portal.md)을 구성합니다.
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage-portal.md).
* [SSL 인증서](cloud-services-configure-ssl-certificate-portal.md)구성
