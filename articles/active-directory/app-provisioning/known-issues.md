---
title: Azure AD에서 응용 프로그램 프로 비전에 대 한 알려진 문제
description: Azure AD에서 자동화 된 응용 프로그램 프로 비전을 사용할 때 알려진 문제에 대해 알아봅니다.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/12/2020
ms.reviewer: arvinh
ms.openlocfilehash: 23c3dfc6670c96f44a10b2ad5d5bfeb3ff96382c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271008"
---
# <a name="known-issues-application-provisioning"></a>알려진 문제: 응용 프로그램 프로 비전
앱 프로 비전을 사용 하는 경우 알고 있어야 하는 알려진 문제입니다. UserVoice의 응용 프로그램 프로 비전 서비스에 대 한 피드백을 제공할 수 있습니다. [AZURE AD 응용 프로그램 프로 비전 uservoice](https://aka.ms/appprovisioningfeaturerequest)를 참조 하세요. 서비스를 개선할 수 있도록 UserVoice를 면밀 하 게 감시 합니다. 

> [!NOTE]
> 이는 알려진 문제에 대 한 포괄적인 목록이 아닙니다. 나열 되지 않은 문제를 알고 있는 경우 페이지의 맨 아래에 피드백을 제공 합니다.

## <a name="authorization"></a>권한 부여 

**연결 테스트가 완료 된 후에는 저장할 수 없습니다.**

연결을 성공적으로 테스트할 수 있지만 저장할 수 없는 경우 자격 증명에 대해 허용 되는 저장소 제한을 초과 했습니다. 자세히 알아보려면 [관리자 자격 증명 저장 문제](application-provisioning-config-problem-storage-limit.md)를 참조 하세요.

**저장할 수 없음**

저장 하려면 테 넌 트 URL, 비밀 토큰 및 알림 전자 메일을 채워야 합니다. 둘 중 하나만 제공할 수는 없습니다. 

**프로 비전 모드를 수동으로 다시 변경할 수 없음**

처음으로 프로 비전을 구성한 후에는 프로 비전 모드가 수동에서 자동으로 전환 되었음을 알 수 있습니다. 다시 수동으로 변경할 수 없습니다. 그러나 UI를 통해 프로 비전을 해제할 수 있습니다. UI에서 프로 비전을 해제 하는 것은 드롭다운을 수동으로 설정 하는 것과 동일 합니다.  


## <a name="attribute-mappings"></a>특성 매핑 

**특성 SamAccountName 또는 userType은 원본 특성으로 사용할 수 없습니다.**

SamAccountName 및 userType 특성은 기본적으로 원본 특성으로 사용할 수 없습니다. 스키마를 확장 하 여 특성을 추가 합니다. 스키마를 확장 하 여 사용 가능한 원본 특성 목록에 특성을 추가할 수 있습니다. 자세한 내용은 [누락 된 원본 특성](user-provisioning-sync-attributes-for-mapping.md)을 참조 하세요. 

**스키마 확장에 대 한 원본 특성 dropdown이 없습니다.**

때로는 스키마에 대 한 확장이 UI의 원본 특성 드롭다운에서 누락 될 수 있습니다. 특성 매핑의 고급 설정으로 이동 하 여 특성을 수동으로 추가 합니다. 자세히 알아보려면 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조 하세요.

**Null 특성을 프로 비전 할 수 없습니다.**

Azure AD는 현재 null 특성을 프로 비전 할 수 없습니다. 사용자 개체의 특성이 null 이면 건너뜁니다. 

**특성 매핑 식의 최대 문자**

특성 매핑 식에는 최대 1만 문자를 사용할 수 있습니다. 


## <a name="service-issues"></a>서비스 문제 

**지원되지 않는 시나리오**

- 프로 비전 암호는 지원 되지 않습니다. 
- 중첩 그룹 프로 비전은 지원 되지 않습니다. 
- 테 넌 트의 크기 때문에 B2C 테 넌 트에 프로 비전이 지원 되지 않습니다. 

**프로 비전 간격이 고정 되어 있습니다** . 프로 비전 주기 사이의 [시간은](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user#how-long-will-it-take-to-provision-users) 현재 구성할 수 없습니다. 

**대상 앱에서 Azure AD로 이동 하지 않는 변경 내용**

앱 프로 비전 서비스는 외부 앱에서 변경한 내용을 인식 하지 못합니다. 따라서 롤백하는 동작이 수행 되지 않습니다. 앱 프로 비전 서비스는 Azure AD에서 변경 된 내용을 따릅니다. 

**프로 비전 주기는 완료 될 때까지 계속 됩니다.**

프로 비전 `enabled = off` 을 설정 하거나 중지를 적중 하면 현재 프로 비전 주기가 완료 될 때까지 계속 실행 됩니다. 서비스는 다시 프로 비전을 다시 설정할 때까지 이후 사이클의 실행을 중지 합니다.

**그룹의 멤버가 프로 비전 되지 않았습니다.**

그룹이 범위 내에 있고 멤버가 범위를 벗어난 경우 그룹이 프로 비전 됩니다. 범위를 벗어난 사용자는 프로 비전 되지 않습니다. 멤버가 범위에 다시 들어오면 서비스에서 즉시 변경 내용을 검색 하지 않습니다. 프로 비전을 다시 시작 하면 문제가 해결 됩니다. 정기적으로 서비스를 다시 시작 하 여 모든 사용자가 적절히 프로 비전 되도록 하는 것이 좋습니다.  


## <a name="next-steps"></a>다음 단계
- [프로비저닝 작동 방법](how-provisioning-works.md)
