---
title: Azure Monitor를 사용 하 여 ArcSight와 로그 통합 Microsoft Docs
description: 를 사용 하 여 ArcSight와 Azure Active Directory 로그를 통합 하는 방법을 알아봅니다 Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230520"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Azure Monitor를 사용하여 ArcSight와 Azure Active Directory 로그 통합

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview)는 플랫폼에서 보안 위협을 탐지하여 대응하는 데 도움이 되는 보안 정보 및 이벤트 관리(SIEM)입니다. 이제 Azure AD용 ArcSight 커넥터를 사용하는 Azure Monitor를 사용하여 ArcSight로 Azure AD(Azure Active Directory) 로그를 라우팅할 수 있습니다. 이 기능을 통해 ArcSight를 사용하여 테넌트에 보안 위협이 있는지 모니터링할 수 있습니다.  

이 문서에서는 Azure Monitor를 사용하여 ArcSight로 Azure AD 로그를 라우팅하는 방법을 배웁니다. 

## <a name="prerequisites"></a>전제 조건

이 기능을 사용하려면 다음이 필요합니다.
* Azure AD 활동 로그를 포함하는 Azure 이벤트 허브입니다. [활동 로그를 이벤트 허브로 스트림](./tutorial-azure-monitor-stream-logs-to-event-hub.md)하는 방법을 알아봅니다. 
* ArcSight Syslog NG Daemon SmartConnector(SmartConnector) 또는 ArcSight Load Balancer의 구성된 인스턴스입니다. 이벤트를 ArcSight Load Balancer로 보내면 부하 분산 장치는 이어서 SmartConnector로 보냅니다.

[Azure Monitor 이벤트 허브용 ArcSight SmartConnector의 구성 가이드](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)를 다운로드하고 엽니다. 이 가이드에는 Azure Monitor용 ArcSight SmartConnector를 설치하고 구성하는 데 필요한 단계가 포함되어 있습니다. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>ArcSight와 Azure AD 로그 통합

1. 첫째, 구성 가이드의 **필수 구성 요소** 섹션에 있는 단계를 완료합니다. 이 섹션에는 다음 단계가 포함됩니다.
    * 커넥터를 배포하고 구성하는 **소유자** 역할을 가진 사용자가 있도록 Azure에서 사용자 권한을 설정합니다.
    * Azure에서 액세스 가능하도록 Syslog NG Daemon SmartConnector를 사용하여 서버에서 포트를 엽니다. 
    * 배포에서 Windows PowerShell 스크립트를 사용하므로, 커넥터를 배포하려는 머신에서 스크립트를 실행하도록 PowerShell을 사용 설정해야 합니다.

2. 구성 가이드의 **커넥터 배포** 섹션에 있는 단계에 따라 커넥터를 배포합니다. 이 섹션에서는 커넥터를 다운로드하고 압축을 풀며, 압축을 푼 폴더에서 애플리케이션 속성을 구성하고 배포 스크립트를 실행하는 방법을 안내합니다. 

3. **Azure에서 배포 확인**의 단계를 사용하여 커넥터가 제대로 설정되어 기능하는지 확인합니다. 다음을 확인합니다.
    * 필수 Azure 함수는 Azure 구독에서 만들어집니다.
    * Azure AD 로그가 올바른 대상으로 스트리밍됩니다. 
    * 배포의 애플리케이션 설정은 Azure 함수 앱의 애플리케이션 설정에서 그대로 유지됩니다. 
    * ArcSight 커넥터에 대한 Azure AD 애플리케이션과 CEF 형식으로 매핑된 파일을 포함한 스토리지 계정과 함께, ArcSight에 대한 새 리소스 그룹이 Azure에 생성됩니다.

4. 마지막으로, 구성 가이드의 **배포 후 구성**에 있는 배포 후 단계를 완료합니다. 이 섹션에서는 시간 제한 기간 후 함수 앱이 유휴 상태로 전환 되지 않도록 App Service 계획을 세울 때 추가 구성을 수행 하는 방법에 대해 설명 하 고, 이벤트 허브에서 리소스 로그 스트리밍을 구성 하 고, SysLog 추가 디먼 SmartConnector 키 저장소 certificate를 업데이트 하 여 새로 만든 저장소 계정에 연결 합니다.

5. 또한 구성 가이드에서는 Azure에서 커넥터 속성을 사용자 지정하고 커넥터를 업그레이드 및 제거하는 방법에 대해서도 설명하고 있습니다. 아울러 이벤트 부하가 단일 Syslog NG Daemon SmartConnector로 처리할 수 있는 것보다 큰 경우 [Azure Consumption 계획](https://azure.microsoft.com/pricing/details/functions)으로 업그레이드 및 ArcSight Load Balancer 구성을 포함해 성능 개선 사항에 대한 섹션도 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor 이벤트 허브용 ArcSight SmartConnector의 구성 가이드](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)