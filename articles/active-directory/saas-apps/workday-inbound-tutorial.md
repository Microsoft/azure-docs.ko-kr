---
title: '자습서: Azure Active Directory를 통해 자동 사용자 프로비전을 위한 Workday 구성 | Microsoft Docs'
description: 사용자 계정을 Workday로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 51ab05a995ba5b620b759f419fb5b4594873d2f5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527811"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Workday 구성

이 자습서에서는 Workday에서 온-프레미스 AD (Active Directory)로 작업자 프로필을 프로 비전 하기 위해 수행 해야 하는 단계를 보여 줍니다.

>[!NOTE]
>Workday에서 프로 비전 하려는 사용자가 온-프레미스 AD 계정 및 Azure AD 계정을 필요로 하는 경우이 자습서를 사용 합니다. 
>* Workday의 사용자에 게 Azure AD 계정 (클라우드 전용 사용자)이 필요한 경우 Azure AD 사용자 프로 비전 [에 workday 구성](workday-inbound-cloud-only-tutorial.md) 의 자습서를 참조 하세요. 
>* Azure AD에서 Workday로 메일 주소, 사용자 이름 및 전화 번호와 같은 특성의 쓰기 저장 (writeback)을 구성 하려면 [workday 쓰기 저장 구성](workday-writeback-tutorial.md)에 대 한 자습서를 참조 하세요.


## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비전 서비스](../app-provisioning/user-provisioning.md)는 사용자 계정을 프로비전하기 위해 [Workday 인적 자원 API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)와 통합됩니다. Azure AD 사용자 프로비전 서비스에서 지원되는 Workday 사용자 프로비전 워크플로는 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오의 자동화를 지원합니다.

* **신규 직원 고용** -신규 직원이 workday에 추가 되 면 Active Directory, Azure Active Directory 및 [Azure AD에서 지원 되는 Office 365 및 기타 SaaS 응용 프로그램](../app-provisioning/user-provisioning.md)에서 사용자 계정이 자동으로 생성 되며,이를 통해 IT 관리 연락처 정보를 workday에 다시 쓸 수 있습니다.

* **직원 특성 및 프로필 업데이트** - Workday에서 이름, 직함, 관리자 등의 직원 레코드가 업데이트되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 해당 사용자 계정이 자동으로 업데이트됩니다.

* **직원 퇴사** - Workday에서 직원이 퇴사하면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 해당 사용자 계정이 자동으로 비활성화됩니다.

* **직원 다시 채용** - Workday에서 직원이 다시 채용되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 해당 직원의 기존 계정이 자동으로 다시 활성화되거나 다시 프로비전됩니다(기본 설정에 따라).

### <a name="whats-new"></a>새로운 기능
이 섹션에서는 최신 Workday 통합 기능 향상을 캡처합니다. 포괄적인 업데이트, 계획 된 변경 내용 및 보관에 대 한 목록은 [Azure Active Directory의 새로운 기능](../fundamentals/whats-new.md) 페이지를 참조 하세요. 

* **2020 년 5 월에 전화 번호를 Workday에 쓰기 저장 하는 기능:** 전자 메일 및 사용자 이름 외에도 이제 회사 전화 번호 및 휴대폰 번호를 Azure AD에서 Workday로 쓰기 저장 (writeback) 할 수 있습니다. 자세한 내용은 [쓰기 저장 (writeback) 앱 자습서](workday-writeback-tutorial.md)를 참조 하세요.

* **4 월 2020-최신 버전의 WWS (Workday 웹 서비스) API에 대 한 지원:** 3 월과 9 월에 1 년에 두 번 Workday는 비즈니스 목표를 달성 하 고 인력 수요를 변경 하는 데 도움이 되는 기능을 풍부한 업데이트를 제공 합니다. Workday에서 제공 하는 새로운 기능을 계속 사용 하려면 이제 연결 URL에서 사용할 WWS API 버전을 직접 지정할 수 있습니다. Workday API 버전을 지정 하는 방법에 대 한 자세한 내용은 [workday 연결 구성](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)에 대 한 섹션을 참조 하세요. 

* **1 월 2020-AD accountExpires 특성을 설정 하는 기능:** 이제 [NumFromDate](../app-provisioning/functions-for-customizing-application-data.md#numfromdate) 함수를 사용 하 여 *EndContractDate* 또는 *StatusTerminationDate*과 같은 Workday 날짜 필드를 매핑할 수 있습니다. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 Workday 사용자 프로비저닝 솔루션은 다음과 같은 경우에 가장 적합합니다.

* Workday 사용자 프로비전에 미리 작성된 클라우드 기반 솔루션을 원하는 조직

* Workday에서 Active Directory 또는 Azure Active Directory로 직접 사용자 프로비전이 필요한 조직

* Workday HCM 모듈에서 가져온 데이터를 사용하여 사용자를 프로비전해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조)

* Workday HCM 모듈에서 감지된 변경 내용 정보에 따라서만 하나 이상의 Active Directory 포리스트, 도메인 및 OU를 동기화하도록 사용자를 조인하고, 이동시키고, 유지해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조)

* 이메일에 Office 365를 사용하는 조직

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 일반적인 하이브리드 환경을 위한 엔드투엔드 사용자 프로비전 솔루션 아키텍처에 대해 설명합니다. 두 가지의 관련된 흐름이 있습니다.

* **Workday에서 온-프레미스 Active Directory로 신뢰할 수 있는 HR 데이터 흐름:** 이 흐름에서 작업자 이벤트 (예: 새 채용, 전송, 종료)는 먼저 cloud Workday HR 테 넌 트에서 발생 하 고, 이벤트 데이터는 Azure AD 및 프로 비전 에이전트를 통해 온-프레미스 Active Directory로 흐릅니다. 이벤트에 따라 AD에서 만들기/업데이트/사용/사용 안 함 작업이 이루어질 수 있습니다.
* **쓰기 저장 흐름 – 온-프레미스 Active Directory에서 Workday로:** Active Directory에서 계정 만들기가 완료 되 면 Azure AD Connect를 통해 Azure AD와 동기화 되 고 전자 메일, 사용자 이름 및 전화 번호와 같은 정보를 Workday에 다시 쓸 수 있습니다.

