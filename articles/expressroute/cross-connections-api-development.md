---
title: Azure Express 경로 CrossConnnections API 개발 및 통합
description: 이 문서에서는 expressRouteCrossConnections 리소스 종류에 대 한 Express 경로 파트너에 대 한 자세한 개요를 제공 합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187022"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Express 경로 CrossConnnections API 개발 및 통합

Express 경로 파트너 리소스 관리자 API를 사용 하 여 Express 경로 파트너는 고객 Express 회로의 계층 2 및 계층 3 구성을 관리할 수 있습니다. Express 경로 파트너 리소스 관리자 API에는 새 리소스 유형인 **expressRouteCrossConnections**가 도입 되었습니다. 파트너는이 리소스를 사용 하 여 고객 Express 경로 회로를 관리 합니다.

## <a name="workflow"></a>워크플로

ExpressRouteCrossConnections 리소스는 Express 경로 회로에 대 한 그림자 리소스입니다. Azure 고객이 Express 경로 회로를 만들고 특정 Express 경로 파트너를 선택 하는 경우 Microsoft는 파트너의 Azure Express 경로 관리 구독에 expressRouteCrossConnections 리소스를 만듭니다. 이렇게 하는 경우 Microsoft는에서 expressRouteCrossConnections 리소스를 만들 리소스 그룹을 정의 합니다. 리소스 그룹에 대 한 명명 표준은 **간 연결-* peeringlocation * * *입니다. 여기서 PeeringLocation = Express 경로 위치입니다. 예를 들어 고객이 덴버에서 Express 경로 회로를 만드는 경우 덴버의 리소스 그룹에서 파트너의 Azure 구독에 **간**연결이 생성 됩니다.

Express 경로 파트너는 expressRouteCrossConnections 리소스에 대해 REST 작업을 실행 하 여 계층 2 및 계층 3 구성을 관리 합니다.

## <a name="benefits"></a>이점

ExpressRouteCrossConnections 리소스로 이동할 경우의 이점:

* Express 경로 파트너에 대 한 향후 개선 사항은 ExpressRouteCrossConnection 리소스에서 제공 될 예정입니다.

* 파트너는 expressRouteCrossConnection 리소스에 [역할 기반 Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) 적용할 수 있습니다. 이러한 컨트롤은 사용자 계정에서 expressRouteCrossConnection 리소스를 수정 하 고 피어 링 구성을 추가/업데이트/삭제할 수 있는 권한을 정의할 수 있습니다.

* ExpressRouteCrossConnection 리소스는 Express 경로 연결 문제를 해결 하는 데 도움이 될 수 있는 Api를 노출 합니다. 여기에는 ARP 테이블, BGP 경로 테이블 요약 및 BGP 경로 테이블 정보가 포함 됩니다. 이 기능은 클래식 배포 Api에서 지원 되지 않습니다.

* 파트너는 *RouteFilter* 리소스를 사용 하 여 Microsoft 피어 링에서 보급 된 커뮤니티를 조회할 수도 있습니다.

## <a name="api-development-and-integration-steps"></a>API 개발 및 통합 단계

파트너 API에 대해 개발 하기 위해 Express 경로 파트너는 테스트 고객 및 테스트 파트너 설정을 활용 합니다. 테스트 고객 설치 프로그램은 더미 장치 및 포트에 매핑되는 테스트 피어 링 위치에서 Express 경로 회로를 만드는 데 사용 됩니다. 테스트 파트너 설치는 테스트 피어 링 위치에 생성 된 Express 경로 회로를 관리 하는 데 사용 됩니다.

### <a name="1-enlist-subscriptions"></a>1. 구독 참여

테스트 파트너를 요청 하 고 고객 설정을 테스트 하려면 Express 경로 엔지니어링 담당자에 게 두 개의 종 량 제 Azure 구독을 등록 합니다.
* **ExpressRoute_API_Dev_Provider_Sub:** 이 구독은 더미 장치 및 포트에서 테스트 피어 링 위치에 생성 되는 Express 경로 회로를 관리 하는 데 사용 됩니다.

* **ExpressRoute_API_Dev_Customer_Sub:** 이 구독은 더미 장치 및 포트에 매핑되는 테스트 피어 링 위치에서 Express 경로 회로를 만드는 데 사용 됩니다.

테스트 피어 링 위치: 더미 장치 및 포트는 기본적으로 프로덕션 고객에 게 노출 되지 않습니다. 테스트 설정에 매핑되는 Express 경로 회로를 만들려면 구독 기능 플래그를 사용 하도록 설정 해야 합니다.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. expressRouteCrossConnections API에 액세스 하는 Dev_Provider 구독 등록

ExpressRouteCrossConnections API에 액세스 하려면 파트너 구독을 **Microsoft 네트워크 리소스 공급자**에 등록 해야 합니다. [Azure 리소스 공급자 및 유형](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) 문서의 단계에 따라 등록 프로세스를 완료 합니다.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Azure Resource Manager REST API 호출에 대 한 인증 설정

대부분의 Azure 서비스는 서비스 Api를 호출 하기 전에 유효한 자격 증명을 사용 하 여 리소스 관리자 인증 하는 클라이언트 코드를 요구 합니다. 인증은 Azure AD의 다양 한 행위자 간에 조정 되며 인증 증명으로 클라이언트에 액세스 토큰을 제공 합니다.

인증 프로세스에는 두 가지 주요 단계가 포함 됩니다.

1. [클라이언트를 등록](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
2. [액세스 요청을 만듭니다](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 클라이언트 응용 프로그램에 대 한 네트워크 참가자 권한 제공

인증이 성공적으로 구성 되 면 Dev_Provider_Sub에서 클라이언트 응용 프로그램에 대 한 네트워크 참가자 액세스 권한을 부여 해야 합니다. 사용 권한을 부여 하려면 [Azure Portal](https://ms.portal.azure.com/#home) 에 로그인 하 고 다음 단계를 완료 합니다.

1. 구독으로 이동 하 여 Dev_Provider_Sub을 선택 합니다.
2. Access Control (IAM)로 이동 합니다.
3. 역할 할당 추가
4. 네트워크 참가자 역할을 선택 합니다.
5. Azure AD 사용자, 그룹 또는 서비스 주체에 대 한 액세스 할당
6. 클라이언트 응용 프로그램 선택
7. 변경 내용 저장

### <a name="5-develop"></a>5. 개발

[EXPRESSROUTECROSSCONNECTIONS API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)에 대해 개발 합니다.

## <a name="rest-api"></a>REST API

REST API 설명서 [REST API express 경로 연결](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

모든 Express 경로 REST Api에 대 한 자세한 내용은 [express 경로 Rest api](https://docs.microsoft.com/rest/api/expressroute/)를 참조 하세요.