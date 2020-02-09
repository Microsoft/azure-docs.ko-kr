---
title: 포함 파일
description: 포함 파일
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 831e3330126d82795cece128c28cb96b7199d69a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825580"
---
**시험** 사용 탭에서는 고객이 제품을 구입 하기 전에 제품을 사용해 볼 수 있도록 데모 (또는 "시험 사용")를 설정할 수 있습니다. [테스트 드라이브인 이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)문서에서 자세히 알아보세요. 제품에 대 한 시험 사용을 더 이상 제공 하지 않으려는 경우 **제품 설치** 페이지로 돌아가 **테스트 드라이브 사용**을 선택 취소 합니다.

### <a name="technical-configuration"></a>기술 구성
각각의 기술 구성 요구 사항을 포함 하는 다음과 같은 종류의 테스트 드라이브를 사용할 수 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (기술 구성이 필요 하지 않음)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 테스트 드라이브에 대 한 기술 구성

솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용 합니다. [Azure Resource Manager 테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)를 설정 하는 방법에 대해 자세히 알아보세요.

- **지역** (필수): 현재 테스트 드라이브를 사용할 수 있는 26 개의 Azure 지원 지역이 있습니다. 일반적으로 가장 큰 수의 고객을 예상 하는 지역에서 테스트 드라이브를 사용할 수 있도록 하 여 최상의 성능을 위해 가장 가까운 지역을 선택할 수 있게 합니다. 구독에서 선택 하는 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인 해야 합니다.

- **인스턴스**: 제품을 사용할 수 있는 지역 수를 곱하여 유형 (핫 또는 콜드) 및 사용할 수 있는 인스턴스 수를 선택 합니다.

**핫**:이 유형의 인스턴스가 배포 되 고 선택한 지역에 따라 액세스를 대기 합니다. 고객은 배포를 기다릴 필요 없이 테스트 드라이브의 *핫* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않고 *핫* 인스턴스를 사용할 수 없는 경우 고객 사용에 대 한 삭제를 원하지 않으므로 *핫* 인스턴스를 하나 이상 사용 하는 것이 좋습니다.

**콜드**:이 유형의 인스턴스는 지역별 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 드라이브를 요청할 때 배포 하기 위해 전체 테스트 드라이브 리소스 관리자 템플릿이 필요 하므로 *콜드* 인스턴스는 *핫* 인스턴스 보다 로드 속도가 훨씬 느립니다. 이는 테스트 드라이브의 기간에 대해서만 비용을 지불 하면 되므로 항상 *핫* 인스턴스와 마찬가지로 Azure 구독에서 실행 *되지 않습니다* .

- **테스트 드라이브 Azure Resource Manager 템플릿**: Azure Resource Manager 템플릿이 포함 된 .Zip을 업로드 합니다.  빠른 시작 문서 [Azure Portal를 사용 하 여 Azure Resource Manager 템플릿을 만들고 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)하는 방법에 대 한 자세한 정보를 Azure Resource Manager.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 시간 (시간)을 입력 합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간은 전체 시간 (예: "2" 시간, "1.5"은 유효 하지 않음) 으로만 설정할 수 있습니다.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 시험 드라이브에 대 한 기술 구성

Microsoft는이 유형의 테스트 드라이브를 사용 하 여 서비스 프로 비전 및 배포를 호스팅하고 유지 관리 하 여 테스트 드라이브를 설정 하는 복잡성을 제거할 수 있습니다. 이 유형의 호스트 된 테스트 드라이브에 대 한 구성은 테스트 드라이브가 비즈니스 중부, 고객 참여 또는 작업 대상을 대상으로 하는지 여부에 관계 없이 동일 합니다.

