---
title: Azure Portal를 사용 하 여 Azure Stack에 지 정렬 문제 해결 | Microsoft Docs
description: Azure Stack Edge 순서 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: twooley
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: twooley
ms.openlocfilehash: 226274c52610e24c305400d77dc7737d32c2b722
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783997"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Azure Stack Edge 순서 문제 해결

이 문서에서는 Azure Stack Edge 순서 지정 문제를 해결 하는 방법을 설명 합니다.

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 주문 문제 해결

## <a name="unsupported-subscription-or-region"></a>지원 되지 않는 구독 또는 지역

**오류 설명:** Azure Portal에서 오류가 발생 하는 경우:

*선택한 구독 또는 지역은 지원 되지 않습니다. 다른 구독 또는 지역을 선택 하세요.*

![지원 되지 않는 구독 또는 지역](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**제안 된 솔루션:**  [EA (Microsoft 기업계약](https://azure.microsoft.com/overview/sales-number/)), [CSP (클라우드 솔루션 공급자)](https://docs.microsoft.com/partner-center/azure-plan-lp)또는 [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/)와 같은 지원 되는 구독을 사용 했는지 확인 합니다. 종 량 제 구독은 지원 되지 않습니다. 자세한 내용은 [Azure Stack Edge 리소스 필수 조건](azure-stack-edge-deploy-prep.md#prerequisites)을 참조 하세요.

Microsoft에서 대/소문자를 기준으로 구독 유형 업그레이드를 허용할 수 있습니다. [Microsoft 지원](https://azure.microsoft.com/support/options/) 에 문의 하 여 요구 사항을 이해 하 고 이러한 한도를 적절 하 게 조정 합니다.

## <a name="selected-subscription-type-not-supported"></a>선택한 구독 유형이 지원 되지 않음

**오류:** EA, CSP 또는 후원 된 구독이 있는 경우 다음과 같은 오류가 표시 됩니다.

*선택한 구독 유형은 지원 되지 않습니다. 지원 되는 구독을 사용 하는지 확인 합니다. [자세히 알아보세요](azure-stack-edge-deploy-prep.md#prerequisites). 지원 되는 구독 유형을 사용 하는 경우 `Microsoft.DataBoxEdge` 공급자가 등록 되어 있는지 확인 합니다. 등록 하는 방법에 대 한 자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조*하세요.

**제안 된 솔루션:** Azure Stack Edge 리소스 공급자를 등록 하려면 다음 단계를 따르세요.

1. Azure Portal에서 **홈**  >  **구독**으로 이동 합니다.

2. 장치를 주문 하는 데 사용할 구독을 선택 합니다.

3. **리소스 공급자** 를 선택 하 고 **DataBoxEdge**를 검색 합니다.

    ![리소스 공급자 등록](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

리소스 공급자를 등록 하기 위한 소유자 또는 참가자 액세스 권한이 없는 경우 다음과 같은 오류가 표시 됩니다. *구독 &lt; 구독 이름에 &gt; 리소스 공급자를 등록할 수 있는 권한이 없습니다. DataBoxEdge.*

자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조 하세요.

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>DataBoxEdge가 구독에 등록 되지 않았습니다.

**오류:** Azure Portal에서 Edge 또는 Data Box Gateway Azure Stack에 사용할 구독을 선택 하 고 다음 오류를 가져옵니다.

*리소스 공급자: DataBoxEdge가 구독 구독 이름에 등록 되어 있지 않으며 &lt; &gt; 구독 &lt; 구독 이름 &gt; 에 대해 리소스 공급자를 등록할 수 있는 권한이*없습니다.

**제안 된 솔루션:** 구독 액세스 권한을 상승 시키거나 소유자 또는 참가자 액세스 권한이 있는 사용자를 찾아 리소스 공급자를 등록 합니다.

## <a name="resource-disallowed-by-policy"></a>정책에서 허용 하지 않는 리소스

**오류:** Azure Portal에서 리소스 공급자를 등록 하려고 시도 하 여 다음 오류를 가져옵니다.

*&lt;정책에서 리소스 리소스 이름을 &gt; 허용 하지 않았습니다. (코드: RequestDisallowedByPolicy). 이니셔티브: 일반적으로 원치 않는 리소스 종류를 거부 합니다. 정책: 리소스 종류가 허용 되지 않습니다.*

**제안 된 솔루션:** 이 오류는 리소스 생성을 차단 하는 기존 Azure 정책 때문에 발생 합니다. Azure 정책은 Azure 리소스를 사용 하거나 만드는 동안 규정 준수를 보장 하기 위해 조직의 시스템 관리자가 설정 합니다. 이러한 정책에서 Edge 리소스 생성 Azure Stack 차단 하 고 있는 경우 시스템 관리자에 게 문의 하 여 Azure 정책을 편집 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stack에 지 문제를 해결](azure-stack-edge-troubleshoot.md)하는 방법에 대해 자세히 알아보세요.
