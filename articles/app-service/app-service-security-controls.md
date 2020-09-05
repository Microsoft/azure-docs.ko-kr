---
title: Azure App Service에 대 한 보안 제어
description: 조직의 Azure App Service를 평가 하기 위한 보안 컨트롤의 검사 목록을 찾습니다.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962930"
---
# <a name="security-controls-for-azure-app-service"></a>Azure App Service에 대 한 보안 제어

이 문서에서는 Azure App Service에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|
| 서비스 엔드포인트 지원| 예 | App Service 사용할 수 있습니다.| [Azure App Service 액세스 제한](app-service-ip-restrictions.md)
| VNet 삽입 지원| 예 | App Service 환경은 고객의 가상 네트워크에 주입 된 단일 고객 전용 App Service의 개인 구현입니다. | [App Service Environment 소개](environment/intro.md)
| 네트워크 격리 및 방화벽 지원| 예 | 공용 다중 테 넌 트 변형 App Service의 경우 고객은 네트워크 Acl (IP 제한)을 구성 하 여 허용 되는 인바운드 트래픽을 잠글 수 있습니다.  App Service 환경은 가상 네트워크에 직접 배포 되므로 NSGs를 사용 하 여 보안을 유지할 수 있습니다. | [Azure App Service 액세스 제한](app-service-ip-restrictions.md)
| 강제 터널링 지원| 예 | 강제 터널링이 구성 된 고객의 가상 네트워크에 App Service 환경을 배포할 수 있습니다. | [강제 터널링으로 App Service Environment 구성](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|
| Azure 모니터링 지원(Log Analytics, App insights 등)| 예 | App Service는 Application Insights (전체 .NET Framework, .NET Core, Java 및 Node.JS)를 지 원하는 언어에 대 한 Application Insights와 통합 됩니다.  [Azure App Service 성능 모니터링](../azure-monitor/app/azure-web-apps.md)을 참조 하세요. 또한 App Service는 응용 프로그램 메트릭을 Azure Monitor 보냅니다. | [Azure App Service에서 앱 모니터링](web-sites-monitor.md)
| 제어와 관리 평면 로깅 및 감사| 예 | App Service 개체에서 수행 되는 모든 관리 작업은 [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해 수행 됩니다. 이러한 작업의 기록 로그는 포털에서 CLI를 통해 사용할 수 있습니다. | [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az monitor activity-log](/cli/azure/monitor/activity-log)
| 데이터 평면 로깅 및 감사 | 아니요 | App Service에 대 한 데이터 평면은 고객의 배포 된 웹 사이트 콘텐츠를 포함 하는 원격 파일 공유입니다.  원격 파일 공유에 대 한 감사는 없습니다. |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모 |  문서화
|---|---|--|
| 인증| 예 | 고객은 [Azure Active Directory (AZURE ad)](../active-directory/index.yml) 와 App Service 자산에 대 한 관리 액세스를 위한 다른 OAuth 호환 id 공급자를 자동으로 통합 하는 App Service에서 응용 프로그램을 빌드할 수 있습니다. 모든 액세스는 azure ad 인증 된 주체 및 azure RBAC를 조합 하 여 제어 됩니다. | [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)
| 권한 부여| 예 | App Service 자산에 대 한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 된 주체 및 Azure RBAC의 조합에 의해 제어 됩니다.  | [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 예 | 웹 사이트 파일 콘텐츠는 미사용 콘텐츠를 자동으로 암호화 하는 Azure Storage에 저장 됩니다. <br><br>고객 제공 암호는 미사용 암호화 됩니다. 비밀은 App Service 구성 데이터베이스에 저장 되어 있는 동안 암호화 되지 않습니다.<br><br>필요에 따라 로컬에 연결 된 디스크를 웹 사이트 (D:\local 및% TMP%)에서 임시 저장소로 사용할 수 있습니다. 로컬에 연결 된 디스크는 미사용 상태로 암호화 되지 않습니다. | [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)
| 미사용 서버 쪽 암호화: 고객 관리형 키(BYOK) | 예 | 고객은 Key Vault에 응용 프로그램 암호를 저장 하 고 런타임에 검색 하도록 선택할 수 있습니다. | [App Service 및 Azure Functions의 Key Vault 참조 사용(미리 보기)](app-service-key-vault-references.md)
| 열 수준 암호화(Azure Data Services)| 해당 없음 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 고객은 인바운드 트래픽에 대해 HTTPS를 요구 하 고 사용 하도록 웹 사이트를 구성할 수 있습니다.  | [AZURE APP SERVICE HTTPS만 만드는 방법](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (블로그 게시물)
| API 호출 암호화| 예 | App Service를 구성 하는 관리 호출은 HTTPS를 통한 [Azure Resource Manager](../azure-resource-manager/index.yml) 호출을 통해 수행 됩니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모 | 문서화
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| 예 | 관리 작업의 경우 App Service 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. 런타임 작업의 경우 고객은 배포 슬롯 App Service 기능을 사용 하 여 응용 프로그램의 여러 다른 라이브 버전을 유지할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.