- **최대 동시 테스트 드라이브** 수 (필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정 합니다. 테스트 드라이브가 활성화 된 상태에서 각 동시 사용자는 Dynamics 365 라이선스를 사용 하므로 최대 제한 집합을 지 원하는 데 사용할 수 있는 충분 한 라이선스가 있는지 확인 해야 합니다. 권장 값은 3~5입니다.

- **테스트 드라이브 기간** (필수): 시간 수를 정의 하 여 테스트 드라이브가 활성 상태로 유지 되는 시간을 입력 합니다. 이 시간이 지난 후에는 세션이 종료 되 고 더 이상 라이선스 중 하나를 사용 하지 않습니다. 제품의 복잡도에 따라 2-24 시간 값을 설정 하는 것이 좋습니다. 이 기간은 전체 시간 (예: "2" 시간, "1.5"은 유효 하지 않음) 으로만 설정할 수 있습니다.  사용자는 시간이 부족 하 고 테스트 드라이브에 다시 액세스 하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL** (필수): 고객이 테스트 드라이브를 시작 하는 url입니다. 일반적으로 샘플 데이터가 설치 된 앱을 실행 하는 Dynamics 365 인스턴스의 URL (예: https://testdrive.crm.dynamics.com)입니다.

- **인스턴스 웹 API url** (필수): Microsoft 365 계정에 로그인 하 고 **설정** \&gt;로 이동 하 여 DYNAMICS 365 인스턴스의 Web API url을 검색 합니다. **사용자 지정** \&gt; **개발자 리소스** \&gt; **인스턴스 웹 API (서비스 루트 URL)** 에서 여기에 있는 URL을 복사 합니다 (예: https://testdrive.crm.dynamics.com/api/data/v9.0).

- **역할 이름** (필수): 사용자 지정 Dynamics 365 테스트 드라이브에서 정의한 보안 역할 이름을 제공 합니다. 이는 테스트 드라이브 (예: 테스트-드라이브-역할) 중에 사용자에 게 할당 됩니다.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>논리 앱 테스트 드라이브에 대 한 기술 구성

모든 사용자 지정 제품은 다양 한 복잡 한 솔루션 아키텍처를 포함 하는이 유형의 테스트 드라이브 배포 템플릿을 사용 해야 합니다. 논리 앱 테스트 드라이브를 설정 하는 방법에 대 한 자세한 내용은 GitHub의 [작업](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 를 참조 하세요.

- **지역** (필수, 단일 선택 드롭다운 목록): 현재 테스트 드라이브를 사용할 수 있는 26 개의 Azure 지원 지역이 있습니다. 논리 앱에 대 한 리소스는 선택한 지역에 배포 됩니다. 논리 앱에 특정 지역에 저장 된 사용자 지정 리소스가 있는 경우 여기에서 해당 지역이 선택 되어 있는지 확인 합니다. 이 작업을 수행 하는 가장 좋은 방법은 포털의 Azure 구독에서 논리 앱을 로컬로 완전히 배포 하 고이를 선택 하기 전에 제대로 작동 하는지 확인 하는 것입니다.

- **최대 동시 테스트 드라이브** 수 (필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정 합니다. 이러한 테스트 드라이브는 이미 배포 되어 있으므로 고객이 배포를 기다리지 않고 즉시 액세스할 수 있습니다.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 시간 (시간)을 입력 합니다. 이 기간이 종료 되 면 테스트 드라이브가 자동으로 종료 됩니다.

- **Azure 리소스 그룹 이름** (필수): 논리 앱 테스트 드라이브가 저장 된 [azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력 합니다.

- **Azure 논리 앱 이름** (필수): 사용자에 게 테스트 드라이브를 할당 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **논리 앱 이름 프로 비전** 해제 (필수): 고객이 완료 되 면 테스트 드라이브를 프로 비전 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에는 기술 구성이 필요 하지 않습니다.

대화형 Power BI 시각적 개체를 시연 하려는 제품은 포함 된 링크를 사용 하 여 사용자 지정 된 대시보드를 테스트 드라이브로 공유할 수 있으며 추가 기술 구성은 필요 하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 정보

사용자를 대신 하 여 테스트 드라이브를 배포 하기 위해 별도의 고유한 Azure 구독을 만들어 제공 하세요. (Power BI 테스트 드라이브에는 필요 하지 않음)

- **Azure 구독 id** (Azure Resource Manager 및 논리 앱에 필요): 리소스 사용량 보고 및 청구를 위해 azure 계정 서비스에 대 한 액세스 권한을 부여 하는 구독 Id를 입력 합니다. 아직 없는 경우 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 왼쪽 메뉴의 **구독** 탭으로 이동 하 여 Azure 구독 ID를 찾을 수 있습니다. 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764

- **AZURE ad 테넌트 id** (필수): AZURE ACTIVE DIRECTORY (AD) [테넌트 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 왼쪽 메뉴에서 Active Directory 탭을 선택 하 고, **속성** 을 선택한 다음, 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용 하 여 조직의 테넌트 ID를 조회할 수도 있습니다.

- **AZURE ad 테넌트 이름** (동적 365에 필요): AZURE ACTIVE DIRECTORY (AD) 이름을 입력 합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 오른쪽 위 모서리에 있는 테넌트 이름이 계정 이름 아래에 나열 됩니다.

- **AZURE ad 앱 id** (필수): AZURE ACTIVE DIRECTORY (AD) [응용 프로그램 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **앱 등록**를 선택 하 고 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE ad 앱 클라이언트 암호** (필수): azure ad 응용 프로그램 [클라이언트 암호](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)를 입력 합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택 하 고 **앱 등록**을 선택한 다음 테스트 드라이브 앱을 선택 합니다. 그런 다음 **인증서 및 비밀**을 선택 하 고, **새 클라이언트 암호**를 선택 하 고, 설명을 입력 하 고, **만료**됨 **을 선택 하** 고, **추가**를 선택 합니다. 값을 복사 해야 합니다. 이 작업을 수행 하기 전에 페이지에서 다른 곳으로 이동 하지 마세요. 그렇지 않으면 값에 액세스할 수 없습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

### <a name="test-drive-listings-optional"></a>드라이브 목록 테스트 (선택 사항)

**테스트 드라이브 탭 아래** 에 있는 **테스트 드라이브 목록** 옵션은 테스트 드라이브를 사용할 수 있는 언어 (및 시장)를 표시 하며 현재 영어 (미국)만 사용할 수 있는 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 테스트 드라이브 세부 정보 (설명, 사용자 설명서, 비디오 등)를 정의 해야 합니다.

- **설명** (필수): 시험 사용에 대 한 설명, 표시 될 내용, 사용자가 시험해 볼 기능, 탐색할 기능 및 사용자가 제품을 확보할 지 여부를 결정 하는 데 도움이 되는 모든 관련 정보를 설명 합니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보** (Azure Resource Manager 및 논리 시험 사용에 필요):이 테스트 드라이브에 액세스 하 고 사용 하기 위해 고객이 알아야 할 사항에 대해 설명 합니다. 제품 사용에 대 한 시나리오와 고객이 테스트 드라이브를 통해 기능에 액세스 하기 위해 알아야 하는 사항을 정확히 살펴봅니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서** (필수): 테스트 드라이브 환경을 자세히 연습 합니다. 사용자 설명서는 시험 사용에 대 한 고객의 의견을 정확 하 게 파악 하 고 있을 수 있는 질문에 대 한 참조로 사용할 수 있습니다. 업로드 후 파일은 PDF 형식 이어야 하며 (최대 255 자) 이름이 지정 되어야 합니다.

- **비디오: 비디오 추가** (선택 사항): 비디오를 YouTube 또는 Vimeo에 업로드 하 고 링크 및 미리 보기 이미지 (533 x 324 픽셀)를 사용 하 여 여기에서 참조할 수 있으므로 고객이 테스트 드라이브를 보다 잘 이해 하는 데 도움이 되는 정보를 볼 수 있습니다. 예를 들어 제품의 기능을 성공적으로 사용 하 고 혜택을 강조 하는 시나리오를 이해 하는 방법입니다.
  - **이름** (필수)
  - **URL (YouTube 또는 Vimeo만 해당)** (필수)
  - **축소판 그림 (533 x 324px)** : 이미지 파일은 PNG 형식 이어야 합니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.
