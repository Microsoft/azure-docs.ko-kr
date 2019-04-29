---
title: 'Azure AD Connect: 설치 하는 동안 원본 앵커 문제 해결 | Microsoft Docs'
description: 이 항목에서는 설치 하는 동안 원본 앵커를 사용 하 여 문제를 해결 하는 방법에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114158"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>설치 하는 동안 원본 앵커 문제 해결
이 문서에서는 다양 한 설명 원본 앵커 관련 설치 및 제공 방법으로 이러한 문제를 해결 하는 동안 발생할 수 있는 문제입니다.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory에서 잘못 된 원본 앵커

### <a name="custom-installation"></a>사용자 지정 설치

사용자 지정 설치 하는 동안 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. 정책을 Azure Active Directory에 있는 경우 Azure AD Connect 고객에 의해 재정의 되지 않으면 동일한 정책이 적용 됩니다. 마법사는 특성을 읽은 알려 줍니다. 또한 마법사는 원본 앵커 정책 재정의 하려는 경우 경고 합니다.

이 읽기 작업 동안 Azure Active Directory의 원본 앵커 정책 예기치 않은 수 있습니다. 이 경우 Azure AD Connect를 사용 하 여 어떤 원본 앵커를 인식 하지 못합니다 및 수동 재정의 해야 합니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

이 문제를 해결 하려면 특정 특성을 선택 하 여 원본 앵커를 수동으로 재정의할 수 있습니다. 확신 하는 경우에이 옵션을 사용 하 여 계속 진행 선택 하는 특성입니다. 확실 하지 않은, 경우에 문의 [Microsoft 지원](https://support.microsoft.com/contactus/) 지침에 대 한 합니다. 원본 앵커 정책을 변경 하는 경우 온-프레미스 사용자 및 관련된 Azure 리소스 간의 연결을 중단할 수 있습니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>기본 설치
Express 설치 하는 동안 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. 정책을 Azure Active Directory에 있는 경우 Azure AD Connect에 동일한 정책이 적용 됩니다. 수동 재정의 작업을 수행 하는 방법이 있습니다.

이 읽기 작업 동안 Azure Active Directory의 원본 앵커 정책 예기치 않은 수 있습니다. 이 경우 Azure AD Connect는 해야 할 원본 앵커 모릅니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

이 문제를 해결 하려면 다시 사용자 지정 모드를 사용 하 여를 설치 하 고 특정 특성을 선택 하 여 원본 앵커를 수동으로 재정의 해야 합니다. 확신 하는 경우에이 옵션을 사용 하 여 계속 진행 선택 하는 특성입니다. 확실 하지 않은, 경우에 문의 [Microsoft 지원](https://support.microsoft.com/contactus/) 지침에 대 한 합니다. 원본 앵커 정책을 변경 하는 경우 온-프레미스 사용자 및 관련된 Azure 리소스 간의 연결을 중단할 수 있습니다.

### <a name="invalid-source-anchor-in-sync-engine"></a>동기화 엔진에서 잘못 된 원본 앵커
가능한 Azure AD는 설치 동안 잘못 된 원본 앵커를 사용 하 여 동기화 엔진을 구성 하는 시도 연결 합니다. 이 작업은 대부분 제품 문제 및 Azure AD Connect의 설치가 실패 합니다. 연락처 [Microsoft 지원](https://support.microsoft.com/contactus/) 이 문제를 실행 하는 경우.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.