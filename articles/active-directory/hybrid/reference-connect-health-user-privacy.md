---
title: Azure AD Connect Health 및 사용자 개인 정보 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect Health와 함께 사용자 개인 정보를 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182363"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>사용자 개인 정보 및 Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>이 문서에서는 Azure AD Connect Health 및 사용자 개인 정보를 다룹니다.  Azure AD Connect 및 사용자 개인 정보에 대한 자세한 내용은 [여기](reference-connect-user-privacy.md) 문서를 참조하세요.

## <a name="user-privacy-classification"></a>사용자 개인 정보 분류
Azure AD Connect Health는 GDPR 분류의 **데이터 프로세서** 범주에 해당합니다. 이 서비스는 데이터 프로세서 파이프라인으로, 주요 파트너 및 최종 소비자에게 데이터 처리 서비스를 제공합니다. Azure AD Connect Health는 사용자 데이터를 생성하지 않으며 개인 데이터가 수집되는 방법 및 해당 데이터의 사용 방법을 독립적으로 제어하지 않습니다. Azure AD Connect Health의 데이터 검색, 집계, 분석 및 보고는 기존 온-프레미스 데이터를 기반으로 합니다. 

## <a name="data-retention-policy"></a>데이터 보존 정책
Azure AD Connect Health는 보고서를 생성하거나, 분석을 수행하거나, 30일이 지난 정보를 제공하지 않습니다. 따라서 Azure AD Connect Health는 30일이 지난 데이터를 저장, 처리 또는 유지하지 않습니다. 이러한 설계는 GDPR 규정, Microsoft 개인 정보 보호 준수 규정 및 Azure AD 데이터 보존 정책을 준수합니다. 

활성 **상태 서비스 데이터가 최신 상태가 아닙니다.** **오류** 경고가 30일 넘게 연속해서 발생하는 서버에서는 이 기간 동안 데이터가 Connect Health에 도달하지 않은 것입니다. 이러한 서버는 사용되지 않도록 설정되고 Connect Health 포털에 표시되지 않습니다. 서버를 다시 사용하도록 설정하려면 [상태 에이전트를 제거했다가 다시 설치](how-to-connect-health-agent-install.md)해야 합니다. 동일한 경고 유형을 갖는 **경고**는 여기에 해당되지 않습니다. 경고는 대상 서버에서 일부 데이터가 누락된 것을 나타냅니다. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health에서 데이터 수집 및 모니터링 사용 안 함
Azure AD Connect Health를 사용하면 모니터링되는 개별 서버 또는 모니터링되는 서비스의 인스턴스에 대한 데이터 수집을 중지할 수 있습니다. 예를 들어 Azure AD Connect Health를 사용하여 모니터링되는 개별 ADFS(Active Directory Federation Services) 서버의 데이터 수집을 중지할 수 있습니다. 또한 Azure AD Connect Health를 사용하여 모니터링 중인 전체 ADFS 인스턴스의 데이터 수집을 중지할 수도 있습니다. 중지하도록 선택하면 데이터 수집을 중지한 후 Azure AD Connect Health 포털에서 해당 서버가 삭제됩니다. 

>[!IMPORTANT]
> Azure AD Connect Health에서 모니터링 되는 서버를 삭제 하려면 Azure AD 전역 관리자 권한 또는 Azure RBAC의 참가자 역할이 필요 합니다.
>
> Azure AD Connect Health에서 서버 또는 서비스 인스턴스를 제거하는 작업은 취소할 수 없습니다. 

### <a name="what-to-expect"></a>고려해야 할 사항
모니터링되는 개별 서버 또는 모니터링되는 서비스의 인스턴스에 대한 데이터 수집 및 모니터링을 중지하는 경우, 다음 사항에 주의하세요.