![개요](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>엔드투엔드 사용자 데이터 흐름

1. HR 팀은 Workday HCM에서 작업자 트랜잭션(참가자/이동자/이탈자 또는 신규 고용/전근/종료)을 수행합니다.
2. Azure AD Provisioning Service는 Workday HR에서 예약된 ID 동기화를 실행하고 온-프레미스 Active Directory와의 동기화를 위해 처리해야 하는 변경 내용을 식별합니다.
3. Azure AD Provisioning Service는 AD 계정 만들기/업데이트/사용/사용 안 함 작업이 포함된 요청 페이로드로 온-프레미스 Azure AD Connect Provisioning Agent를 호출합니다.
4. Azure AD Connect 프로비전 에이전트는 서비스 계정을 사용하여 AD 계정 데이터를 추가/업데이트합니다.
5. Azure AD Connect / AD Sync 엔진이 AD에서 업데이트를 끌어오기 위해 델타 동기화를 실행합니다.
6. Active Directory 업데이트는 Azure Active Directory와 동기화됩니다.
7. [Workday 쓰기 저장](workday-writeback-tutorial.md) 앱이 구성 되 면 전자 메일, 사용자 이름 및 전화 번호와 같은 특성을 workday에 기록 합니다.

## <a name="planning-your-deployment"></a>배포 계획

Workday를 Active Directory 사용자 프로 비전을 구성 하려면 다음과 같은 다양 한 측면을 포함 하는 상당한 계획이 필요 합니다.
* Azure AD Connect 프로 비전 에이전트 설정 
* 배포할 Workday 사용자 프로 비전 앱 수
* 오른쪽 일치 식별자, 특성 매핑, 변환 및 범위 지정 필터 선택

포괄적인 지침 및 권장 되는 모범 사례는 [CLOUD HR 배포 계획](../app-provisioning/plan-cloud-hr-provision.md) 을 참조 하세요. 

## <a name="configure-integration-system-user-in-workday"></a>Workday에서 통합 시스템 사용자 구성

모든 Workday 프로비전 커넥터의 일반적인 요구 사항으로, 해당 커넥터는 Workday Human Resources API에 연결하는 Workday 통합 시스템 사용자의 자격 증명을 요구합니다. 이 섹션에서는 Workday에서 통합 시스템 사용자를 만드는 방법을 설명하고 다음 섹션을 포함합니다.

* [통합 시스템 사용자 만들기](#creating-an-integration-system-user)
* [통합 보안 그룹 만들기](#creating-an-integration-security-group)
* [도메인 보안 정책 권한 구성](#configuring-domain-security-policy-permissions)
* [비즈니스 프로세스 보안 정책 권한 구성](#configuring-business-process-security-policy-permissions)
* [보안 정책 변경 사항 활성화](#activating-security-policy-changes)

> [!NOTE]
> 이 절차를 건너뛰고 Workday 전역 Administrator 계정을 시스템 통합 계정으로 사용할 수도 있습니다. 이 방법이 데모에서는 제대로 작동할 수 있지만 프로덕션 배포에는 권장하지 않습니다.

### <a name="creating-an-integration-system-user"></a>통합 시스템 사용자 만들기

**통합 시스템 사용자를 만들려면**

1. Administrator 계정을 사용하여 Workday 테넌트에 로그인합니다. **Workday 애플리케이션**의 검색 상자에서 사용자 만들기를 입력하고 **통합 시스템 사용자 만들기**를 클릭합니다.

   >[!div class="mx-imgBorder"] 
   >![사용자 만들기](./media/workday-inbound-tutorial/wd_isu_01.png "사용자 만들기")
2. 새 통합 시스템 사용자에 대한 사용자 이름과 암호를 입력하여 **통합 시스템 사용자 만들기** 작업을 완료합니다.  
  
   * 이 사용자는 프로그래밍 방식으로 로그인할 것이므로 **다음 로그인할 때 새 암호 필요** 옵션을 선택하지 않습니다.
   * 사용자의 세션이 너무 빨리 시간 초과되지 않도록 **세션 시간 초과 분**을 기본값인 0으로 둡니다.
   * 통합 시스템 암호가 있는 사용자가 Workday에 로그인하지 못하게 하는 추가 보안 계층을 제공하기 때문에 **UI 세션 허용 안 함** 옵션을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![통합 시스템 사용자 만들기](./media/workday-inbound-tutorial/wd_isu_02.png "통합 시스템 사용자 만들기")

### <a name="creating-an-integration-security-group"></a>통합 보안 그룹 만들기

이 단계에서는 비제한형 또는 제한형 통합 시스템 보안 그룹을 Workday에 만들고 이 그룹에 이전 단계에서 만든 통합 시스템 사용자를 할당합니다.

**보안 그룹을 만들려면**

1. 검색 상자에 보안 그룹 만들기를 입력하고 **보안 그룹 만들기**를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![보안 그룹 만들기](./media/workday-inbound-tutorial/wd_isu_03.png "보안 그룹 만들기")
2. **보안 그룹 만들기** 작업을 완료합니다. 

   * Workday에 있는 두 가지 유형의 보안 그룹은 다음과 같습니다.
     * **비제한형:** 보안 그룹의 모든 구성원이 보안 그룹으로 보호되는 모든 데이터 인스턴스에 액세스할 수 있습니다.
     * **제한형:** 모든 보안 그룹 구성원에게 보안 그룹이 액세스할 수 있는 데이터 인스턴스(행) 하위 세트의 컨텍스트 액세스 권한이 있습니다.
   * 통합에 적합한 보안 그룹 유형을 선택하려면 Workday 통합 파트너에게 문의하세요.
   * 그룹 유형을 알고 있으면 드롭다운에서 **통합 시스템 보안 그룹(비제한형)** 또는 **통합 시스템 보안 그룹(제한형)** 을 선택합니다 **.**

     > [!div class="mx-imgBorder"]
     >![보안 그룹 만들기](./media/workday-inbound-tutorial/wd_isu_04.png "보안 그룹 만들기")

3. 보안 그룹 생성이 완료되면 멤버를 보안 그룹에 할당할 수 있는 페이지가 표시됩니다. 이전 단계에서 만든 새 통합 시스템 사용자를 이 보안 그룹에 추가합니다. ‘제한형’ 보안 그룹을 사용하는 경우 적절한 조직 범위도 선택해야 합니다.

   >[!div class="mx-imgBorder"]
   >![보안 그룹 편집](./media/workday-inbound-tutorial/wd_isu_05.png "보안 그룹 편집")

### <a name="configuring-domain-security-policy-permissions"></a>도메인 보안 정책 권한 구성

이 단계에서는 보안 그룹에 대한 작업자 데이터에 “도메인 보안” 정책 권한을 부여합니다.

**도메인 보안 정책 권한을 구성하려면**

1. 검색 상자에 **도메인 보안 구성**을 입력한 다음, **도메인 보안 구성 보고서** 링크를 클릭합니다.  
   >[!div class="mx-imgBorder"]
   >![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_06.png "도메인 보안 정책")  
2. **도메인** 텍스트 상자에서 다음 도메인을 검색하고 필터에 하나씩 추가합니다.  
   * *외부 계정 프로비저닝*
   * *작업자 데이터: 작업자*
   * *작업자 데이터: 공용 작업자 보고서*
   * *사용자 데이터: 회사 연락처 정보*
   * *작업자 데이터: 모든 위치*
   * *작업자 데이터: 현재 직원 충원 정보*
   * *작업자 데이터: 작업자 프로필 직함*
   * *Workday 계정*
   
     >[!div class="mx-imgBorder"]
     >![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_07.png "도메인 보안 정책")  

     >[!div class="mx-imgBorder"]
     >![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_08.png "도메인 보안 정책") 

     **확인**을 클릭합니다.

3. 표시되는 보고서에서 **외부 계정 프로비저닝** 옆에 나타나는 줄임표(...)를 선택하고 메뉴 옵션 **도메인 -> 보안 정책 권한 편집**을 클릭합니다.
   >[!div class="mx-imgBorder"]
   >![도메인 보안 정책](./media/workday-inbound-tutorial/wd_isu_09.png "도메인 보안 정책")  

4. **도메인 보안 정책 권한 편집** 페이지에서 **통합 권한** 섹션까지 아래로 스크롤합니다. “+” 기호를 클릭하여 **Get** 및 **Put** 통합 권한이 있는 보안 그룹 목록에 통합 시스템 그룹을 추가합니다.
   >[!div class="mx-imgBorder"]
   >![권한 편집](./media/workday-inbound-tutorial/wd_isu_10.png "편집 권한")  

5. “+” 기호를 클릭하여 **Get** 및 **Put** 통합 권한이 있는 보안 그룹 목록에 통합 시스템 그룹을 추가합니다.

   >[!div class="mx-imgBorder"]
   >![권한 편집](./media/workday-inbound-tutorial/wd_isu_11.png "편집 권한")  

6. 이러한 나머지 보안 정책 각각에 대해 위의 3-5단계를 반복합니다.

   | 작업(Operation) | 도메인 보안 정책 |
   | ---------- | ---------- |
   | 가져오기 및 넣기 | 작업자 데이터: 공용 작업자 보고서 |
   | 가져오기 및 넣기 | 사용자 데이터: 회사 연락처 정보 |
   | 가져오기 | 작업자 데이터: 작업자 |
   | 가져오기 | 작업자 데이터: 모든 위치 |
   | 가져오기 | 작업자 데이터: 현재 직원 충원 정보 |
   | 가져오기 | 작업자 데이터: 작업자 프로필 직함 |
   | 가져오기 및 넣기 | Workday 계정 |

### <a name="configuring-business-process-security-policy-permissions"></a>비즈니스 프로세스 보안 정책 권한 구성

이 단계에서는 보안 그룹에 대한 작업자 데이터에 “비즈니스 프로세스 보안” 정책 권한을 부여합니다. 

> [!NOTE]
> 이 단계는 Workday 쓰기 저장 앱 커넥터를 설정 하는 경우에만 필요 합니다.

**비즈니스 프로세스 보안 정책 권한을 구성하려면**

1. 검색 상자에 **비즈니스 프로세스 정책**을 입력한 다음, **비즈니스 프로세스 보안 정책 편집** 작업 링크를 클릭합니다.  

   >[!div class="mx-imgBorder"]
   >![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_12.png "비즈니스 프로세스 보안 정책")  

2. **비즈니스 프로세스 유형** 텍스트 상자에서 *연락처*를 검색하고 **회사 연락처 변경** 비즈니스 프로세스를 선택하고 **확인**을 클릭합니다.

   >[!div class="mx-imgBorder"]
   >![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_13.png "비즈니스 프로세스 보안 정책")  

3. **비즈니스 프로세스 보안 정책 편집** 페이지에서 **회사 연락처 정보 변경(웹 서비스)** 섹션으로 스크롤합니다.
    

4. 새 통합 시스템 보안 그룹을 선택하여 웹 서비스 요청을 시작할 수 있는 보안 그룹 목록에 추가합니다. 

   >[!div class="mx-imgBorder"]
   >![비즈니스 프로세스 보안 정책](./media/workday-inbound-tutorial/wd_isu_15.png "비즈니스 프로세스 보안 정책")  

5. **완료**를 클릭합니다. 

### <a name="activating-security-policy-changes"></a>보안 정책 변경 사항 활성화

**보안 정책 변경 사항을 활성화하려면**

1. 검색 상자에 활성화를 입력하고 **보류 중인 보안 정책 변경 내용 활성화** 링크를 클릭합니다.
   >[!div class="mx-imgBorder"]
   >![활성화](./media/workday-inbound-tutorial/wd_isu_16.png "활성화")

1. 감사 목적에 대한 메모를 입력하고 **확인**을 클릭하여 보류 중인 보안 정책 변경 내용의 활성화 태스크를 시작합니다.
1. 다음 화면에서 **확인** 확인란을 선택하여 태스크를 완료한 다음 **확인**을 클릭합니다.

   >[!div class="mx-imgBorder"]
   >![보류 중인 보안 활성화](./media/workday-inbound-tutorial/wd_isu_18.png "보류 중인 보안 활성화")  

## <a name="configure-active-directory-service-account"></a>Active Directory 서비스 계정 구성

이 섹션에서는 Azure AD Connect 프로 비전 에이전트를 설치 하 고 구성 하는 데 필요한 AD 서비스 계정 권한을 설명 합니다.

### <a name="permissions-required-to-run-the-provisioning-agent-installer"></a>프로 비전 에이전트 설치 관리자를 실행 하는 데 필요한 권한
프로 비전 에이전트를 호스트 하는 Windows Server를 확인 한 후 로컬 관리자 또는 도메인 관리자 자격 증명을 사용 하 여 서버 호스트에 로그인 합니다. 에이전트 설치 프로세스는 보안 키 저장소 자격 증명 파일을 만들고 호스트 서버에서 서비스 프로필 구성을 업데이트 합니다. 이렇게 하려면 에이전트를 호스트 하는 서버에 대 한 관리자 액세스 권한이 필요 합니다. 

### <a name="permissions-required-to-configure-the-provisioning-agent-service"></a>프로 비전 에이전트 서비스를 구성 하는 데 필요한 권한
다음 단계를 사용 하 여 에이전트 작업을 프로 비전 하는 데 사용할 수 있는 서비스 계정을 설정 합니다. 
1.  AD 도메인 컨트롤러에서 *Active Directory 사용자 및 컴퓨터* 스냅인을 엽니다. 
2.  새 도메인 사용자 만들기 (예: *provAgentAdmin*)  
3.  OU 또는 도메인 이름을 마우스 오른쪽 단추로 클릭 하 고 제어 위임 *마법사*를 열 *위임* 을 선택 합니다. 

> [!NOTE] 
> 테스트 목적으로 특정 OU의 사용자만 만들고 읽도록 프로 비전 에이전트를 제한 하려면 테스트를 실행 하는 동안 적절 한 OU 수준에서 컨트롤을 위임 하는 것이 좋습니다.

4. 시작 화면에서 **다음** 을 클릭 합니다. 
5. **사용자 또는 그룹 선택** 화면에서 2 단계에서 만든 도메인 사용자를 추가 합니다. **다음**을 클릭합니다.
   >[!div class="mx-imgBorder"]
   >![화면 추가](./media/workday-inbound-tutorial/delegation-wizard-01.png "화면 추가")

6. **위임할 작업** 화면에서 다음 작업을 선택 합니다. 
   * 사용자 계정 만들기, 삭제 및 관리
   * 모든 사용자 정보 읽기

   >[!div class="mx-imgBorder"]
   >![작업 화면](./media/workday-inbound-tutorial/delegation-wizard-02.png "작업 화면")

7. **다음** 을 클릭 하 고 구성을 **저장** 합니다.


## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday에서 Active Directory로 사용자 프로비전 구성

이 섹션에서는 Workday에서 통합 범위 안의 각 Active Directory 도메인으로 사용자 계정을 프로비전하는 단계를 제공합니다.

* [프로비저닝 커넥터 앱을 추가하고 Provisioning Agent 다운로드](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [온-프레미스 프로비전 에이전트 설치 및 구성](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Workday 및 Active Directory에 대한 연결 구성](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [특성 매핑 구성](#part-4-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1부: 프로비저닝 커넥터 앱을 추가하고 Provisioning Agent 다운로드

**Workday에서 Active Directory로의 프로비전을 구성하려면:**

1. [https://editor.swagger.io](<https://portal.azure.com>) 로 이동합니다.

2. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. **Workday를 검색 하 여 사용자 프로 비전을 Active Directory**하 고 갤러리에서 해당 앱을 추가 합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비저닝**을 선택합니다.

7. **프로비저닝** **모드**를 **자동**으로 변경합니다.

8. 표시된 정보 배너를 클릭하여 Provisioning Agent를 다운로드합니다. 

   >[!div class="mx-imgBorder"]
   >![에이전트 다운로드](./media/workday-inbound-tutorial/pa-download-agent.png "에이전트 다운로드 화면")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2부: 온-프레미스 프로비전 에이전트 설치 및 구성

Active Directory 온-프레미스로 프로비저닝하려면 .NET Framework 4.7.1 이상이 있고 원하는 Active Directory 도메인에 대한 네트워크 액세스 권한이 있는 서버에 Provisioning Agent를 설치해야 합니다.

> [!TIP]
> [여기](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)에 제공된 지침을 사용하여 서버에서 .NET Framework 버전을 확인할 수 있습니다.
> 서버에 .NET 4.7.1 이상이 설치되어 있지 않으면 [여기](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)에서 다운로드할 수 있습니다.  

다운로드한 에이전트 설치 관리자를 서버 호스트로 전송하고 아래에 제시된 단계에 따라 에이전트 구성을 완료합니다.

1. 새 에이전트를 설치하려는 Windows Server에 로그인합니다.

1. Provisioning Agent 설치 관리자를 시작하고 동의한 다음, **설치** 단추를 클릭합니다.

   >[!div class="mx-imgBorder"]
   >![설치 화면](./media/workday-inbound-tutorial/pa_install_screen_1.png "설치 화면")
   
1. 설치가 완료되면 마법사가 시작되고 **Azure AD 연결** 화면이 표시됩니다. **인증** 단추를 클릭하여 Azure AD 인스턴스에 연결합니다.

   >[!div class="mx-imgBorder"]
   >![Azure AD 연결](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD 연결")
   
1. 하이브리드 Id 관리자 자격 증명을 사용 하 여 Azure AD 인스턴스에 인증 합니다.

   >[!div class="mx-imgBorder"]
   >![관리자 인증](./media/workday-inbound-tutorial/pa_install_screen_3.png "관리자 인증")

   > [!NOTE]
   > Azure AD 관리자 자격 증명은 Azure AD 테넌트에 연결하는 데만 사용됩니다. 에이전트는 자격 증명을 서버에 로컬로 저장하지 않습니다.

1. Azure AD의 인증에 성공하면 **Active Directory 연결** 화면이 표시됩니다. 이 단계에서는 AD 도메인 이름을 입력하고 **디렉터리 추가** 단추를 클릭합니다.

   >[!div class="mx-imgBorder"]
   >![디렉터리 추가](./media/workday-inbound-tutorial/pa_install_screen_4.png "디렉터리 추가")
  
1. 이제 AD 도메인에 연결하는 데 필요한 자격 증명을 입력하라는 메시지가 표시됩니다. 동일한 화면에서 **도메인 컨트롤러 우선 순위 선택**을 사용하여 에이전트가 프로비전 요청을 보내는 데 사용할 도메인 컨트롤러를 지정할 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![도메인 자격 증명](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 도메인을 구성한 후 설치 관리자는 구성된 도메인 목록을 표시합니다. 이 화면에서 5 및 6단계를 반복하여 도메인을 더 추가하거나 **다음**을 클릭하여 에이전트 등록을 진행할 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![구성된 도메인](./media/workday-inbound-tutorial/pa_install_screen_6.png "구성된 도메인")

   > [!NOTE]
   > 여러 AD 도메인이 있는 경우(예: na.contoso.com, emea.contoso.com) 각 도메인을 목록에 개별적으로 추가하세요.
   > 부모 도메인(예: contoso.com)만 추가하는 것은 충분하지 않습니다. 각 자식 도메인을 에이전트에 등록해야 합니다.
   
1. 구성 세부 정보를 검토하고 **확인**을 클릭하여 에이전트를 등록합니다.
  
   >[!div class="mx-imgBorder"]
   >![확인 화면](./media/workday-inbound-tutorial/pa_install_screen_7.png "확인 화면")
   
1. 구성 마법사가 에이전트 등록의 진행률을 표시합니다.
  
   >[!div class="mx-imgBorder"]
   >![에이전트 등록](./media/workday-inbound-tutorial/pa_install_screen_8.png "에이전트 등록")
   
1. 에이전트 등록이 완료되면 **종료**를 클릭하여 마법사를 끝낼 수 있습니다.

   >[!div class="mx-imgBorder"]
   >![종료 화면](./media/workday-inbound-tutorial/pa_install_screen_9.png "종료 화면")
   
1. 에이전트 설치를 확인하고 "서비스" 스냅인을 열고 "Microsoft Azure AD Connect Provisioning Agent"라는 서비스를 찾아 해당 에이전트가 실행 중인지 확인합니다.

   >[!div class="mx-imgBorder"]
   >![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>3부: 프로비저닝 앱에서 Workday 및 Active Directory에 대한 연결 구성
이 단계에서는 Azure Portal에서 Workday 및 Active Directory와의 연결을 설정합니다. 

1. Azure Portal에서 [1부](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)에서 만든 Workday-Active Directory User Provisioning App으로 돌아갑니다.

1. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **Workday 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. **username\@tenant_name**과 비슷합니다.

   * **Workday 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **Workday 웹 서비스 API URL –** 테 넌 트의 Workday 웹 서비스 끝점에 대 한 URL을 입력 합니다. URL은 커넥터에서 사용 하는 Workday 웹 서비스 API의 버전을 결정 합니다. 
   
     | URL 형식 | 사용 되는 WWS API 버전 | XPATH 변경 필요 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | 예 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | 예 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # # # | 예 |

      > [!NOTE]
     > URL에 버전 정보가 지정 되지 않은 경우 앱은 WWS (Workday 웹 서비스) v 21.1를 사용 하 고 앱과 함께 제공 되는 기본 XPATH API 식에는 변경이 필요 하지 않습니다. 특정 WWS API 버전을 사용 하려면 URL에서 버전 번호를 지정 합니다. <br>
     > 예: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> WWS API v 30.0 +를 사용 하는 경우 프로 비전 작업을 설정 하기 전에 **특성 매핑-> 고급 옵션-** 구성 및 [Workday 특성 참조](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [관리](#managing-your-configuration) 섹션을 참조 하는 workday에 대 한 > 특성 목록에서 **XPATH API 식을** 업데이트 하세요.  

   * **Active Directory 포리스트 -** 에이전트에 등록된 Active Directory 도메인의 “이름”입니다. 드롭다운에서 프로비전을 위한 대상 도메인을 선택합니다. 이 값은 일반적으로 *contoso.com* 형태의 문자열입니다.

   * **Active Directory 컨테이너 -** 에이전트가 기본적으로 사용자 계정을 만드는 컨테이너 DN을 입력합니다.
        예제: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
        
     > [!NOTE]
     > 이 설정은 *parentDistinguishedName* 특성이 특성 매핑에 구성되지 않은 경우에만 사용자 계정 생성에 사용됩니다. 이 설정은 사용자 검색 또는 업데이트 작업에는 사용되지 않습니다. 전체 도메인 하위 트리는 검색 작업의 범위에 속합니다.

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

     > [!NOTE]
     > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패한 경우 에이전트 설치 시 구성된 Workday 자격 증명과 AD 자격 증명이 유효한지 재차 확인하세요.

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * 자격 증명이 저장되면 **매핑** 섹션에 기본 매핑인 **Synchronize Workday Workers to On Premises Active Directory**(온-프레미스 Active Directory에 Workday 작업자 동기화)가 표시됩니다.

### <a name="part-4-configure-attribute-mappings"></a>4부. 특성 매핑 구성

이 섹션에서는 사용자 데이터가 Workday에서 Active Directory로 흐르는 방식을 구성하겠습니다.

1. **매핑** 아래 [프로비전] 탭에서 **Synchronize Workday Workers to On Premises Active Directory**(온-프레미스 Active Directory에 Workday 작업자 동기화)를 클릭합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 세트를 정의하여 AD 프로비전 범위에 포함할 Workday 사용자 세트를 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예제: 작업자 ID가 1000000-2000000(2000000 제외) 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음

      * 특성: EmployeeID

      * 연산자: NULL이 아님

   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. Workday의 몇몇 테스트 사용자로 매핑을 테스트하려면 **원본 개체 범위** 아래에 있는 범위 지정 필터를 사용하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

   > [!CAUTION] 
   > 프로비저닝 엔진의 기본 동작은 범위를 벗어나는 사용자를 사용하지 않도록 설정/삭제하는 것입니다. 이는 Workday-AD 통합 환경에서 적합하지 않을 수 있습니다. 이 기본 동작을 재정의하려면 범위를 [벗어난 사용자 계정 삭제 건너뛰기](../app-provisioning/skip-out-of-scope-deletions.md) 문서를 참조하세요.
  
1. **대상 개체 작업** 필드에서 Active Directory에서 수행할 작업을 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

1. **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

1. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../app-provisioning/functions-for-customizing-application-data.md).

      * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)을 참조하세요.

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active Directory의 사용자 특성입니다.

      * **이 특성을 사용하여 개체 일치** – Workday와 Active Directory 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 이 값은 일반적으로 Active Directory의 직원 ID 특성 중 하나에 매핑되는 Workday의 작업자 ID 필드에서 설정됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**

         * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

1. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.
   >[!div class="mx-imgBorder"]
   >![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>아래는 몇 가지 일반적인 식을 사용한 Workday와 Active Directory 간의 특성 매핑을 보여주는 예입니다.

* *parentDistinguishedName* 특성에 매핑되는 식을 사용하여 하나 이상의 Workday 원본 특성에 따라 다른 OU에 사용자를 프로비전할 수 있습니다. 이 예제에서는 도시에 따라 사용자를 다른 OU에 배치합니다.

* Active Directory의 *userPrincipalName* 특성은 대상 AD 도메인에 생성된 값이 있는지 확인하고 고유한 경우에만 해당 값을 설정하는 중복 제거 함수 [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)를 사용하여 생성됩니다.  

* [식 작성에 대한 설명서는 여기에 있습니다](../app-provisioning/functions-for-customizing-application-data.md). 이 섹션에는 특수 문자를 제거하는 방법에 대한 예제가 포함되어 있습니다.

| WORKDAY 특성 | ACTIVE DIRECTORY 특성 |  ID 일치 여부 | 만들기/업데이트 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **예** | 만들기 작업 시에만 기록 |
| **PreferredNameData**    |  cn    |   |   만들기 작업 시에만 기록 |
| **SelectUniqueValue( Join("\@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | 만들기 작업 시에만 기록 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         만들기 작업 시에만 기록 |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 만들기 + 업데이트 |
| **FirstName**   | givenName       |     |    만들기 + 업데이트 |
| **LastName**   |   sn   |     |  만들기 + 업데이트 |
| **PreferredNameData**  |  displayName |     |   만들기 + 업데이트 |
| **회사**         | company   |     |  만들기 + 업데이트 |
| **SupervisoryOrganization**  | department  |     |  만들기 + 업데이트 |
| **ManagerReference**   | manager  |     |  만들기 + 업데이트 |
| **BusinessTitle**   |  title     |     |  만들기 + 업데이트 | 
| **AddressLineData**    |  streetAddress  |     |   만들기 + 업데이트 |
| **Municipality**   |   l   |     | 만들기 + 업데이트 |
| **CountryReferenceTwoLetter**      |   co |     |   만들기 + 업데이트 |
| **CountryReferenceTwoLetter**    |  c  |     |         만들기 + 업데이트 |
| **CountryRegionReference** |  st     |     | 만들기 + 업데이트 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  만들기 + 업데이트 |
| **PostalCode**  |   postalCode  |     | 만들기 + 업데이트 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 만들기 + 업데이트 |
| **Fax**      | facsimileTelephoneNumber     |     |    만들기 + 업데이트 |
| **Mobile**  |    mobile       |     |       만들기 + 업데이트 |
| **LocalReference** |  preferredLanguage  |     |  만들기 + 업데이트 |                                               
| **Switch ( \[ 지방 자치 체 \] , "OU = Default USERS, DC = CONTOSO, dc = com", "달라스", "Ou = 달라스, Ou = USERS, dc = CONTOSO, dc = com", "오스틴", "Ou = 오스틴, Ou = USERS, dc = CONTOSO, DC = com", "시애틀", "Ou = 시애틀, Ou = USERS, dc = CONTOSO, dc = com", "런던", "Ou = 런던, Ou = USERS, dc = CONTOSO, dc = com")**  | parentDistinguishedName     |     |  만들기 + 업데이트 |

특성 매핑 구성이 완료되면 이제 [사용자 프로비저닝 서비스를 사용하도록 설정하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

Workday 프로비전 앱 구성이 완료되면 Azure Portal에서 프로비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업을 수행하면 초기 동기화가 시작되고, Workday 테넌트에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 다음, Active Directory에 추가 또는 업데이트되는지와 같은 프로비저닝 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. 감사 로그를 검토하고 프로비저닝 오류를 수정하는 방법에 대한 지침은 문제 해결 섹션을 참조하세요.

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.
   > [!div class="mx-imgBorder"]
   > ![프로 비전 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)



## <a name="frequently-asked-questions-faq"></a>FAQ(질문과 대답)

* **솔루션 기능 질문**
  * [Workday에서 신규 채용자를 처리할 때 솔루션에서 Active Directory에서 새 사용자 계정의 암호를 어떻게 설정하나요?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [솔루션에서 프로비전 작업이 완료된 후 메일 알림 보내기를 지원하나요?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [신규 채용자의 암호 전송을 관리하고 해당 암호를 재설정하는 메커니즘을 안전하게 제공하려면 어떻게 할까요?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [솔루션에서는 Azure AD 클라우드 또는 프로비전 에이전트 계층에서 Workday 사용자 프로필을 캐시하나요?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [솔루션에서 온-프레미스 AD 그룹을 사용자에게 할당할 수 있나요?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [솔루션에서 Workday 작업자 프로필을 쿼리 및 업데이트하는 데 사용하는 Workday API는 무엇인가요?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [두 Azure AD 테넌트를 사용하여 Workday HCM 테넌트를 구성할 수 있나요?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Azure AD Connect를 배포한 경우 “Workday-Azure AD” 사용자 프로비저닝 앱이 지원되지 않는 이유는 무엇인가요?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Workday 및 Azure AD 통합에 관련된 개선 사항을 제안하거나 새로운 기능을 요청하려면 어떻게 할까요?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **프로비전 에이전트 질문**
  * [프로비전 에이전트의 GA 버전은 무엇인가요?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [내 프로비전 에이전트의 버전을 어떻게 알 수 있나요?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft에서 프로비전 에이전트 업데이트를 자동으로 푸시하나요?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Azure AD Connect를 실행하는 동일한 서버에 Provisioning Agent를 설치할 수 있나요?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [아웃바운드 HTTP 통신에 프록시 서버를 사용하도록 프로비전 에이전트를 구성하려면 어떻게 할까요?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [프로비전 에이전트가 Azure AD 테넌트와 통신할 수 있고 에이전트에 필요한 포트를 차단하는 방화벽이 없는지 확인하려면 어떻게 할까요?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [내 프로비전 에이전트와 연결된 도메인의 등록을 해제하려면 어떻게 할까요?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [프로비전 에이전트를 제거하려면 어떻게 할까요?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Workday-AD 특성 매핑 및 구성 질문**
  * [내 Workday 프로비전 특성 매핑 및 스키마의 작업 복사본을 백업하거나 내보내려면 어떻게 할까요?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Workday 및 Active Directory에 사용자 지정 특성이 있습니다. 사용자 지정 특성을 사용하도록 솔루션을 구성하려면 어떻게 할까요?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [사용자의 사진을 Workday에서 Active Directory로 프로비전할 수 있나요?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [공개 사용을 위해 사용자 동의에 따라 Workday에서 휴대폰 번호를 동기화하려면 어떻게 할까요?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [사용자의 부서/국가/시/도 특성에 따라 AD에서 표시 이름의 서식을 지정하고 지역별 차이를 처리하려면 어떻게 해야 할까요?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [SelectUniqueValue를 사용하여 samAccountName 특성의 고유 값을 생성하려면 어떻게 할까요?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [분음 부호가 있는 문자를 제거하고 일반 영어 알파벳으로 변환하려면 어떻게 할까요?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>솔루션 기능 질문

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Workday에서 신규 채용자를 처리할 때 솔루션에서 Active Directory에서 새 사용자 계정의 암호를 어떻게 설정하나요?

온-프레미스 프로비전 에이전트는 새 AD 계정을 만드는 요청을 받으면 AD 서버에서 정의한 암호 복잡성 요구 사항을 충족하도록 설계된 복잡한 임의 암호를 자동으로 생성하고 사용자 개체에서 이 암호를 설정합니다. 이 암호는 아무 위치에도 기록되지 않습니다.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>솔루션에서 프로비전 작업이 완료된 후 메일 알림 보내기를 지원하나요?

아니요. 프로비전 작업을 완료한 후 메일 알림 보내기는 현재 릴리스에서 지원되지 않습니다.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>신규 채용자의 암호 전송을 관리하고 해당 암호를 재설정하는 메커니즘을 안전하게 제공하려면 어떻게 할까요?

새 AD 계정 프로비저닝에 포함된 마지막 단계 중 하나는 사용자의 AD 계정에 할당된 임시 암호를 전송하는 것입니다. 많은 기업에서 사용자의 관리자에게 임시 암호를 전달한 다음, 해당 관리자가 신규 채용자/임시 고용 작업자에게 암호를 전달하는 기존 접근 방식을 사용하고 있습니다. 이 프로세스에는 고유한 보안 결함이 있으며 Azure AD 기능을 사용하여 더 나은 접근 방식을 구현할 수 있는 옵션이 있습니다.

HR 팀은 채용 프로세스의 일부로 일반적으로 신원 조사를 실행하고 신규 채용자의 휴대폰 번호를 조사합니다. Workday-AD 사용자 프로비저닝 통합을 사용하면 이 사실을 기반으로 빌드하고 첫날에 사용자의 셀프 서비스 암호 재설정 기능을 출시할 수 있습니다. 이 작업을 수행하려면 Azure AD Connect를 사용하여 신규 채용자의 "휴대폰 번호" 특성을 Workday에서 AD로 전파한 후 AD에서 Azure AD로 전파합니다. Azure AD에 "휴대폰 번호"가 있으면 사용자 계정의 [SSPR(셀프 서비스 암호 재설정)](../authentication/howto-sspr-authenticationdata.md)을 사용하도록 설정하여 첫날에 신규 채용자가 등록되고 확인된 휴대폰 번호를 인증에 사용할 수 있습니다.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>솔루션에서는 Azure AD 클라우드 또는 프로비전 에이전트 계층에서 Workday 사용자 프로필을 캐시하나요?

아니요. 솔루션은 사용자 프로필의 캐시를 유지하지 않습니다. Azure AD 프로비전 서비스는 단순히 Workday에서 데이터를 읽고 대상 Active Directory 또는 Azure AD에 쓰는 데이터 프로세서로 사용됩니다. 사용자 개인 정보 및 데이터 보존에 대한 자세한 내용은 [개인 데이터 관리](#managing-personal-data) 섹션을 참조하세요.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>솔루션에서 온-프레미스 AD 그룹을 사용자에게 할당할 수 있나요?

이 기능은 현재 지원되지 않습니다. 이를 해결하려면 [감사 로그 데이터](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta)에 대한 Microsoft Graph API 엔드포인트를 쿼리하는 PowerShell 스크립트를 배포하고 이 스크립트를 사용하여 그룹 할당과 같은 시나리오를 트리거하는 것이 좋습니다. 이 PowerShell 스크립트는 작업 스케줄러에 연결되고 프로비전 에이전트를 실행하는 동일한 상자에 배포될 수 있습니다.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>솔루션에서 Workday 작업자 프로필을 쿼리 및 업데이트하는 데 사용하는 Workday API는 무엇인가요?

현재 솔루션에서는 다음 Workday API를 사용합니다.

* **관리자 자격 증명** 섹션에서 사용된 **Workday Web Services API URL** 형식에 따라 Get_Workers에 사용되는 API 버전이 달라집니다.
  * URL 형식이 https://\#\#\#\#\.workday\.com/ccx/service/tenantName인 경우 API v21.1이 사용됩니다. 
  * URL 형식이 https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources인 경우 API v21.1이 사용됩니다. 
  * URL 형식이 https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources/v\#\#\.\#인 경우 지정된 API 버전이 사용됩니다. (예: v34.0이 지정된 경우 해당 버전이 사용됩니다.)  
   
* Workday 이메일 쓰기 저장 기능은 Change_Work_Contact_Information(v30.0)을 사용합니다. 
* Workday 사용자 이름 쓰기 저장 기능은 Update_Workday_Account(v31.2)를 사용합니다. 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>두 Azure AD 테넌트를 사용하여 Workday HCM 테넌트를 구성할 수 있나요?

예. 이 구성은 지원됩니다. 이 시나리오를 구성하는 개략적인 단계는 다음과 같습니다.

* 프로비전 에이전트 #1을 배포하고 Azure AD 테넌트 #1에 등록합니다.
* 프로비전 에이전트 #2를 배포하고 Azure AD 테넌트 #2에 등록합니다.
* 각 프로비전 에이전트가 관리할 “자식 도메인”에 따라 도메인을 사용하여 각 에이전트를 구성합니다. 하나의 에이전트가 여러 도메인을 처리할 수 있습니다.
* Azure Portal을 통해 각 테넌트에서 Workday-AD 사용자 프로비저닝 앱을 설정하고 각 도메인을 사용하여 앱을 구성합니다.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Azure AD Connect를 배포한 경우 “Workday-Azure AD” 사용자 프로비저닝 앱이 지원되지 않는 이유는 무엇인가요?

Azure AD를 하이브리드 모드(클라우드 + 온-프레미스 사용자가 함께 포함된 모드)에서 사용하는 경우 “인증 원본”의 명확한 정의가 있어야 합니다. 일반적으로 하이브리드 시나리오의 경우 Azure AD Connect를 배포해야 합니다. Azure AD Connect가 배포된 경우에는 온-프레미스 AD가 인증 원본입니다. Workday-Azure AD 커넥터를 함께 도입하면 Workday 특성 값이 Azure AD Connect에서 설정한 값을 덮어쓸 수 있는 상황이 발생할 수 있습니다. 이런 이유로 Azure AD Connect가 사용하도록 설정된 경우 “Workday-Azure AD” 프로비전 앱 사용이 지원되지 않습니다. 이 상황에 발생하면 사용자를 온-프레미스 AD로 가져온 후 Azure AD Connect를 사용하여 Azure AD에 동기화하는 데 “Workday-AD 사용자” 프로비저닝 앱을 사용하는 것이 좋습니다.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Workday 및 Azure AD 통합에 관련된 개선 사항을 제안하거나 새로운 기능을 요청하려면 어떻게 할까요?

사용자 의견은 향후 릴리스 및 개선 사항의 방향을 설정하는 데 도움이 되므로 매우 중요합니다. 의견이 있으시면 언제든 보내주세요. [Azure AD 사용자 의견 포럼](https://feedback.azure.com/forums/169401-azure-active-directory)에서 아이디어나 개선 사항 제안을 제출하실 것을 권장합니다. Workday 통합에 관련된 특정 사용자 의견을 보려면 ‘SaaS 애플리케이션’ 범주를 선택하고 *Workday* 키워드로 검색하여 Workday와 관련된 기존 사용자 의견을 찾습니다.

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS 앱](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

새로운 아이디어를 제안할 경우 다른 사용자가 유사한 기능을 이미 제안했는지 확인하세요. 이 경우 기능 또는 개선 사항 요청을 추천할 수 있습니다. 특정 사용 사례에 관한 댓글을 남겨서 해당 아이디어를 지지함을 나타내고 해당 기능이 얼마나 중요한지 보여 줄 수도 있습니다.

### <a name="provisioning-agent-questions"></a>프로비전 에이전트 질문

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>프로비전 에이전트의 GA 버전은 무엇인가요?

프로 비전 에이전트의 최신 GA 버전에 대 한 [Azure AD Connect 프로 비전 에이전트: 버전 릴리스 기록](../app-provisioning/provisioning-agent-release-version-history.md) 을 참조 하세요.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>내 프로비전 에이전트의 버전을 어떻게 알 수 있나요?

* Provisioning Agent가 설치된 Windows Server에 로그인합니다.
* **제어판** -> **프로그램 제거/변경** 메뉴로 이동
* **Microsoft Azure AD Connect 프로비전 에이전트** 항목에 해당하는 버전 찾기

  >[!div class="mx-imgBorder"]
  >![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft에서 프로비전 에이전트 업데이트를 자동으로 푸시하나요?

예, Windows 서비스 **Microsoft Azure AD Connect 에이전트 업데이트** 프로그램이 실행 되 고 있으면 Microsoft에서 프로 비전 에이전트를 자동으로 업데이트 합니다.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Azure AD Connect를 실행하는 동일한 서버에 Provisioning Agent를 설치할 수 있나요?

예. Azure AD Connect를 실행하는 동일한 서버에 Provisioning Agent를 설치할 수 있습니다.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>구성할 때 프로비전 에이전트가 Azure AD 관리자 자격 증명을 요구하는 메시지를 표시합니다. 에이전트가 자격 증명을 서버에 로컬로 저장하나요?

구성 중에 프로비전 에이전트는 Azure AD 테넌트에 연결하기 위해서만 Azure AD 관리자 자격 증명을 요구하는 메시지를 표시합니다. 자격 증명을 서버에 로컬로 저장하지 않습니다. 그러나 ‘온-프레미스 Active Directory 도메인’에 연결하는 데 사용되는 자격 증명을 로컬 Windows 암호 자격 증명 모음에 보존합니다.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>아웃바운드 HTTP 통신에 프록시 서버를 사용하도록 프로비전 에이전트를 구성하려면 어떻게 할까요?

프로비전 에이전트는 아웃바운드 프록시 사용을 지원합니다. 에이전트 구성 파일 **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**를 편집하여 에이전트를 구성할 수 있습니다. 해당 구성 파일의 끝 쪽으로 닫는 `</configuration>` 태그 바로 앞에 다음 줄을 추가합니다.
[proxy-server] 및 [proxy-port] 변수를 프록시 서버 이름 및 포트 값으로 바꿉니다.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>프로비전 에이전트가 Azure AD 테넌트와 통신할 수 있고 에이전트에 필요한 포트를 차단하는 방화벽이 없는지 확인하려면 어떻게 할까요?

[필요한 모든 포트가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports) 열려 있는지 여부도 확인할 수 있습니다.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>단일 프로비전 에이전트가 여러 AD 도메인을 프로비전하도록 구성할 수 있나요?

예. 에이전트가 각 도메인 컨트롤러를 확인할 수 있는 경우 단일 프로비전 에이전트가 여러 AD 도메인을 처리하도록 구성할 수 있습니다. 고가용성을 보장하고 장애 조치(failover) 지원을 제공하기 위해 동일한 AD 도메인 세트를 제공하는 3개의 프로비전 에이전트로 구성된 그룹을 설정하는 것이 좋습니다.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>내 프로비전 에이전트와 연결된 도메인의 등록을 해제하려면 어떻게 할까요?

* Azure Portal에서 Azure AD 테넌트의 ‘테넌트 ID’를 확인합니다.
* Provisioning Agent를 실행하는 Windows Server에 로그인합니다.
* Windows 관리자 권한으로 PowerShell을 엽니다.
* 등록 스크립트를 포함하는 디렉터리로 변경하고 다음 명령을 실행하여 \[tenant ID\] 매개 변수를 테넌트 ID 값으로 바꿉니다.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* 표시되는 에이전트 목록에서 *resourceName*이 AD 도메인 이름과 같은 해당 리소스의 `id` 필드 값을 복사합니다.
* ID 값을 이 명령에 붙여넣고 PowerShell에서 실행합니다.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* 에이전트 구성 마법사를 다시 실행합니다.
* 이전에 이 도메인에 할당된 다른 에이전트를 다시 구성해야 합니다.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>프로비전 에이전트를 제거하려면 어떻게 할까요?

* Provisioning Agent가 설치된 Windows Server에 로그인합니다.
* **제어판** -> **프로그램 제거/변경** 메뉴로 이동
* 다음 프로그램을 제거합니다.
  * Microsoft Azure AD Connect 프로비전 에이전트
  * Microsoft Azure AD Connect 에이전트 업데이트
  * Microsoft Azure AD Connect 프로비전 에이전트 패키지

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Workday-AD 특성 매핑 및 구성 질문

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>내 Workday 프로비전 특성 매핑 및 스키마의 작업 복사본을 백업하거나 내보내려면 어떻게 할까요?

Microsoft Graph API를 사용하여 Workday 사용자 프로비저닝 구성을 내보낼 수 있습니다. 자세한 내용은 [Workday 사용자 프로비저닝 특성 매핑 구성 내보내기 및 가져오기](#exporting-and-importing-your-configuration) 섹션의 단계를 참조하세요.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Workday 및 Active Directory에 사용자 지정 특성이 있습니다. 사용자 지정 특성을 사용하도록 솔루션을 구성하려면 어떻게 할까요?

솔루션은 사용자 지정 Workday 및 Active Directory 특성을 지원합니다. 매핑 스키마에 사용자 지정 특성을 추가하려면 **특성 매핑** 블레이드를 열고 아래로 스크롤하여 **고급 옵션 표시** 섹션을 확장합니다. 

![특성 목록 편집](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

사용자 지정 Workday 특성을 추가하려면 ‘Workday의 특성 목록 편집’ 옵션을 선택하고, 사용자 지정 AD 특성을 추가하려면 ‘온-프레미스 Active Directory의 특성 목록 편집’ 옵션을 선택합니다. 

참고 항목:

* [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Workday 변경 사항에 따라 AD의 특성만 업데이트하고 새 AD 계정을 만들지 않도록 솔루션을 구성하려면 어떻게 할까요?

다음과 같이 **특성 매핑** 블레이드에서 **대상 개체 작업**을 설정하여 이 구성을 수행할 수 있습니다.

![업데이트 동작](./media/workday-inbound-tutorial/wd_target_update_only.png)

업데이트 작업만 Workday에서 AD로 흐르도록 “업데이트” 확인란을 선택합니다. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>사용자의 사진을 Workday에서 Active Directory로 프로비전할 수 있나요?

현재 솔루션은 Active Directory에서 *thumbnailPhoto* 및 *jpegPhoto*와 같은 이진 특성 설정을 지원하지 않습니다.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>공개 사용을 위해 사용자 동의에 따라 Workday에서 휴대폰 번호를 동기화하려면 어떻게 할까요?

* Workday 프로비전 앱의 “프로비전” 블레이드로 이동합니다.
* 특성 매핑을 클릭합니다. 
* **매핑** 아래에서 **Synchronize Workday Workers to On Premises Active Directory**(온-프레미스 Active Directory에 Workday 작업자 동기화) 또는 **Synchronize Workday Workers to Azure AD**(Azure AD에 Workday 작업자 동기화)를 선택합니다.
* 특성 매핑 페이지에서 아래로 스크롤하여 “고급 옵션 표시” 상자를 선택합니다.  **Workday의 특성 목록 편집**을 클릭합니다.
* 열리는 블레이드에서 "Mobile" 특성을 찾고 행을 클릭하여 **API 식** ![모바일 GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)을 편집할 수 있습니다.

* **API 식**을 다음 새 식으로 바꿉니다. 이 식은 Workday에서 “공개 사용 플래그”가 “True”로 설정된 경우에만 회사 휴대폰 번호를 검색합니다.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* 특성 목록을 저장합니다.
* 특성 매핑을 저장합니다.
* 현재 상태를 지우고 전체 동기화를 다시 시작합니다.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>사용자의 부서/국가/시/도 특성에 따라 AD에서 표시 이름의 서식을 지정하고 지역별 차이를 처리하려면 어떻게 해야 할까요?

사용자의 부서 및 국가/지역에 대한 정보도 제공하도록 AD에서 *displayName* 특성을 구성하는 것은 일반적인 요구 사항입니다. 예를 들어 John Smith가 미국의 마케팅 부서에서 일하는 경우 *displayName*을 *Smith, John(Marketing-US)* 으로 표시할 수 있습니다.

회사, 사업부, 도시 또는 국가/지역과 같은 특성을 포함하도록 *CN* 또는 *displayName*을 생성하기 위해 해당 요구 사항을 처리하는 방법은 다음과 같습니다.

* 각 Workday 특성은 **특성 매핑 -> 고급 섹션 -> Workday의 특성 목록 편집**에서 구성할 수 있는 기본 XPATH API 식을 사용하여 검색합니다. Workday *PreferredFirstName*, *PreferredLastName*, *Company* 및 *SupervisoryOrganization* 특성의 기본 XPATH API 식은 다음과 같습니다.

     | Workday 특성 | API XPATH 식 |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | 회사 | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   위의 API 식이 Workday 테넌트 구성에 유효한지 Workday 팀에 문의하세요. 필요한 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes) 섹션의 설명대로 편집할 수 있습니다.

* 마찬가지로, Workday에 있는 국가/지역 정보는 다음 XPATH를 사용하여 검색합니다. *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Workday 특성 목록 섹션에서 사용할 수 있는 5개의 국가/지역 관련 특성이 있습니다.

     | Workday 특성 | API XPATH 식 |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  위의 API 식이 Workday 테넌트 구성에 유효한지 Workday 팀에 문의하세요. 필요한 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes) 섹션의 설명대로 편집할 수 있습니다.

* 적절한 특성 매핑 식을 빌드하려면 사용자의 이름, 성, 국가/지역 및 부서를 "정식으로" 나타내는 Workday 특성을 식별합니다. 특성이 각각 *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* 및 *SupervisoryOrganization*이라고 가정하겠습니다. 이 특성을 사용하여 *Smith, John(Marketing-US)* 과 같은 표시 이름을 가져오는 AD *displayName* 특성 식을 다음과 같이 빌드할 수 있습니다.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    적절한 식을 빌드한 후 Attribute Mappings 테이블을 편집하고 *displayName* 특성 매핑을 다음과 같이 수정합니다.   ![DisplayName 매핑](./media/workday-inbound-tutorial/wd_displayname_map.png)

* 위의 예제를 확장하여 Workday에서 가져온 도시 이름을 약어 값으로 변환한 다음, 이 값을 사용하여 *Smith, John(CHI)* 또는 *Doe, Jane(NYC)* 과 같은 표시 이름을 빌드한다고 가정하겠습니다. 이러한 결과는 Workday *Municipality* 특성이 결정자 변수로 포함된 Switch 식을 사용하여 얻을 수 있습니다.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    참고 항목:
  * [Switch 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Join 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Append 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>SelectUniqueValue를 사용하여 samAccountName 특성의 고유 값을 생성하려면 어떻게 할까요?

Workday에서 *FirstName* 및 *LastName* 특성을 함께 사용하여 *samAccountName* 특성의 고유 값을 생성한다고 가정하겠습니다. 시작할 수 있는 식은 다음과 같습니다.

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

위 식의 작동 방식은 다음과 같습니다. 사용자가 John Smith면 먼저 JSmith를 생성하려고 합니다. JSmith가 이미 있으면 JoSmith를 생성하고, JoSmith가 있으면 JohSmith를 생성합니다. 이 식을 사용하면 생성된 값이 *samAccountName*과 연결된 길이 제한 및 특수 문자 제한도 충족할 수 있습니다.

참고 항목:

* [Mid 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Replace 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue 함수 구문](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>분음 부호가 있는 문자를 제거하고 일반 영어 알파벳으로 변환하려면 어떻게 할까요?

사용자의 메일 주소 또는 CN 값을 생성하는 동안 [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) 함수를 사용하여 사용자의 이름 및 성에서 특수 문자를 제거합니다.

## <a name="troubleshooting-tips"></a>문제 해결 팁

이 섹션에서는 Azure AD 감사 로그 및 Windows Server 이벤트 뷰어 로그를 사용하여 Workday 통합의 프로비전 문제를 해결하는 방법에 대한 구체적인 지침을 제공합니다. 이 지침은 [자습서: 자동 사용자 계정 프로비전에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)에 캡처된 일반적인 문제 해결 단계 및 개념을 기반으로 작성됩니다.

이 섹션에서는 문제 해결의 다음 측면을 다룹니다.

* [에이전트 문제 해결을 위한 Windows 이벤트 뷰어 설정](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [서비스 문제 해결을 위한 Azure Portal 감사 로그 설정](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [AD 사용자 계정 생성 작업에 대한 로그 이해](#understanding-logs-for-ad-user-account-create-operations)
* [관리자 업데이트 작업에 대한 로그 이해](#understanding-logs-for-manager-update-operations)
* [일반적으로 발생하는 오류 해결](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>에이전트 문제 해결을 위한 Windows 이벤트 뷰어 설정

* Provisioning Agent가 배포된 Windows Server 머신에 로그인
* **Windows Server 이벤트 뷰어** 데스크톱 앱을 엽니다.
* **Windows 로그 &gt; 애플리케이션**을 선택합니다.
* **현재 로그 필터링...** 옵션을 사용하여 **AAD.Connect.ProvisioningAgent** 원본 아래에서 기록된 모든 이벤트를 보고 다음과 같이 필터 “-5”를 지정하여 이벤트 ID “5”가 포함된 이벤트를 제외합니다.

  ![Windows 이벤트 뷰어](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* **확인**을 클릭하고 **날짜 및 시간** 열을 기준으로 결과 보기를 정렬합니다.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>서비스 문제 해결을 위한 Azure Portal 감사 로그 설정

* [Azure Portal](https://portal.azure.com)을 시작하고 Workday 프로비전 애플리케이션의 **감사 로그** 섹션으로 이동합니다.
* 감사 로그 페이지의 **열** 단추를 사용하여 보기에 날짜, 활동, 상태, 상태 이유 열만 표시합니다. 이 구성은 문제 해결에 관련된 데이터에만 초점을 맞추도록 합니다.

  ![감사 로그 열](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* **대상** 및 **날짜 범위** 쿼리 매개 변수를 사용하여 보기를 필터링합니다. 
  * **대상** 쿼리 매개 변수를 Workday 작업자 개체의 “작업자 ID” 또는 “직원 ID”로 설정합니다.
  * 프로비저닝 관련 오류 또는 문제를 조사하려는 적절한 기간으로 **날짜 범위**를 설정합니다.

  ![감사 로그 필터](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>AD 사용자 계정 생성 작업에 대한 로그 이해

Workday의 신규 채용자가 검색되면(직원 ID가 *21023*이라고 가정) Azure AD 프로비전 서비스는 작업자의 새 AD 사용자 계정을 만들려고 시도하고 그 과정에서 아래 설명된 대로 4개 감사 로그 레코드를 만듭니다.

  [![감사 로그 만들기 작업](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

감사 로그 레코드를 클릭하면 **활동 세부 정보** 페이지가 열립니다. 각 로그 레코드 유형에 대해 **활동 세부 정보** 페이지에 표시되는 내용은 다음과 같습니다.

* **Workday 가져오기** 레코드: 이 로그 레코드는 Workday에서 페치된 작업자 정보를 표시합니다. 로그 레코드의 ‘추가 세부 정보’ 섹션에 있는 정보를 사용하여 Workday의 데이터 페치 관련 문제를 해결합니다. 각 필드를 해석하는 방법에 대한 예제 레코드가 다음과 같이 포인터와 함께 표시됩니다.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD 가져오기** 레코드: 이 로그 레코드는 AD에서 페치된 계정의 정보를 표시합니다. 초기 사용자를 만드는 동안에는 AD 계정이 없으므로 ‘활동 상태 이유’는 Active Directory에서 일치하는 ID 특성 값을 가진 계정을 찾을 수 없음을 나타냅니다. 로그 레코드의 ‘추가 세부 정보’ 섹션에 있는 정보를 사용하여 Workday의 데이터 페치 관련 문제를 해결합니다. 각 필드를 해석하는 방법에 대한 예제 레코드가 다음과 같이 포인터와 함께 표시됩니다.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  이 AD 가져오기 작업에 해당하는 프로비전 에이전트 로그 레코드를 찾으려면 Windows 이벤트 뷰어 로그를 열고 **찾기...** 메뉴 옵션을 사용하여 일치하는 ID/조인 속성 특성 값(이 경우 *21023*)이 포함된 로그 항목을 찾습니다.

  ![찾기](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  ‘이벤트 ID = 9’인 항목을 찾습니다. 이 항목은 에이전트가 AD 계정을 검색하는 데 사용하는 LDAP 검색 필터를 제공합니다. 이 필터가 고유한 사용자 항목을 검색할 적합한 검색 필터인지 확인할 수 있습니다.

  ![LDAP 검색](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  해당 항목 바로 뒤에 나오는 *Event ID = 2*인 레코드는 검색 작업의 결과와 함께 해당 항목이 결과를 반환했는지 여부를 캡처합니다.

  ![LDAP 결과](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **동기화 규칙 작업** 레코드: 이 로그 레코드는 들어오는 Workday 이벤트를 처리하기 위해 수행할 프로비저닝 작업과 함께 특성 매핑 규칙 및 구성된 범위 지정 필터의 결과를 표시합니다. 로그 레코드의 ‘추가 세부 정보’ 섹션에 있는 정보를 사용하여 동기화 작업 관련 문제를 해결합니다. 각 필드를 해석하는 방법에 대한 예제 레코드가 다음과 같이 포인터와 함께 표시됩니다.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  특성 매핑 식 관련 문제가 있거나 들어오는 Workday 데이터에 문제가 있는 경우(예: 필수 특성의 값이 비어 있거나 null인 경우), 오류 세부 정보를 제공하는 ErrorCode를 사용하여 이 스테이지에서 오류를 관찰합니다.

* **AD 내보내기** 레코드: 이 로그 레코드는 프로세스에서 설정된 특성 값과 함께 AD 계정 만들기 작업의 결과를 표시합니다. 로그 레코드의 ‘추가 세부 정보’ 섹션에 있는 정보를 사용하여 계정 생성 작업 관련 문제를 해결합니다. 각 필드를 해석하는 방법에 대한 예제 레코드가 다음과 같이 포인터와 함께 표시됩니다. "추가 세부 정보" 섹션에서 "EventName"은 "EntryExportAdd"로 설정되고, "JoiningProperty"는 일치하는 ID 특성 값으로 설정되며, "SourceAnchor"는 레코드와 연결된 WID(WorkdayID)로 설정되고, "TargetAnchor"는 새로 만들어진 사용자의 AD "ObjectGuid" 특성 값으로 설정됩니다. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  이 AD 내보내기 작업에 해당하는 프로비전 에이전트 로그 레코드를 찾으려면 Windows 이벤트 뷰어 로그를 열고 **찾기...** 메뉴 옵션을 사용하여 일치하는 ID/조인 속성 특성 값(이 경우 *21023*)이 포함된 로그 항목을 찾습니다.  

  *Event ID = 2*인 내보내기 작업의 타임스탬프에 해당하는 HTTP POST 레코드를 찾습니다. 이 레코드는 프로비전 서비스가 프로비전 에이전트에 보낸 특성 값을 포함합니다.

  [![SCIM 추가](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  위의 이벤트 바로 뒤에는 AD 계정 만들기 작업의 응답을 캡처하는 또 다른 이벤트가 있어야 합니다. 이 이벤트는 AD에 만들어진 새 objectGuid를 반환하고 프로비전 서비스의 TargetAnchor 특성으로 설정됩니다.

  [![SCIM 추가](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>관리자 업데이트 작업에 대한 로그 이해

관리자 특성은 AD의 참조 특성입니다. 프로비전 서비스가 관리자 특성을 사용자 생성 작업의 일부로 설정하지 않습니다. 오히려 관리자 특성은 해당 사용자의 AD 계정이 만들어진 후 ‘업데이트’ 작업의 일부로 설정됩니다. 위의 예제를 확장하여 직원 ID가 "21451"인 신규 채용자가 Workday에서 활성화되고 신규 채용자의 관리자(*21023*)에게 이미 AD 계정이 있다고 가정하겠습니다. 이 시나리오에서 사용자 21451에 해당하는 감사 로그를 검색하면 5개의 항목이 표시됩니다.

  [![관리자 업데이트](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

처음 네 개의 레코드는 사용자 생성 작업의 일부로 검색한 것과 비슷합니다. 5번째 레코드는 관리자 특성 업데이트와 연결된 내보내기입니다. 로그 레코드는 관리자의 *objectGuid* 특성을 사용하여 수행되는 AD 계정 관리자 업데이트 작업의 결과를 표시합니다.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>일반적으로 발생하는 오류 해결

이 섹션에서는 Workday 사용자 프로비저닝에서 일반적으로 나타나는 오류 및 해결 방법을 다룹니다. 오류는 다음과 같이 그룹화됩니다.

* [프로비전 에이전트 오류](#provisioning-agent-errors)
* [연결 오류](#connectivity-errors)
* [AD 사용자 계정 만들기 오류](#ad-user-account-creation-errors)
* [AD 사용자 계정 업데이트 오류](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>프로비전 에이전트 오류

|#|오류 시나리오 |가능한 원인|권장 해결 방법|
|--|---|---|---|
|1.| 다음 오류 메시지와 함께 프로비전 에이전트를 설치하는 중 오류가 발생했습니다.  ‘서비스 ‘Microsoft Azure AD Connect Provisioning Agent’(AADConnectProvisioningAgent)를 시작하지 못했습니다. 시스템을 시작할 권한이 있는지 확인하세요.’ | 이 오류는 일반적으로 도메인 컨트롤러에 프로비전 에이전트를 설치할 때 그룹 정책이 서비스 시작을 차단하는 경우 나타납니다.  이전 버전의 에이전트가 실행 중이고 새 설치를 시작하기 전에 해당 에이전트를 제거하지 않은 경우에도 나타납니다.| DC가 아닌 서버에 프로비전 에이전트를 설치합니다. 새 에이전트를 설치하기 전에 이전 버전의 에이전트가 제거되었는지 확인합니다.|
|2.| Windows 서비스 ‘Microsoft Azure AD Connect Provisioning Agent’가 ‘시작하는 중’ 상태이고 ‘실행 중’ 상태로 전환되지 않습니다.  | 설치 과정에서 에이전트 마법사는 서버에 로컬 계정(**NT 서비스\\AADConnectProvisioningAgent**)을 만듭니다. 이 계정은 서비스를 시작하는 데 사용되는 로그온 계정입니다. Windows Server의 보안 정책이 로컬 계정의 서비스 실행을 차단하면 이 오류가 발생합니다. | ‘서비스 콘솔’을 엽니다. Windows 서비스 ‘Microsoft Azure AD Connect Provisioning Agent’를 마우스 오른쪽 단추로 클릭하고 로그온 탭에서 서비스를 실행할 도메인 관리자의 계정을 지정합니다. 서비스를 다시 시작합니다. |
|3.| ‘Active Directory 연결’ 단계에서 AD 도메인을 사용하여 프로비전 에이전트를 구성할 때 마법사는 AD 스키마를 로드하는 데 시간이 오래 걸리고 결국 시간이 초과됩니다. | 일반적으로 이 오류는 방화벽 문제로 인해 마법사가 AD 도메인 컨트롤러 서버에 연결할 수 없는 경우에 나타납니다. | ‘Active Directory 연결’ 마법사 화면에서 AD 도메인의 자격 증명을 제공하는 동안 ‘도메인 컨트롤러 우선 순위 선택’ 옵션이 제공됩니다.  이 옵션을 사용하여 에이전트 서버와 동일한 사이트에 있는 도메인 컨트롤러를 선택하고 통신을 차단하는 방화벽 규칙이 없는지 확인합니다. |

#### <a name="connectivity-errors"></a>연결 오류

프로비전 서비스가 Workday 또는 Active Directory에 연결할 수 없으면 프로비전이 격리 상태로 전환될 수 있습니다. 아래 표를 사용하여 연결 문제를 해결합니다.

|#|오류 시나리오 |가능한 원인|권장 해결 방법|
|--|---|---|---|
|1.| **연결 테스트**를 클릭하면 오류 메시지가 표시됩니다. ‘Active Directory에 연결하는 중 오류가 발생했습니다. 온-프레미스 프로비전 에이전트가 실행 중이고 올바른 Active Directory 도메인으로 구성되었는지 확인하세요.’ | 일반적으로 이 오류는 프로비전 에이전트가 실행되고 있지 않거나 Azure AD와 프로비전 에이전트 사이의 통신을 차단하는 방화벽이 있는 경우 나타납니다. 도메인이 에이전트 마법사에 구성되지 않은 경우에도 이 오류가 표시될 수 있습니다. | Windows Server에서 ‘서비스’ 콘솔을 열어 에이전트가 실행 중인지 확인합니다. 프로비전 에이전트 마법사를 열고 적합한 도메인이 에이전트에 등록되었는지 확인합니다.  |
|2.| 프로비전 작업이 주말(금-토) 동안 격리 상태로 전환되고 동기화 관련 오류가 있다는 메일 알림을 받게 됩니다. | 이 오류의 일반적인 원인 중 하나는 계획된 Workday 가동 중지 시간입니다. Workday 구현 테넌트를 사용하는 경우 Workday에는 주말(보통 금요일 저녁부터 토요일 아침까지) 동안 구현 테넌트의 가동 중지 시간이 예약되고 해당 기간에는 Workday 프로비전 앱이 격리 상태로 전환되어 Workday에 연결할 수 없다는 점에 유의하세요. Workday 구현 테넌트가 다시 온라인 상태가 되면 정상 상태로 돌아갑니다. 드문 경우지만 통합 시스템 사용자의 암호가 테넌트 새로 고침으로 인해 변경된 경우 또는 계정이 잠김 또는 만료됨 상태인 경우에도 이 오류가 표시될 수 있습니다. | Workday 관리자 또는 통합 파트너에게 문의하여 Workday에서 가동 중지 시간 동안 경고 메시지를 무시하는 가동 중지 시간이 언제 예약되는지 확인하고 Workday 인스턴스가 다시 온라인 상태가 된 후 가용성을 확인합니다.  |


#### <a name="ad-user-account-creation-errors"></a>AD 사용자 계정 만들기 오류

|#|오류 시나리오 |가능한 원인|권장 해결 방법|
|--|---|---|---|
|1.| 다음 메시지와 함께 감사 로그에서 내보내기 작업 오류가 표시되는 경우. ‘오류: OperationsError-SvcErr: 작업 오류가 발생했습니다. 디렉터리 서비스에 상위 참조가 구성되지 않았습니다. 따라서 디렉터리 서비스가 이 포리스트 밖에 있는 개체로 참조할 수 없습니다.’ | 일반적으로 이 오류는 ‘Active Directory Container’ OU가 제대로 설정되지 않은 경우 또는 *parentDistinguishedName*에 사용되는 식 매핑 관련 문제가 있는 경우 나타납니다. | ‘Active Directory Container’ OU 매개 변수에 오타가 있는지 확인합니다. 특성 매핑에서 *parentDistinguishedName*을 사용하는 경우 항상 AD 도메인 내에서 알려진 컨테이너로 평가되는지 확인합니다. 감사 로그의 *내보내기* 이벤트에서 생성된 값을 확인합니다. |
|2.| 다음과 같이 감사 로그에서 내보내기 작업 오류가 표시되는 경우. 오류 코드: *SystemForCrossDomainIdentityManagementBadResponse* 및 메시지 ‘오류: ConstraintViolation-AtrErr: 요청의 값이 잘못되었습니다. 특성 값이 허용 가능한 값 범위에 없습니다. \n오류 세부 정보: CONSTRAINT_ATT_TYPE - company’. | 이 오류는 *company* 특성과 관련되지만, *CN*과 같은 다른 특성의 경우에도 이 오류가 표시될 수 있습니다. 이 오류는 AD 강제 적용 스키마 제약 조건으로 인해 발생합니다. 기본적으로 AD의 *company* 및 *CN* 같은 특성에는 최대 64자를 사용할 수 있습니다. Workday에서 가져오는 값이 64자를 초과하면 이 오류 메시지가 표시됩니다. | 감사 로그에서 *내보내기* 이벤트를 확인하여 오류 메시지에서 보고된 특성의 값을 확인합니다. [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) 함수를 사용하거나 유사한 길이 제약 조건이 없는 AD 특성으로 매핑을 변경하여 Workday에서 가져오는 값을 잘라 보세요.  |

#### <a name="ad-user-account-update-errors"></a>AD 사용자 계정 업데이트 오류

AD 사용자 계정 업데이트 프로세스 중에 프로비전 서비스는 Workday 및 AD에서 정보를 읽고, 특성 매핑 규칙을 실행하고, 변경 사항을 적용해야 하는지 여부를 결정합니다. 이에 따라 업데이트 이벤트가 트리거됩니다. 이러한 단계에서 오류가 발생하면 감사 로그에 기록됩니다. 아래 표를 사용하여 일반적인 업데이트 오류를 해결합니다.

|#|오류 시나리오 |가능한 원인|권장 해결 방법|
|--|---|---|---|
|1.| ‘EventName = EntrySynchronizationError 및 ErrorCode = EndpointUnavailable’ 메시지와 함께 감사 로그에 동기화 규칙 작업 오류가 표시되는 경우 | 이 오류는 온-프레미스 프로비전 에이전트에서 발생한 처리 오류로 인해 프로비전 서비스가 Active Directory에서 사용자 프로필 데이터를 검색할 수 없는 경우 나타납니다. | 프로비저닝 에이전트 이벤트 뷰어 로그에서 읽기 작업(이벤트 ID #2로 필터링) 관련 문제를 나타내는 오류 이벤트를 확인합니다. |
|2.| AD의 특정 사용자에 대한 AD의 관리자 특성이 업데이트되지 않는 경우 | 이 오류는 범위 지정 규칙을 사용 중이고 사용자의 관리자가 범위를 벗어나는 경우 발생할 가능성이 가장 큽니다. 관리자의 일치하는 ID 특성(예: EmployeeID)이 대상 AD 도메인에 없거나 올바른 값으로 설정되지 않은 경우에도 이 문제가 발생할 수 있습니다. | 범위 지정 필터를 검토하고 범위에 관리자 사용자를 추가합니다. AD의 관리자 프로필을 확인하여 일치하는 ID 특성 값이 있는지 확인합니다. |

## <a name="managing-your-configuration"></a>구성 관리

이 섹션에서는 Workday 기반 사용자 프로비저닝 구성을 추가로 확장, 사용자 지정 및 관리하는 방법을 설명합니다. 다음 항목을 설명합니다.

* [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)  
* [구성 내보내기 및 가져오기](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Workday 사용자 특성 목록 사용자 지정

Active Directory 및 Azure AD 둘 다에 대한 Workday 프로비저닝 앱에는 선택할 수 있는 Workday사용자 특성의 기본 목록이 포함되어 있습니다. 그러나 이러한 목록은 포괄적이지 않습니다. Workday는 Workday 테넌트에 고유할 수 있는 수백 개의 가능한 사용자 특성을 지원합니다.

Azure AD 프로비저닝 서비스는 인사 API의[Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 작업에 노출되는 특성을 포함하도록 목록 또는 Workday 특성을 사용자 지정하는 기능을 지원합니다.

이와 같이 변경하려면 [Workday Studio](https://community.workday.com/studio-download)를 사용하여 사용하려는 특성을 나타내는 XPath 식을 추출한 다음, Azure Portal에서 고급 특성 편집기를 사용하여 프로비전 구성에 추가해야 합니다.

**Workday 사용자 특성에 대한 XPath 식을 검색하려면**

1. [Workday Studio](https://community.workday.com/studio-download)를 다운로드하고 설치합니다. 설치 관리자에 액세스하려면 Workday 커뮤니티 계정이 필요합니다.

2. [Workday Web Services Directory](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)에서 사용할 WWS API 버전과 관련된 Workday **Human_Resources** WSDL 파일을 다운로드합니다.

3. Workday Studio를 시작합니다.

4. 명령 모음에서 **Workday > 테스터에서 웹 서비스 테스트** 옵션을 선택합니다.

5. **외부**를 선택하고 2단계에서 다운로드한 Human_Resources WSDL 파일을 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. "IMPL-CC"를 실제 인스턴스 유형으로, "TENANT"를 실제 테넌트 이름으로 바꾸어 **위치** 필드를 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`로 설정합니다.

7. **작업**을 **Get_Workers**로 설정합니다.

8.    요청/응답 창 아래의 작은 **구성** 링크를 클릭하여 Workday 자격 증명을 설정합니다. **인증**을 선택하고 Workday 통합 시스템 계정의 사용자 이름 및 암호를 입력합니다. 사용자 이름 형식을 name\@tenant로 지정하고 **WS-Security UsernameToken** 옵션을 선택된 상태로 둡니다.
   ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. **확인**을 선택합니다.

10. **요청** 창에서 아래의 XML을 붙여넣습니다. **Employee_ID**를 Workday 테넌트에 있는 실제 사용자의 직원 ID로 설정합니다. **wd:version**을 사용하려는 WWS 버전으로 설정합니다. 추출하려는 특성으로 채워진 사용자를 선택합니다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. **요청 보내기**(녹색 화살표)를 클릭하여 명령을 실행합니다. 성공한 경우, **응답** 창에 응답이 표시됩니다. 응답에서 입력한 사용자 ID의 데이터를 확인하여 오류가 없는지 검토합니다.

12. 성공한 경우 **응답** 창의 XML을 복사한 후 XML 파일로 저장합니다.

13. Workday Studio의 명령 모음에서 **파일 > 파일 열기...** 를 선택하고 저장한 XML 파일을 엽니다. 이 작업을 수행하면 Workday Studio XML 편집기에서 파일이 열립니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. 파일 트리에서 **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker**를 탐색하여 사용자의 데이터를 찾습니다.

15. **wd: Worker** 아래에서 추가하려는 특성을 찾은 후 선택합니다.

16. **문서 경로** 필드에서 선택한 특성에 대한 XPath 식을 복사합니다.

17. 복사한 식에서 **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** 접두사를 제거합니다.

18. 복사한 식의 마지막 항목이 노드인 경우(예: “/wd: Birth_Date”) **/text()** 를 식의 끝에 추가합니다. 마지막 항목이 특성(예: “/@wd: type”)인 경우에는 이 작업이 필요하지 않습니다.

19. 결과는 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`와 같아야 합니다. 이 값은 Azure Portal에 복사할 값입니다.

**프로비저닝 구성에 사용자 지정 Workday 사용자 특성을 추가하려면**

1. [Azure Portal](https://portal.azure.com)을 시작하고 이 자습서 앞부분에 설명된 것처럼 Workday 프로비저닝 애플리케이션의 프로비저닝 섹션으로 이동합니다.

2. **프로비전 상태**를 **해제**로 설정하고 **저장**을 선택합니다. 이 단계를 수행하면 준비가 되었을 때만 변경 내용을 적용할 수 있게 됩니다.

3. **매핑** 아래에서 **Synchronize Workday Workers to On Premises Active Directory**(온-프레미스 Active Directory에 Workday 작업자 동기화) 또는 **Synchronize Workday Workers to Azure AD**(Azure AD에 Workday 작업자 동기화)를 선택합니다.

4. 다음 화면 아래로 스크롤하여 **고급 옵션 표시**를 선택합니다.

5. **Workday에 대한 특성 목록 편집**을 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 특성 목록에서 입력 필드가 있는 맨 아래로 스크롤합니다.

7. **이름**으로 해당 특성의 표시 이름을 입력합니다.

8. **형식**에 대해 특성에 해당하는 형식(**문자열**이 가장 일반적임)을 선택합니다.

9. **API 식**에 대해 Workday Studio에서 복사한 XPath 식을 입력합니다. 예: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **특성 추가**를 선택합니다.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 위의 **저장**을 선택하고 대화 상자에서 **예**를 선택합니다. 특성 매핑 화면이 계속 열려 있으면 닫습니다.

12. 주 **프로비전** 탭으로 돌아가 **Synchronize Workday Workers to On Premises Active Directory**(온-프레미스 Active Directory에 Workday 작업자 동기화) 또는 **Synchronize Workers to Azure AD**(Azure AD에 작업자 동기화)를 선택합니다.

13. **새 매핑 추가**를 선택합니다.

14. 이제 새 사용자 특성이 **원본 특성** 목록에 표시됩니다.

15. 필요에 따라 새 사용자 특성에 대한 매핑을 추가합니다.

16. 완료되면 **프로비전 상태**를 다시 **설정**으로 지정하고 저장합니다.

### <a name="exporting-and-importing-your-configuration"></a>구성 내보내기 및 가져오기

[프로비저닝 구성 내보내기 및 가져오기](../app-provisioning/export-import-provisioning-configuration.md) 문서를 참조하세요.

## <a name="managing-personal-data"></a>개인 데이터 관리

Active Directory용 Workday 프로비저닝 솔루션을 사용하려면 프로비저닝 에이전트를 온-프레미스 Windows Server에 설치해야 하고, 이 에이전트는 Workday-AD 특성 매핑에 따라 개인 데이터를 포함할 수 있는 Windows 이벤트 로그에 로그를 만듭니다. 사용자 개인 정보 관련 의무를 준수하기 위해 이벤트 로그를 지우는 Windows 예약 작업을 설정하여 48시간이 지난 데이터가 이벤트 로그에 보존되지 않도록 할 수 있습니다.

Azure AD 프로비전 서비스는 GDPR 분류의 **데이터 프로세서** 범주에 속합니다. 이 서비스는 데이터 프로세서 파이프라인으로, 주요 파트너 및 최종 소비자에게 데이터 처리 서비스를 제공합니다. Azure AD 프로비전 서비스는 사용자 데이터를 생성하지 않으며 개인 데이터가 수집되는 방법 및 해당 데이터의 사용 방법을 독립적으로 제어하지 않습니다. Azure AD 프로비전 서비스의 데이터 검색, 집계, 분석 및 보고는 기존 엔터프라이즈 데이터를 기반으로 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

데이터 보존과 관련해서 Azure AD 프로비전 서비스는 보고서를 생성하거나, 분석을 수행하거나, 30일이 지난 인사이트를 제공하지 않습니다. 따라서 Azure AD 프로비전 서비스는 30일이 지난 데이터를 저장, 처리 또는 유지하지 않습니다. 이러한 설계는 GDPR 규정, Microsoft 개인 정보 보호 준수 규정 및 Azure AD 데이터 보존 정책을 준수합니다.

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [Workday와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.](workday-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [Microsoft Graph API를 사용하여 프로비전 구성을 관리하는 방법 알아보기](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
