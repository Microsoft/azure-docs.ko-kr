---
title: 서비스 엔드포인트 정책 만들기 및 연결 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Portal을 사용하여 서비스 엔드포인트 정책을 설정 및 연결하는 방법에 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: aaa07759ed8b10578d024d5838ac1d2658778695
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034643"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트를 만들기, 변경 또는 삭제

서비스 엔드포인트 정책을 사용하면 서비스 엔드포인트를 통해 특정 Azure 리소스에 가상 네트워크 트래픽을 필터링 할 수 있습니다. 서비스 엔드포인트 정책을 잘 모르는 경우 [서비스 엔드포인트 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조하여 자세히 알아보세요.

 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서비스 엔드포인트 정책 만들기
> * 서비스 엔드포인트 정책 정의 만들기
> * 서브넷을 사용하여 가상 네트워크 만들기
> * 서비스 엔드포인트 정책을 서브넷에 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

 [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-service-endpoint-policy"></a>서비스 엔드포인트 정책 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. 검색 창에 "서비스 엔드포인트 정책"을 입력하고 **서비스 엔드포인트 정책(미리 보기)** 를 선택한 다음, **만들기**를 선택합니다.
3. **기본**에 다음 정보를 입력하거나 선택 

   - 구독: 정책에 대한 사용자의 구독을 선택합니다.    
   - 리소스 그룹: **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.     
   - 이름: myEndpointPolicy
   - 위치: 미국 중서부     
 
   ![서비스 엔드포인트 정책 기본 만들기](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. **정책 정의**에 다음 정보를 입력하거나 선택

   - **+ 리소스 추가**를 클릭하고 다음 정보를 입력하거나 선택하고, 나머지 설정에 대해 기본값을 적용하고 **추가**를 클릭합니다.  
   - 범위: **단일 계정**, **구독의 모든 계정** 또는 **리소스 그룹의 모든 계정**을 선택합니다.    
   - 구독: 스토리지 계정에 대한 구독을 선택합니다. 정책 및 저장소 계정은 다른 구독에 있을 수 있습니다.   
   - 리소스 그룹: 리소스 그룹을 선택합니다. 범위가 "리소스 그룹의 모든 계정" 또는 "단일 계정"으로 설정된 경우 필요합니다.  
   - 리소스: mystorageaccountportal    
   - **+ 리소스 추가**를 클릭하여 계속 더 많은 리소스를 추가합니다.
   
   ![서비스 엔드포인트 정책 정의 만들기](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. 선택 사항: **태그**에 다음 정보를 입력하거나 선택:
   
   - 키: 정책에 대한 키를 선택합니다. 예: 부서     
   - 값: 키에 대한 값 쌍을 입력합니다. 예: 재무

6. **검토 + 만들기**를 선택합니다. 정보의 유효성을 검사하고 **만들기**를 클릭합니다. 추가로 편집하려면 **이전**을 클릭합니다. 

   ![서비스 엔드포인트 정책 최종 유효성 검사 만들기](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>엔드포인트 정책 보기 

1. 포털의 *모든 서비스* 상자에 *서비스 엔드포인트 정책* 입력을 시작합니다. **서비스 엔드포인트 정책(미리 보기)** 를 선택합니다.
2. 다음 그림에 표시된 것처럼 **구독**에서 구독 및 리소스 그룹 선택

   ![정책 표시](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. 더 많은 정책 정의를 보거나 추가하려면 정책을 선택하고 **정책 정의**를 클릭합니다.

   ![정책 정의 표시](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. **연결된 서브넷**을 선택하여 정책이 연결된 서브넷을 확인합니다. 서브넷에 정책을 연결하려면 "동일한 지역의 가상 네트워크로 이동"을 클릭합니다.

   ![연결된 서브넷 표시](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>서브넷에 정책 연결

>[!WARNING] 
> 선택한 서비스의 경우 정책을 연결하기 전에 서브넷에서 액세스된 모든 리소스를 정책에 추가했는지 확인합니다. 정책이 연결되면 서비스에 대한 엔드포인트 지역의 경우 정책에 나열된 리소스에 대한 액세스만 허용됩니다. 

서브넷에 정책을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. 그래야 서브넷에 정책을 연결할 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.
   - Name: myVirtualNetwork      
   - 주소 공간: 10.0.0.0/16      
   - 구독: 구독을 선택합니다. 정책은 VNet과 동일한 구독에 있어야 합니다.     
   - 리소스 그룹: **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.     
   - 위치: 미국 중서부     
   - 서브넷 이름: 개인     
   - 주소 범위: 10.0.0.0/24
     
4. 포털 맨 위에 있는 **리소스, 서비스 및 문서 검색** 상자에 *myVirtualNetwork*를 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
5. **SETTINGS**에서 **서브넷**을 선택한 다음, **개인**을 선택합니다.
6. 다음 그림에 표시된 것처럼 **서비스 엔드포인트**, **Microsoft.Storage**, **서비스 엔드포인트 정책**,  **myEndpointPolicy** 및 **저장**을 차례로 선택합니다.

   ![연결 정책](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>NSG(네트워크 보안 그룹)에 따라 이 서브넷에서 다른 지역의 서비스 리소스에 대한 액세스가 허용됩니다. 엔드포인트 지역에 대한 액세스만 제한하려면 NSG를 엔드포인트 지역의 서비스 트래픽으로만 제한합니다. 지역별 서비스 태그를 사용하여 NSG를 만드는 방법에 대한 자세한 내용은 [NSG Azure 서비스 태그](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)를 참조하세요.

아래 예제에서 NSG는 우선 순위가 낮은 규칙으로 "모두 거부" 규칙에 따라 WestCentralUS 및 WestUS2의 Azure Storage 리소스에 대한 액세스만 제한됩니다.

![모든 NSG 거부](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 서비스 엔드포인트 정책을 만들어 서브넷에 연결했습니다. 서비스 엔드포인트 정책에 대한 자세한 내용은 [서비스 엔드포인트 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조하세요.

