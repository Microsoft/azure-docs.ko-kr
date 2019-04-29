---
title: Azure API Management 정책을 설정 및 편집하는 방법 | Microsoft Docs
description: 이 항목에서는 Azure API Management 정책을 설정하거나 편집하는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097200"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management 정책을 설정 및 편집하는 방법

정책 정의는 일련의 인바운드 및 아웃바운드 명령문을 설명하는 XML 문서입니다. 정의 창에서 XML을 직접 편집할 수 있습니다. 정책 창 오른쪽에 제공되는 목록에서 미리 정의된 정책을 선택할 수도 있습니다. 현재 범위에 해당하는 명령문은 활성화되고 강조 표시됩니다. 사용할 수 있는 명령문을 클릭하면 정의 뷰에서 커서의 위치에 적절한 XML이 추가됩니다. 

정책에 대한 자세한 내용은 [Azure API Management의 정책](api-management-howto-policies.md)을 참조하세요.

## <a name="set-or-edit-a-policy"></a>정책 설정 또는 편집

정책을 설정하거나 편집하려면 다음 단계를 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. APIM 인스턴스로 이동합니다.
3. **API** 탭을 클릭합니다.

    ![정책 편집](./media/set-edit-policies/code-editor.png)

4. 이전에 가져온 API 중 하나를 선택합니다.
5. **디자인** 탭을 선택합니다.
6. 정책을 적용하려는 작업을 선택합니다. 모든 작업에 정책을 적용하려는 경우 **모든 작업**을 선택합니다.
7. **인바운드 처리** 또는 **아웃바운드 처리** 섹션에서 **</>**(코드 편집기) 아이콘을 선택합니다.
8. 원하는 정책 코드를 적절한 블록 중 하나에 붙여넣습니다.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>범위 구성

정책을 글로벌로 구성하거나 제품, API 또는 작업 범위로 구성할 수 있습니다. 정책을 구성하려면 먼저 정책을 적용할 범위를 선택해야 합니다.

정책 범위는 다음 순서로 평가됩니다.

1. 전역 범위
2. 제품 범위
3. API 범위
4. 작업 범위

정책 내의 명령문은 `base` 요소(있는 경우)의 위치에 따라 평가됩니다. 글로벌 정책에는 부모 정책이 없으며 해당 정책 안의 `<base>` 요소를 사용해도 아무 효과가 없습니다.

정책 편집기에서 현재 범위의 정책을 보려면 **Recalculate effective policy for selected scope**(선택한 범위에 대한 실제 정책 다시 계산)을 클릭합니다.

### <a name="global-scope"></a>전역 범위

전역 범위는 APIM 인스턴스의 **모든 API**에 대해 구성됩니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 APIM 인스턴스로 이동합니다.
2. **모든 API**를 클릭합니다.

    ![전역 범위](./media/api-management-howto-policies/global-scope.png)

3. 삼각형 아이콘을 클릭합니다.
4. **코드 편집기**를 선택합니다.
5. 정책을 추가하거나 편집합니다.
6. **저장**을 누릅니다. 

    이러한 변경 내용은 API Management 게이트웨이에 즉시 전파됩니다.

### <a name="product-scope"></a>제품 범위

제품 범위는 선택한 제품에 대해 구성됩니다.

1. **제품**을 클릭합니다.

    ![제품 범위](./media/api-management-howto-policies/product-scope.png)

2. 정책을 적용하려는 제품을 선택합니다.
3. **정책**을 클릭합니다.
4. 정책을 추가하거나 편집합니다.
5. **저장**을 누릅니다. 

### <a name="api-scope"></a>API 범위

API 범위는 선택한 API의 **모든 작업**에 대해 구성됩니다.

1. 정책을 적용하려는 **API**를 선택합니다.

    ![API 범위](./media/api-management-howto-policies/api-scope.png)

2. **모든 작업**을 선택합니다.
3. 삼각형 아이콘을 클릭합니다.
4. **코드 편집기**를 선택합니다.
5. 정책을 추가하거나 편집합니다.
6. **저장**을 누릅니다. 

### <a name="operation-scope"></a>작업 범위 

작업 범위는 선택한 작업에 대해 구성됩니다.

1. **API**를 선택합니다.
2. 정책을 적용하려는 작업을 선택합니다.

    ![작업 범위](./media/api-management-howto-policies/operation-scope.png)

3. 삼각형 아이콘을 클릭합니다.
4. **코드 편집기**를 선택합니다.
5. 정책을 추가하거나 편집합니다.
6. **저장**을 누릅니다. 

## <a name="next-steps"></a>다음 단계

다음 관련 항목을 참조하세요.

+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)