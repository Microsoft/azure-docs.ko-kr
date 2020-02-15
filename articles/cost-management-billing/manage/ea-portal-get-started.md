---
title: Azure Enterprise Portal 시작
description: 이 문서에서는 Azure EA(Azure 기업계약) 고객이 Azure Enterprise Portal을 사용하는 방법을 설명합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 516b3704daee885541cec49b0ee374e050dd4b22
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023347"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Azure Enterprise Portal 시작

이 문서에서는 직접 및 간접 Azure EA(Azure 기업계약) 고객이 [Azure Enterprise Portal](https://ea.azure.com)을 사용하는 방법을 설명합니다. 다음 항목에 대한 기본 정보를 숙지하세요.

- Azure Enterprise Portal의 구조
- Azure Enterprise Portal에서 사용하는 역할
- 구독 만들기
- Azure Enterprise Portal 및 Azure Portal의 비용 분석

이 비디오를 보면서 Azure Enterprise Portal 온보딩 세션 전체를 알아보세요.

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Azure Enterprise Portal 계층 구조

Azure Enterprise Portal 계층 구조는 다음과 같이 구성됩니다.

- **Azure EA Portal** - Azure EA 서비스의 비용을 관리할 수 있는 온라인 관리 포털입니다. 다음을 수행할 수 있습니다.

  - 부서, 계정 및 구독이 포함된 Azure EA 계층 구조를 만듭니다.
  - 사용된 서비스의 비용을 조정하고, 사용량 보고서를 다운로드하고, 가격 목록을 봅니다.
  - 등록의 API 키를 만듭니다.

- **부서**는 비용을 논리 그룹으로 분할하는 데 도움이 됩니다. 부서를 사용하면 부서 수준에서 예산 또는 할당량을 설정할 수 있습니다.

- **계정**은 Azure Enterprise Portal의 조직 구성 단위입니다. 계정을 사용하여 구독을 관리하고 보고서에 액세스할 수 있습니다.

- **구독**은 Azure Enterprise Portal에서 가장 작은 단위입니다. 구독은 서비스 관리자가 관리하는 Azure 서비스의 컨테이너입니다.

다음 다이어그램에서는 간단한 Azure EA 계층 구조를 보여줍니다.

![간단한 Azure EA 계층 구조 다이어그램](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>엔터프라이즈 사용자 역할

다음 관리 사용자 역할은 엔터프라이즈 등록에 포함되어 있습니다.

- 엔터프라이즈 관리자
- 부서 관리자
- 계정 소유자
- 서비스 관리자
- 알림 연락처

역할은 두 개의 다른 포털에서 작동하여 작업을 완료합니다. [Azure Enterprise Portal](https://ea.azure.com)은 청구 및 비용을 관리에 사용되고, [Azure Portal](https://portal.azure.com)은 Azure 서비스 관리에 사용됩니다.

사용자 역할은 사용자 계정과 연결됩니다. 사용자 인증의 유효성을 검사하려면 각 사용자에게 유효한 회사, 학교 또는 Microsoft 계정이 있어야 합니다. 각 계정이 적극적으로 모니터링되는 이메일 주소와 연결되어 있는지 확인합니다. 계정 알림은 이메일 주소로 전달됩니다.

사용자를 설정할 때 여러 계정을 엔터프라이즈 관리자 역할에 할당할 수 있습니다. 그러나 오직 한 계정만 계정 소유자 역할을 보유할 수 있습니다. 또한 엔터프라이즈 관리자와 계정 소유자 역할을 모두 한 계정에 할당할 수 있습니다.

### <a name="enterprise-administrator"></a>엔터프라이즈 관리자

이 역할을 가진 사용자는 최고 수준의 액세스 권한을 가지며 다음 작업을 수행할 수 있습니다.

- 계정 및 계정 소유자 관리
- 다른 엔터프라이즈 관리자 관리
- 부서 관리자 관리
- 알림 연락처 관리
- 모든 계정의 사용량 보기
- 모든 계정의 미청구 요금 보기

기업 등록 하나에 여러 엔터프라이즈 관리자를 둘 수 있습니다. 엔터프라이즈 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다. 모든 엔터프라이즈 관리자는 부서 관리자 역할을 상속합니다.

### <a name="department-administrator"></a>부서 관리자

이 역할을 가진 사용자는 다음과 같은 작업을 수행할 수 있습니다.

- 부서 만들기 및 관리
- 새 계정 소유자 만들기
- 자신이 관리하는 부서의 사용량 세부 정보 보기
- 비용 보기(필요한 권한이 부여된 경우)

기업 등록마다 여러 부서 관리자를 둘 수 있습니다.

새 부서 관리자를 편집하거나 만들 때 부서 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다. 읽기 전용 옵션을 **예**로 설정합니다.

### <a name="account-owner"></a>계정 소유자

이 역할을 가진 사용자는 다음과 같은 작업을 수행할 수 있습니다.

- 구독 만들기 및 관리
- 서비스 관리자 관리
- 구독 사용량 보기

계정마다 고유한 회사, 학교 또는 Microsoft 계정이 필요합니다. Azure Enterprise Portal 관리 역할에 대한 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요.

### <a name="service-administrator"></a>서비스 관리자

서비스 관리자 역할은 Azure Portal에서 서비스를 관리하고 사용자를 공동 관리자 역할에 할당할 수 있는 권한을 갖고 있습니다.

### <a name="notification-contact"></a>알림 연락처

알림 담당자는 등록과 관련된 사용 알림을 받습니다.

## <a name="activate-your-enrollment"></a>등록 활성화

서비스를 활성화하려면 초기 엔터프라이즈 관리자는 [Azure Enterprise Portal](https://ea.azure.com)을 열고 초대 이메일의 이메일 주소를 사용하여 로그인합니다.

엔터프라이즈 관리자로 설정된 경우에는 활성화 이메일을 받을 필요가 없습니다. [Azure Enterprise Portal](https://ea.azure.com)로 이동하여 회사, 학교 또는 Microsoft 계정 이메일 주소와 암호로 로그인합니다.

등록이 여러 개 있는 경우 하나를 선택하여 활성화합니다. 기본적으로 활성 등록만 표시됩니다. 등록 기록을 보려면 Azure Enterprise Portal의 오른쪽 위에서 **활성** 옵션을 선택 취소합니다.

**등록** 아래에 상태가 **활성**으로 표시됩니다.

![활성 등록을 보여주는 예제](./media/ea-portal-get-started/ea-enrollment-status.png)

기존 Azure 엔터프라이즈 관리자만이 다른 엔터프라이즈 관리자를 만들 수 있습니다.

### <a name="create-another-enterprise-administrator"></a>다른 엔터프라이즈 관리자 만들기

다른 엔터프라이즈 관리자를 추가하는 방법은 다음과 같습니다.

1. [Azure Enterprise Portal](https://ea.azure.com)에 로그인합니다.
1. **관리** > **등록 세부 정보**로 이동합니다.
1. 오른쪽 위에서 **+ 관리자 추가**를 선택합니다.

회사, 학교 또는 Microsoft 계정처럼 사용자의 이메일 주소와 기본 인증 방법이 있는지 확인합니다.

엔터프라이즈 관리자가 아닌 경우 등록에 추가해 달라고 엔터프라이즈 관리자에게 요청하세요. 등록에 추가되면 활성화 이메일을 받게 됩니다.

엔터프라이즈 관리자의 도움을 받을 수 없는 경우 [Azure 엔터프라이즈 Portal 지원 요청](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)을 작성합니다. 다음 정보를 지정합니다.

- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사, 학교 또는 Microsoft 계정)
- 기존 엔터프라이즈 관리자의 이메일 승인
  - 기존 엔터프라이즈 관리자에게 연락할 수 없는 경우 파트너 또는 소프트웨어 관리자에게 연락하여 VLSC(Volume Licensing Service Center) 도구를 통해 연락처 세부 정보를 변경해 달라고 요청합니다.

엔터프라이즈 관리 역할에 대한 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요.

## <a name="create-an-azure-enterprise-department"></a>Azure 엔터프라이즈 부서 만들기

엔터프라이즈 관리자와 부서 관리자는 부서를 사용하여 부서 및 비용 센터별로 엔터프라이즈 Azure 서비스와 사용량을 구성하고 보고합니다. 엔터프라이즈 관리자는 다음과 같은 작업을 할 수 있습니다.

- 부서 추가 또는 제거
- 부서에 계정 연결
- 부서 관리자 만들기
- 부서 관리자가 가격 및 비용을 볼 수 있도록 허용

부서 관리자는 부서에 새 계정을 추가할 수 있습니다. 부서 관리자는 부서에서 계정을 제거할 수 있지만 등록에서 제거할 수는 없습니다.

부서를 추가하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 창에서 **관리**를 선택합니다.
1. **부서** 탭을 선택한 다음, **+ 부서 추가**를 선택합니다.
1. 정보를 입력합니다.
   부서 이름은 유일한 필수 필드입니다. 부서 이름은 3자 이상이어야 합니다.
1. 마쳤으면 **추가**를 선택합니다.

## <a name="add-a-department-administrator"></a>부서 관리자 추가

부서를 만든 후 엔터프라이즈 관리자는 부서 관리자를 추가하고 각 부서 관리자를 부서에 연결할 수 있습니다. 부서 관리자는 해당 부서에 대해 다음과 같은 작업을 수행할 수 있습니다.

- 다른 부서 관리자 만들기
- 이름 또는 비용 센터와 같은 부서 속성을 살펴보고 편집
- 계정 추가
- 계정 제거
- 사용량 세부 정보 다운로드
- 월간 사용량 및 요금 보기<sup>1</sup>

> <sup>1</sup> 엔터프라이즈 관리자는 이러한 권한을 부여해야 합니다. 부서 월간 사용량 및 요금을 볼 수 있는 권한이 부여되었지만 볼 수 없는 경우 파트너에게 문의하세요.

### <a name="to-add-a-department-administrator"></a>부서 관리자 추가

엔터프라이즈 관리자로 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 창에서 **관리**를 선택합니다.
1. **부서** 탭을 선택한 다음, 부서를 선택합니다.
1. **+ 관리자 추가**를 선택하고 필요한 정보를 추가합니다.
1. 읽기 전용 액세스의 경우 **읽기 전용** 옵션을 **예**로 설정하고 **추가**를 선택합니다.

![[부서 관리자 추가] 대화 상자를 보여주는 예제](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>읽기 전용 액세스 설정 방법

부서 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다.

- 새 부서 관리자를 만들 때 읽기 전용 옵션을 **예**로 설정합니다.

- 기존 부서 관리자를 편집하려면 다음을 수행합니다.
   1. 부서를 선택한 다음, 편집하려는 **부서 관리자** 옆에 있는 연필 기호를 선택합니다.
   1. 읽기 전용 열기를 **예**로 설정하고 **저장**을 선택합니다.

엔터프라이즈 관리자에게 자동으로 부서 관리자 권한이 할당됩니다.

## <a name="add-an-account"></a>계정 추가

계정 및 구독의 구조는 관리 방법과 청구서 및 보고서에 표시되는 방식에 영향을 줍니다. 비즈니스 부문, 직능 팀 및 지리적 위치에 따라 조직 구조를 만드는 것이 대표적인 예입니다.

계정을 추가하려면 다음을 수행합니다.

1. navigation Enterprise Portal의 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **계정** 탭을 선택합니다. **계정** 페이지에서 **+ 계정 추가**를 선택합니다.
1. 부서를 선택하거나 할당되지 않은 상태로 두고 원하는 인증 유형을 선택합니다.
1. 보고서에서 계정을 식별하는 데 사용할 식별 이름을 입력합니다.
1. 새 계정과 연결할 **계정 소유자 이메일** 주소를 입력합니다.
1. 이메일 주소를 확인하고 **추가**를 선택합니다.

![계정 목록과 [+계정 추가] 옵션을 보여주는 예제](./media/ea-portal-get-started/create-ea-add-an-account.png)

다른 계정을 추가하려면 **다른 계정 추가**를 선택하거나, 왼쪽에 있는 도구 모음의 오른쪽 아래 모서리에서 **추가**를 선택합니다.

계정 소유권을 확인하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 상태를 확인합니다.

   상태가 **보류 중**에서 **시작/종료 날짜**로 변경될 것입니다. 시작/종료 날짜는 사용자가 처음 로그인한 날짜와 계약 종료 날짜입니다.
1. **경고** 메시지가 표시되면 계정 소유자는 Azure Enterprise Portal에 처음 로그인할 때 **계속**을 클릭하여 계정을 활성화해야 합니다.

## <a name="change-account-owner"></a>계정 소유자 변경

엔터프라이즈 관리자는 Azure Enterprise Portal을 사용하여 등록의 구독 계정 소유권을 양도할 수 있습니다. 이 작업은 원본 사용자 계정의 모든 구독을 대상 사용자 계정으로 이전합니다.

계정을 양도할 때 다음과 같은 중요 정보를 알고 있어야 합니다.

- 양도 가능한 작업은 다음과 같습니다.
  - 회사 또는 학교 계정에서 다른 회사 또는 학교 계정으로 양도
  - Microsoft 계정에서 다른 회사 또는 학교 계정으로 양도
  - Microsoft 계정에서 다른 Microsoft 계정으로 양도
  
    대상 계정은 전송 대상으로 유효한 Azure 상거래 계정이어야 합니다. 새 계정의 경우 Azure Enterprise Portal에 로그인할 때 Azure 상거래 계정을 만들라는 메시지가 표시됩니다. 기존 계정의 경우 새 Azure 구독을 만들어야만 적격 계정이 됩니다.

- 회사 또는 학교 계정에서 Microsoft 계정으로 양도할 수 없습니다.

- 구독 양도가 완료되면 Microsoft에서 계정 소유자를 업데이트합니다.

다음과 같은 RBAC(역할 기반 액세스 제어) 정책을 이해합니다.

- 동일한 테넌트에 있는 두 조직 ID 간에 구독을 양도하는 경우 RBAC 정책과 기존 서비스 관리자 및 공동 관리자 역할이 유지됩니다.
- 그 외의 구독 양도에서는 RBAC 정책과 역할 할당이 손실됩니다.
- 정책 및 관리자 역할은 디렉터리 간에 양도되지 않습니다. 서비스 관리자는 대상 계정의 소유자로 업데이트됩니다.

계정 소유자를 변경하기 전에 다음을 수행합니다.

1. Azure Enterprise Portal에서 **계정** 탭을 보고 원본 계정을 찾습니다. 원본 계정이 활성 상태여야 합니다.
1. 대상 계정을 찾아서 활성 상태인지 확인합니다.

모든 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **계정** 탭을 선택하고 계정 위로 마우스를 가져갑니다.
1. 오른쪽에서 계정 소유자 변경 아이콘을 선택합니다. 사람처럼 생긴 아이콘입니다.
1. 적격 계정을 선택하고 **다음**을 선택합니다.
1. 양도를 확인하고 **제출**을 선택합니다.

![계정 소유자 변경 기호를 보여주는 이미지](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

단일 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **계정** 탭을 선택하고 계정 위로 마우스를 가져갑니다.
1. 오른쪽에서 구독 양도 아이콘을 선택합니다. 페이지처럼 생긴 아이콘입니다.
1. 적격 구독을 선택하고 **다음**을 선택합니다.
1. 양도를 확인하고 **제출**을 선택합니다.

![양도 구독 기호를 보여주는 이미지](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

이 비디오를 보면서 Azure Enterprise Portal 사용자 관리에 대해 알아보세요.

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>구독 만들기

계정 소유자는 구독을 보고 관리할 수 있습니다. 구독을 사용하여 조직 내 팀에게 개발 환경 및 프로젝트에 대한 액세스 권한을 부여할 수 있습니다. 테스트, 프로덕션, 개발 및 준비를 예로 들 수 있습니다.

각 애플리케이션 환경에 대해 여러 구독을 만들 때 각 환경의 보안을 유지하는 데 도움이 됩니다.

- 또한 각 구독에 서로 다른 서비스 관리자 계정을 할당할 수 있습니다. 
- 원하는 만큼 구독을 서비스에 연결할 수 있습니다.
- 계정 소유자는 구독을 만들고 계정의 각 구독에 서비스 관리자 계정을 할당합니다.

### <a name="add-a-subscription"></a>구독 추가

다음 정보를 사용하여 구독을 추가합니다.

계정에 구독을 처음으로 추가할 때 MOSA(Microsoft 온라인 정기가입 계약) 및 요금제를 수락하라는 메시지가 표시됩니다. MOSA 및 요금제는 기업계약 고객에게는 적용되지 않지만 구독을 만드는 데 필요합니다. Microsoft Azure 기업계약 등록 수정 사항이 위의 항목을 대체하며 기업계약 고객의 계약 관계는 변경되지 않습니다. 메시지가 표시되면 약관에 동의하는 상자를 선택합니다.

_Microsoft Azure 엔터프라이즈_는 구독을 만들 때의 기본 이름입니다. 등록 내의 다른 구독과 구분하고 엔터프라이즈 수준의 보고서에서 구독을 식별할 수 있도록 이름을 변경할 수 있습니다.

구독을 추가하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 계정에 로그인합니다.
1. **관리** 탭을 선택한 다음, 페이지 위쪽에서 **구독**을 선택합니다.
1. 계정 소유자로 로그인했는지 확인합니다.
1. **+ 구독 추가**를 선택하고 **구매**를 선택합니다.

   계정에 구독을 처음으로 추가할 때 연락처 정보를 입력해야 합니다. 구독을 추가하면 연락처 정보가 자동으로 추가됩니다.

1. **구독**을 선택한 다음, 앞에서 만든 구독을 선택합니다.
1. **구독 세부 사항 편집**을 선택합니다.
1. **구독 이름** 및 **서비스 관리자**를 편집한 다음, 확인 표시를 선택합니다.
  
   구독 이름이 보고서에 표시됩니다. 이 이름은 개발 포털의 구독과 연결된 프로젝트 이름입니다.

새 구독이 구독 목록에 표시될 때까지 최대 24시간이 걸릴 수 있습니다. 구독을 만든 후에는 다음을 수행할 수 있습니다.

- [구독 세부 정보 편집](https://account.azure.com/Subscriptions)
- [구독 서비스 관리](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>엔터프라이즈 구독을 종량제 구독으로 전환

Enterprise 구독을 종량제를 사용하는 개별 구독으로 전환하려면 Azure Enterprise Portal에서 새 지원 요청을 작성해야 합니다. 지원 요청을 작성하려면 **도움말 및 지원** 영역에서 **+ 새 지원 요청**을 선택합니다.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>종량제 구독에 기존 계정 연결

Azure Portal에 기존 Microsoft Azure 계정이 이미 있는 경우 연결된 회사, 학교 또는 Microsoft 계정을 입력하여 기업계약 등록과 연결합니다.

### <a name="associate-an-existing-account"></a>기존 계정 연결

1. Azure Enterprise Portal에서 **관리**를 선택합니다.
1. **계정** 탭을 선택합니다.
1. **+계정 추가**를 선택합니다.
1. 기존 Azure 계정과 연결된 회사, 학교 또는 Microsoft 계정을 입력합니다.
1. 기존 Azure 계정과 연결된 계정을 확인합니다.
1. 보고서에서 이 계정을 식별하는 데 사용할 이름을 입력합니다.
1. **추가**를 선택합니다.
1. 계정을 추가하려면 **+계정 추가** 옵션을 다시 선택하거나, **관리** 단추를 선택하여 홈페이지로 돌아갑니다.
1. **계정** 페이지를 열면 새로 추가된 계정이 **보류 중** 상태로 표시됩니다.

### <a name="confirm-account-ownership"></a>계정 소유권 확인

1. 입력한 회사, 학교 또는 Microsoft 계정과 연결된 이메일 계정에 로그인합니다.
1. _"Microsoft 볼륨 라이선스의 Microsoft Azure 서비스에서 계정 활성화를 위한 초대"_ 라는 제목의 이메일 알림을 엽니다.
1. 초대 이메일에서 **Microsoft Azure Enterprise Portal에 로그인** 링크를 클릭합니다.
1. **로그인**을 선택합니다.
1. 회사, 학교 또는 Microsoft 계정과 암호를 입력하여 로그인하고 계정 소유권을 확인합니다.

### <a name="azure-marketplace"></a>Azure Marketplace

대부분의 구독은 종량제 환경에서 Azure 기업계약으로 변환할 수 있지만, Azure Marketplace 서비스는 그렇지 않습니다. 모든 구독과 요금을 단일 보기에서 보려면 다음과 같이 Azure Marketplace 서비스를 Azure Enterprise Portal에 추가하는 것이 좋습니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **등록 탭**을 선택합니다.
1. **등록 세부 정보** 섹션을 봅니다.
1. Azure Marketplace 필드 오른쪽에서 연필 아이콘을 선택하여 사용하도록 설정합니다. **저장**을 선택합니다.

이제 계정 소유자는 이전에 종량제 구독에서 소유했던 Azure Marketplace 서비스를 구매할 수 있습니다.

Azure EA 등록에서 새 Azure Marketplace 구독이 활성화되면 종량제 환경에서 만든 Azure Marketplace 서비스를 취소합니다. 종량제 결제 방법이 만료될 때 Azure Marketplace 구독이 잘못된 상태로 전환되지 않도록 이 단계를 수행해야 합니다.

### <a name="msdn"></a>MSDN

MSDN 구독은 MSDN 개발/테스트로 자동 변환되며 Azure EA 제품은 기존 금액 크레딧을 잃게 됩니다.

### <a name="azure-in-open"></a>Azure in Open

Azure in Open 구독을 기업계약과 연결하면 사용하지 않은 Azure in Open 크레딧을 돌려받지 못하게 됩니다. 따라서 기업계약에 계정을 추가하기 전에 Azure in Open 구독의 모든 크레딧을 사용하는 것이 좋습니다.  

### <a name="accounts-with-support-subscriptions"></a>지원 구독이 있는 계정

기업계약에 지원 구독이 없는 상태에서 지원 구독이 있는 기존 계정을 Azure Enterprise Portal에 추가하면 MOSA 지원 구독이 자동으로 양도되지 않습니다. 유예 기간 동안 다음 달 말일까지 Azure EA에서 지원 구독을 다시 구매해야 합니다.

## <a name="view-usage-summary-and-download-reports"></a>사용 요약 정보 보기 및 보고서 다운로드

엔터프라이즈 관리자는 Azure Enterprise Portal에서 사용량 데이터, 사용된 약정 금액, 추가 사용량 관련 요금에 대한 요약 정보를 볼 수 있습니다. 요금 정보는 모든 계정 및 구독에서 요약 정보로 제공됩니다.

특정 계정의 자세한 사용량을 보려면 사용량 세부 정보 보고서를 다운로드합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. **보고서**를 선택합니다.
1. **사용량 다운로드** 탭을 선택합니다.
1. 보고서 목록에서, 가져오려는 월간 보고서의 **다운로드**를 선택합니다.

   > [!NOTE]
   > 사용량 세부 정보 보고서에는 관련 세금이 포함되지 않습니다.
   >
   > 사용량이 발생한 시점부터 보고서에 반영되는 시점까지 최대 8시간의 대기 시간이 있을 수 있습니다.

사용 요약 보고서 및 그래프를 보려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.

1. 약정 기간을 선택합니다.

   **사용 요약**의 날짜 범위를 변경하려면 페이지의 오른쪽 위에서 **M**(월별)을 **C**(사용자 지정)로 전환하고 사용자 지정 시작 및 종료 날짜를 입력합니다.

   ![사용자 지정 보기에서 사용 요약 정보를 작성하여 살펴보고 보고서 다운로드](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. 추가 세부 정보를 보려면 그래프에서 보고 싶은 기간 또는 월을 선택합니다.

   - 이 그래프에는 사용량, 서비스 초과 요금, 별도로 청구된 요금, Azure Marketplace 요금의 자세한 분석 내용과 함께 월별 사용 정보가 표시됩니다.
   - 선택한 월에서, 그래프 아래의 필드를 사용하여 부서, 계정 및 구독으로 필터링할 수 있습니다.
   - **서비스별 요금**과 **계층 구조별 요금** 간에 전환할 수 있습니다.
   - 관련 섹션을 확장하여 **Azure 서비스**, **별도 청구 요금** 및 **Azure Marketplace**의 세부 정보를 봅니다.

다음 비디오를 보면서 사용량 확인 방법을 알아보세요.

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV 보고서 다운로드

엔터프라이즈 관리자는 [월간 보고서 다운로드] 페이지를 사용하여 다음 보고서를 CSV 파일로 다운로드할 수 있습니다.

- 잔액 및 요금
- 사용량 세부 정보
- Azure Marketplace 요금
- 가격표

보고서를 다운로드하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**를 선택합니다.
2. 페이지 위쪽에서 **사용량 다운로드**를 선택합니다.
3. 해당 월의 보고서 옆에 있는 **다운로드**를 선택합니다.

   > [!NOTE]
   > 발생한 사용량 날짜와 사용량이 보고서에 표시되는 시간 사이에 최대 5일의 대기 시간이 있을 수 있습니다.
   >
   > Safari를 사용하여 CSV 파일을 Excel로 다운로드하는 사용자는 형식 오류가 발생할 수 있습니다. 이 오류를 피하려면 텍스트 편집기를 사용하여 파일을 여세요.

![사용량 다운로드 페이지를 보여주는 예제](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

다음 비디오를 보면서 사용량 정보를 다운로드하는 방법을 알아보세요.

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>고급 보고서 다운로드

고급 보고서 다운로드를 사용하여 특정 날짜 범위 또는 계정에 대한 보고서를 얻을 수 있습니다. 출력 파일은 대량의 레코드 세트를 수용할 수 있는 CSV 형식입니다.

1. Azure Enterprise Portal에서 **고급 보고서 다운로드**를 선택합니다.
1. 적절한 날짜 범위와 적절한 계정을 선택합니다.
1. **사용 데이터 요청**을 선택합니다.
1. 보고서 상태가 **다운로드**로 업데이트될 때까지 **새로 고침** 단추를 선택합니다.
1. 보고서를 다운로드합니다.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>이전 등록의 사용량 보고서 및 청구 정보 다운로드

등록 양도가 끝난 후 이전 등록의 사용량 보고서 및 청구 정보를 다운로드할 수 있습니다. 기록 보고서는 Azure Enterprise Portal 및 Cost Management에서 사용할 수 있습니다.

Azure Enterprise Portal은 보기에서 비활성 등록을 필터링합니다. 양도된 비활성 등록을 보려면 **활성** 상자를 선택 취소해야 합니다.  

![활성 상자를 선택 취소하면 사용자가 비활성 등록을 볼 수 있습니다.](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Azure EA 사용 약관 용어집

- **계정**: Azure Enterprise Portal의 조직 구성 단위입니다. 구독을 관리하고 보고하는 데 사용됩니다.
- **계정 소유자**: Azure에서 구독 및 서비스 관리자를 관리하는 사람입니다. 이 계정 및 계정과 연결된 구독의 사용량 데이터를 볼 수 있습니다.
- **수정 구독**: 등록 수정계약에 따라 체결된 1년 또는 그와 동일한 구독입니다.
- **약정**: Azure 서비스 요금을 선결제하는 대신 할인된 약정 금액으로 사용할 수 있는 연간 금액 약정입니다.
- **부서 관리자**: 부서를 관리하고, 새 계정 및 계정 소유자를 만들고, 관리하는 부서의 사용량 세부 정보를 살펴보고, 권한이 부여되면 비용을 살펴볼 수 있는 사람입니다.
- **등록 번호**: 기업계약과 관련된 특정 등록을 식별하기 위해 Microsoft에서 제공하는 고유 식별자입니다.
- **엔터프라이즈 관리자**: Azure에서 부서, 부서 소유자, 계정 및 계정 소유자를 관리하는 사람입니다. 엔터프라이즈 관리자를 관리할 수 있을 뿐 아니라 엔터프라이즈 등록과 관련된 모든 계정과 구독의 사용 데이터, 청구된 수량 및 청구되지 않은 요금을 볼 수 있습니다.
- **기업계약**: Microsoft 기술을 기반으로 조직 전체를 표준화하고 Microsoft 소프트웨어 표준에 따라 정보 기술 인프라를 유지하려는 중앙 집중식 구매 방식을 사용하는 고객을 위한 Microsoft 라이선스 계약입니다.
- **기업계약 등록**: Microsoft 제품을 할인된 가격으로 대량 제공하는 기업계약 프로그램의 등록입니다.
- **Microsoft 계정**: 참여하는 사이트에서 단일 자격 증명 세트로 사용자를 인증할 수 있게 해주는 웹 기반 서비스입니다.
- **Microsoft Azure 엔터프라이즈 등록 수정계약(등록 수정계약)** : 기업에서 서명한 수정계약으로, 엔터프라이즈 등록의 일부로 Azure에 대한 액세스 권한을 제공합니다.
- **Azure Enterprise Portal**: 엔터프라이즈 고객이 Azure 계정과 관련 구독을 관리하는 데 사용하는 포털입니다.
- **사용된 리소스 수량**: 한 달 동안 사용된 개별 Azure 서비스의 수량입니다.
- **서비스 관리자**: Azure Enterprise Portal에서 구독 및 개발 프로젝트에 액세스하고 관리할 수 있는 사람입니다.
- **구독**: Azure Enterprise Portal 구독을 나타내며, 동일한 서비스 관리자가 관리하는 Azure 서비스의 컨테이너입니다.
- **회사 또는 학교 계정**: 클라우드에 대한 페더레이션을 사용하여 Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에 적합합니다.

### <a name="enrollment-statuses"></a>등록 상태

- **Pending**: 등록 관리자가 Azure Enterprise Portal에 로그인해야 합니다. 로그인하면 등록이 활성 상태로 전환됩니다.
- **활성**: 등록이 활성 상태이면 Azure Enterprise Portal에서 계정 및 구독을 만들 수 있습니다. 등록은 기업계약 종료 날짜가 될 때까지 활성 상태로 유지됩니다.
- **무한정 확장된 기간**: 무한정 확장된 기간은 기업계약 종료 날짜 후에도 발생합니다. 확장된 기간에 옵트인한 Azure EA 고객은 기업계약이 종료된 후에도 무기한으로 Azure 서비스를 계속 사용할 수 있습니다.

   Azure EA 등록이 기업계약 종료 날짜에 도달하기 전에 등록 관리자는 다음 옵션 중에 선택해야 합니다.

  - 금액 약정을 추가하여 등록 갱신
  - 새 등록으로 양도
  - MOSP(Microsoft Online Subscription 프로그램)로 마이그레이션
  - 등록과 연결된 모든 서비스의 비활성화 확인
- **만료됨**: Azure EA 고객이 연장된 기간에서 옵트아웃되었으며, Azure EA 등록이 기업계약 종료 날짜에 도달했습니다. 등록이 만료되고 연결된 모든 서비스를 사용할 수 없게 됩니다.
- **양도됨**: 연결된 모든 계정과 서비스가 새 등록으로 양도된 등록은 양도됨 상태로 표시됩니다.
  >[!NOTE]
  > 갱신할 때 새 등록 번호가 생성되면 등록이 자동으로 양도되지 않습니다. 이전 등록 번호가 갱신 서류에 포함되어 있어야만 자동으로 양도됩니다.

## <a name="get-started-on-azure-ea---faq"></a>Azure EA 시작 - FAQ

이 섹션에서는 온보딩 프로세스 중에 고객이 묻는 일반적인 질문에 대한 세부 정보를 제공합니다.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>기존 Azure 계정을 Azure EA 등록에 연결할 수 있나요?

예. 계정 소유자로 있는 모든 Azure 구독이 기업계약으로 변환됩니다. Visual Studio, AzurePass, MPN, BizSpark 등과 같이 월간 크레딧을 사용하는 구독이 여기에 포함됩니다. 이러한 구독을 변환하면 월간 크레딧이 손실됩니다.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>실수로 기존 Azure 계정을 Azure EA 등록에 연결했습니다. 그 결과, 월간 크레딧이 손실되었습니다. 월간 크레딧을 돌려받을 수 있나요?

Visual Studio 구독과 동일한 자격 증명을 사용하여 Azure EA 계정 소유자로 로그인한 경우에는 다음 작업 중 하나를 수행하여 개별 Visual Studio 구독 Azure 혜택을 복구할 수 있습니다.

- 연결된 Azure 구독을 제거하거나 이전한 후 Azure Enterprise Portal에서 계정 소유자를 삭제합니다. 그런 다음, 개별 Visual Studio Azure 혜택에 새로 가입합니다.
- VLSC의 관리 사이트에서 Visual Studio 구독자를 삭제하고, 이번에는 다른 자격 증명을 사용하는 계정에 구독을 다시 할당합니다. 그런 다음, 개별 Visual Studio Azure 혜택에 새로 가입합니다.

### <a name="what-type-of-subscription-should-i-create"></a>어떤 유형의 구독을 만들어야 하나요?

Azure Enterprise Portal은 기업 고객을 위한 다음 두 가지 유형의 구독을 제공합니다.

- Microsoft Azure 엔터프라이즈 - 적합한 용도:
  - 모든 프로덕션 사용
  - 인프라 지출을 기준으로 하는 최상의 가격
  
  자세한 내용은 [Azure 영업 팀에 문의](https://azure.microsoft.com/pricing/enterprise-agreement/)하세요.

- Enterprise 개발/테스트 - 적합한 용도:
  - 모든 팀 개발/테스트 워크로드
  - 중간 수준 이상의 개별 개발/테스트 워크로드
  - 특수 MSDN 이미지 및 우선 서비스 요금에 액세스
  
  자세한 내용은 [Enterprise 개발/테스트 제품](https://azure.microsoft.com/offers/ms-azr-0148p/)을 참조하세요.

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>구독 소유권을 다른 계정으로 양도할 수 있나요?

네, 구독 소유권을 다른 계정으로 양도할 수 있습니다. 예를 들어 계정 A에 세 개의 구독이 있는 경우 엔터프라이즈 관리자는 한 구독을 계정 B로, 한 구독을 계정 C로, 한 구독을 계정 D로 양도하거나 모든 구독을 계정 E로 양도할 수 있습니다.

구독을 양도하는 방법은 다음과 같습니다.

1. Azure Enterprise Portal에서 **관리** > **계정**을 선택합니다.
1. 맨 오른쪽에 있는 **계정**을 마우스로 가리키면 **소유권 양도**(사람 아이콘) 및 **구독 양도**(목록 아이콘) 옵션이 표시됩니다. 두 옵션은 활성 계정에만 표시됩니다.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>구독 이름이 제품 이름과 동일합니다. 구독 이름을 조직에 유의미한 이름으로 변경해야 하나요?

구독을 만들 때 이름은 기본적으로 고객이 선택한 제품 형식으로 지정됩니다. 구독을 쉽게 추적할 수 있도록 구독 이름을 변경하는 것이 좋습니다.

이름을 변경하는 방법은 다음과 같습니다.

1. [https://account.windowsazure.com](https://account.windowsazure.com)에 로그인합니다.
1. 구독 목록을 선택합니다.
1. 편집하려는 구독을 선택합니다.
1. **구독 관리** 아이콘을 선택합니다.
1. 구독 세부 정보를 편집합니다.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>비용 센터에서 발생한 비용을 추적하려면 어떻게 해야 하나요?

비용 센터에서 발생한 비용을 추적하려면 다음 수준 중 하나에서 비용 센터를 정의해야 합니다.

- department
- 계정
- Subscription

요구 사항에 따라, 동일한 비용 센터를 사용하여 특정 비용 센터와 관련된 사용량과 비용을 추적할 수 있습니다.

예를 들어 여러 부서가 관련된 특수 프로젝트의 비용을 추적하려면 구독 수준에서 비용 센터를 사용하여 사용량과 비용을 추적하는 것이 좋습니다.

서비스 수준에서는 비용 센터를 정의할 수 없습니다. 서비스 수준에서 사용량을 추적하려면 서비스 수준에서 사용할 수 있는 _태그_ 기능을 사용하면 됩니다.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>조직 내 부서별로 사용량 및 지출을 추적하려면 어떻게 할까요?

Azure EA 등록에서 필요한 만큼 부서를 만들 수 있습니다. 사용량을 올바르게 추적하려면 부서 간에 구독을 공유하면 안 됩니다.

부서 및 구독을 만든 후에는 사용량 보고서에서 데이터를 볼 수 있습니다. 이 정보는 부서 수준에서 사용량을 추적하고 비용 및 지출을 관리하는 데 도움이 됩니다.

Reporting API를 통해 사용량 데이터에 액세스할 수도 있습니다. 자세한 내용 및 샘플 코드는 [Reporting API 설명서](https://ea.azure.com/helpdocs/reportingAPI)를 참조하세요.

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>지출 할당량을 설정하고 한도에 도달하면 경고를 받을 수 있나요?

부서 수준에서 지출 할당량을 설정하면 지출 한도가 정의된 할당량의 50%, 75%, 90%, 100%에 도달할 때마다 시스템에서 자동으로 알려줍니다.

지출 할당량을 정의하려면 부서를 선택하고 편집 아이콘을 선택합니다. 지출 한도 정보를 편집한 후 **저장**을 선택합니다.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>리소스 그룹을 사용하여 RBAC를 구현하고 사용량을 추적했습니다. 관련 사용량 세부 정보를 어떻게 볼 수 있나요?

_리소스 그룹_ 및 _태그_를 사용하는 경우 이 정보는 서비스 수준에서 추적되며, 세부 사용량 다운로드(CSV) 파일에서 이 정보에 액세스할 수 있습니다. Azure Enterprise Portal에서 [사용량 현황 보고서 다운로드](https://ea.azure.com/report/downloadusage)를 참조하세요.

API를 통해 사용량에 액세스할 수도 있습니다. 자세한 내용 및 샘플 코드는 Azure Enterprise Portal 설명서의 [Reporting API](https://ea.azure.com/helpdocs/reportingAPI)를 참조하세요.

> [!NOTE]
> Azure Resource Manager 작업을 지원하는 리소스에만 태그를 적용할 수 있습니다. 클래식 배포 모델을 통해(예: 클래식 포털을 통해) 가상 머신, 가상 네트워크 또는 스토리지를 만든 경우 해당 리소스에 태그를 적용할 수 없습니다. 태그를 지원하려면 Resource Manager를 통해 이러한 리소스를 다시 배포해야 합니다. 다른 모든 리소스는 태그 지정을 지원합니다.

### <a name="can-i-perform-analyses-using-power-bi"></a>Power BI를 사용하여 분석을 수행할 수 있나요?

예. Power BI용 Microsoft Azure 엔터프라이즈 콘텐츠 팩을 사용하여 다음을 수행할 수 있습니다.

- 엔터프라이즈 등록에 대한 Azure 사용량을 빠르게 가져오고 분석합니다.
- 리소스를 가장 많이 사용한 부서, 계정 또는 구독을 확인합니다.
- 조직에서 가장 많이 사용한 서비스를 확인합니다.
- 지출 및 사용량 추세를 추적합니다.

Power BI를 사용하는 방법은 다음과 같습니다.

1. Power BI 웹 사이트로 이동합니다.
1. 유효한 회사 또는 학교 계정으로 로그인합니다.

   회사 또는 학교 계정은 Azure Enterprise Portal을 통해 등록에 액세스할 때 사용되는 계정과 같아도 되고 달라도 됩니다.
1. 서비스 대시보드에서 Microsoft Azure 엔터프라이즈 타일을 선택하고, **연결**을 선택합니다.
1. **Azure 엔터프라이즈에 연결** 화면에서 다음 정보를 입력합니다.
    - Azure 환경 URL: [https://ea.azure.com](https://ea.azure.com)
    - 개월 수: 1~36
    - 등록 번호: 등록 번호
1. **다음**을 선택합니다.
1. **계정 키** 상자에 API 키를 입력합니다.

   Azure Enterprise Portal에서 API 키를 찾을 수 있습니다. **사용량 다운로드** 탭에서 **API 액세스 키**를 선택합니다. 이 키를 복사하여 Power BI의 **계정 키** 상자에 붙여넣습니다.

데이터 세트의 크기에 따라 데이터를 Power BI에 로드하는 데 5~30분 정도 걸릴 수 있습니다.

Power BI 보고는 청구 정보를 볼 수 있는 Azure EA 직접 고객, 파트너 및 간접 고객에게 제공됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Enterprise Portal 관리자는 [Azure Enterprise Portal 관리](ea-portal-administration.md)를 읽고 일반적인 관리 작업에 대해 알아보아야 합니다.
- Azure Enterprise Portal 이슈를 해결하는 데 도움이 필요한 경우 [Azure Enterprise Portal 액세스 문제 해결](ea-portal-troubleshoot.md)을 참조하세요.
- Azure EA 온보딩 가이드는 [Azure EA 온보딩 가이드(PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)를 참조하세요.