- 모니터링되는 서비스의 인스턴스를 삭제하면 해당 인스턴스가 포털의 Azure AD Connect Health 모니터링 서비스 목록에서 제거됩니다. 
- 모니터링되는 서버 또는 모니터링되는 서비스의 인스턴스를 삭제하면 서버에서 상태 에이전트가 제거되거나 제거되지 않습니다. 상태 에이전트는 Azure AD Connect Health에 데이터를 전송하지 않도록 구성되어 있습니다. 상태 에이전트는 이전에 모니터링한 서버에서 수동으로 제거해야 합니다.
- 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 모니터링되는 서비스의 인스턴스에 속한 모든 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>모니터링되는 서비스의 인스턴스에 대한 데이터 수집 및 모니터링을 사용하지 않도록 설정
[Azure AD Connect Health에서 서비스 인스턴스를 제거하는 방법](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)을 참조하세요.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>모니터링되는 서버에 대한 데이터 수집 및 모니터링을 사용하지 않도록 설정
[Azure AD Connect Health에서 서버를 제거하는 방법](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)을 참조하세요.

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Azure AD Connect Health에서 모니터링되는 모든 서비스에 대해 데이터 수집 및 모니터링 사용 안 함
Azure AD Connect Health는 테넌트에 등록된 **모든** 서비스의 데이터 수집을 중지하는 옵션을 제공합니다. 작업을 수행하기 전에 모든 전역 관리자의 승인을 얻고 신중하게 고려하는 것이 좋습니다. 프로세스가 시작되면 Connect Health 서비스는 모든 서비스의 데이터 수신, 처리 및 보고를 중지합니다. Connect Health 서비스의 기존 데이터는 30일 동안만 보존됩니다.
특정 서버의 데이터 수집을 중지하려면 특정 서버의 삭제 단계를 따르세요. 테넌트 범위 데이터 수집을 중지하려면 다음 단계에 따라 데이터 수집을 중지하고 테넌트의 모든 서비스를 삭제합니다.

1. 주 블레이드의 구성 아래에서 **일반 설정**을 클릭합니다. 
2. 블레이드의 맨 위에서 **데이터 수집 중지** 단추를 클릭합니다. 프로세스가 시작되면 테넌트 구성 설정의 다른 옵션이 사용하지 않도록 설정됩니다.  
 
   ![데이터 수집 중지](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. 데이터 수집을 중지할 경우 영향을 받는 온보딩된 서비스 목록을 확인합니다. 
4. 정확한 테넌트 이름을 입력하여 **삭제** 작업 단추 활성화
5. **삭제**를 클릭하여 모든 서비스의 삭제를 트리거합니다. Connect Health가 온보딩된 서비스에서 전송된 모든 데이터의 수신, 처리 및 보고를 중지합니다. 전체 프로세스에는 최대 24시간이 소요될 수 있습니다. 이 단계는 되돌릴 수 없습니다. 
6. 프로세스가 완료되면 Connect Health에 등록된 서비스가 더 이상 표시되지 않습니다. 

   ![데이터 수집이 중지된 후](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health에서 데이터 수집 및 모니터링을 다시 사용하도록 설정
Azure AD Connect Health에서 이전에 삭제된 모니터링되는 서비스에 대한 모니터링을 다시 사용하도록 설정하려면 모든 서버에서 [상태 에이전트를 제거한 후 다시 설치](how-to-connect-health-agent-install.md)해야 합니다.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>모든 모니터링되는 서비스에 대한 데이터 수집 및 모니터링을 다시 사용하도록 설정

Azure AD Connect Health에서 테넌트 범위 데이터 수집을 다시 시작할 수 있습니다. 작업을 수행하기 전에 모든 전역 관리자의 승인을 얻고 신중하게 고려하는 것이 좋습니다.

>[!IMPORTANT]
> 다음 단계는 24시간 작업 중지 후 사용할 수 있습니다.
> 데이터 수집을 사용하도록 설정하면, Connect Health에 표시되었던 정보 및 모니터링 데이터에 이전에 수집된 기존 데이터가 표시되지 않습니다. 

1. 주 블레이드의 구성 아래에서 **일반 설정**을 클릭합니다. 
2. 블레이드의 맨 위에서 **데이터 수집 사용** 단추를 클릭합니다. 
 
   ![데이터 컬렉션 활성화](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. 정확한 테넌트 이름을 입력하여 **사용** 단추를 활성화합니다.
4. **사용** 단추를 클릭하여 Connect Health 서비스의 데이터 수집 권한을 부여합니다. 변경 내용은 즉시 적용됩니다. 
5. [설치 프로세스](how-to-connect-health-agent-install.md)에 따라 모니터링할 서버에 에이전트를 다시 설치하고 서비스가 포털에 표시됩니다.  


## <a name="next-steps"></a>다음 단계
* [보안 센터에서 Microsoft 개인 정보 취급 방침을 검토합니다.](https://www.microsoft.com/trustcenter)
* [Azure AD Connect 및 사용자 개인 정보](reference-connect-user-privacy.md)

