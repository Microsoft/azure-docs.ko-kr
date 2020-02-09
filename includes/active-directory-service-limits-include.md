---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 01/22/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 2e06a6c8dd7eb58769f504db9f96e0303c3e9f4c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748971"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| 범주 | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 20개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와의 페더레이션용으로 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
|리소스 |<ul><li>기본적으로 Azure Active Directory 무료 버전의 사용자는 단일 디렉터리에 최대 5만 개의 Azure AD 리소스를 만들 수 있습니다. 확인 된 도메인이 하나 이상 있는 경우 Azure AD의 기본 디렉터리 서비스 할당량은 30만 Azure AD 리소스로 확장 됩니다. </li><li>비관리자 사용자는 Azure AD 리소스를 250 개 이하로 만들 수 있습니다. 복원에 사용할 수 있는 활성 리소스와 삭제 된 리소스는 모두이 할당량으로 계산 됩니다. 30 일 이내에 삭제 된 Azure AD 리소스를 삭제 한 경우에만 복원할 수 있습니다. 30 일 동안 1 사분기 값에서 더 이상 복원할 수 없는 Azure AD 리소스를이 할당량으로 계산 합니다. 정규 업무를 진행 하는 동안이 할당량을 반복적으로 초과할 가능성이 높은 개발자가 있는 경우 무제한의 앱 등록을 만들 수 있는 권한이 있는 [사용자 지정 역할을 만들고 할당할](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) 수 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>*모든* 형식 및 *모든* 응용 프로그램에서 100 확장 값만 단일 Azure AD 리소스에 쓸 수 있습니다.</li><li>User, Group, TenantDetail, Device, Application 및 ServicePrincipal 엔터티만 String 형식 또는 Binary 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
|응용 프로그램 매니페스트 |응용 프로그램 매니페스트에서 최대 1200 항목을 추가할 수 있습니다. |
| 그룹 |<ul><li>사용자는 Azure AD 조직에서 최대 250의 그룹을 만들 수 있습니다.</li><li>Azure AD 조직에는 최대 5000 개의 동적 그룹이 있을 수 있습니다.<li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>Azure AD 리소스의 수에 관계 없이 단일 그룹의 멤버일 수 있습니다.</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버 수는 50,000으로 제한됩니다.</li><li>Azure AD의 중첩 그룹은 모든 시나리오에서 지원 되지 않습니다.</li></ul><br/> 이번에는 중첩 된 그룹에 대해 지원 되는 시나리오는 다음과 같습니다.<ul><li> 한 그룹을 다른 그룹의 멤버로 추가 하 고 그룹 중첩을 달성할 수 있습니다.</li><li> 그룹 멤버 자격 클레임 (토큰에서 그룹 구성원 자격 클레임을 수신 하도록 앱이 구성 된 경우 로그인 한 사용자가 구성원 인 중첩 된 그룹 포함)</li><li>조건부 액세스 (그룹에 대한 조건부 액세스 정책의 범위를 지정 하는 경우)</li><li>셀프 서비스 암호 재설정에 대한 액세스 제한</li><li>Azure AD 조인 및 장치 등록을 수행할 수 있는 사용자 제한</li></ul><br/>다음 시나리오에서는 중첩 된 그룹을 지원 하지 않습니다.<ul><li> 앱 역할 할당 (앱에 그룹을 할당 하는 것은 지원 되지만 직접 할당 된 그룹 내에 중첩 된 그룹에는 액세스 권한이 없음) 및 프로 비전에 대한 액세스 권한이 있습니다.</li><li>그룹 기반 라이선스 (그룹의 모든 구성원에 게 자동으로 라이선스 할당)</li><li>Office 365 그룹.</li></ul> |
| 애플리케이션 프록시 | <ul><li>앱 프록시 응용 프로그램당 초당 최대 500 개의 트랜잭션</li><li>Azure AD 조직에서 초당 최대 750 개의 트랜잭션</li></ul><br/>트랜잭션은 단일 http 요청 및 고유 리소스에 대한 응답으로 정의 됩니다. 제한 되는 경우 클라이언트는 429 응답 (너무 많은 요청)을 수신 합니다. |
| 액세스 패널 |<ul><li>사용자당 액세스 패널에서 볼 수 있는 애플리케이션의 수는 제한이 없습니다. 이는 Azure AD Premium 또는 Enterprise Mobility Suite용 라이선스가 할당된 사용자에게 적용됩니다.</li><li>각 사용자는 액세스 패널에서 최대 10개의 앱 타일을 볼 수 있습니다. 이 제한은 Azure AD Free 라이선스 계획에 대한 라이선스가 할당 된 사용자에 게 적용 됩니다. 앱 타일의 예로는 Box, Salesforce 또는 Dropbox가 있습니다. 관리자 계정에는 이 제한이 적용되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | Azure AD 리소스는 30 개 이하의 관리 단위 구성원이 될 수 있습니다. |
| 관리자 역할 및 권한 | <ul><li>그룹을 [소유자로](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)추가할 수 없습니다.</li><li>그룹을 [역할](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)에 할당할 수 없습니다.</li><li>모든 디렉터리 정보에 대한 관리자가 아닌 사용자의 모든 액세스를 사용 하지 않도록 설정 하려면 Azure AD 조직 전체 스위치 외부에서 다른 사용자의 디렉터리 정보를 읽는 기능을 제한할 수 없습니다 (권장 하지 않음). 기본 권한에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)를 참조 하세요.</li><li>관리자 역할 멤버 자격을 추가 하 고 해지 적용 하려면 최대 15 분이 걸릴 수 있습니다.</li></ul> |
