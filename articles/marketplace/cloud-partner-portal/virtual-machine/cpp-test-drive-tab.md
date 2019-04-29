---
title: Cloud 파트너 포털의 Azure Marketplace에 대한 가상 머신 시험 사용 탭 | Microsoft Docs
description: Azure Marketplace VM 제안을 만드는 데 사용되는 시험 사용 탭에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344622"
---
# <a name="virtual-machine-test-drive-tab"></a>가상 머신 시험 사용 탭

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

**새 제안** 페이지의 **시험 사용** 탭을 사용하면 자기 주도형 실습 데모를 통해 잠재 고객에게 표준화된 시나리오에서 설명된 제품의 주요 기능과 이점을 직접 소개할 수 있습니다.  시험 사용은 시험 사용을 지원하는 제안 유형에 대한 선택적 기능입니다.  시험 사용을 사용하려면 지원 자산이 올바르게 구현되어야 합니다.  자세한 내용은 [Azure Marketplace 시험 사용](https://azure.microsoft.com/blog/azure-marketplace-test-drive/) 문서를 참조하세요.  <!--TD: Replace with migrated version of Test Drive article! -->

이 기능을 사용하도록 설정하려면 **시험 사용** 탭에서 **시험 사용 활성화**에서 **예** 옵션을 클릭합니다.  **시험 사용** 탭에 편집 가능한 필드가 표시됩니다.  필드 이름에 별표(*)가 추가되면 필수 항목임을 나타냅니다.

![가상 머신에 대한 새 제안 양식의 시험 사용 탭](./media/publishvm_007.png)

<br/>

다음 표에서는 이러한 필드의 용도와 내용을 설명합니다.


|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
|  *세부 정보*   |  |
| **설명**           | 시험 사용 시나리오에 대한 개요를 제공합니다. 이 텍스트는 시험 사용을 프로비전하는 동안 사용자에게 표시됩니다. 서식 있는 콘텐츠를 제공하려는 경우 이 필드는 기본 HTML을 지원합니다.  |
| **사용자 설명서**           | 시험 사용 사용자가 솔루션을 사용하는 방법을 이해하는 데 도움이 되는 자세한 사용자 설명서(.pdf)를 업로드합니다.  |
| **시험 사용 데모 비디오** | 솔루션을 보여 주는 비디오를 업로드합니다.  이 옵션을 선택한 경우 비디오에 대한 이름, URL(YouTube 또는 Vimeo에서 호스팅됨) 및 썸네일(533x324 픽셀)을 제공해야 합니다. |
| *기술 구성* |  |
| **인스턴스**             | VM 인스턴스의 지역 가용성 및 상대적 가용성을 지정합니다(자세한 내용은 정보 아이콘 클릭).  <br/>잠재적인 동시 시험 사용 세션은 구독에 대한 할당량 한도를 초과하지 않아야 합니다.  전자는 다음과 같이 계산됩니다. [선택한 지역 수 x 핫 인스턴스 수] + [선택한 지역 수 x 웜 인스턴스 수] + [선택한 지역 수 x 콜드 인스턴스 수] |
| **시험 사용 기간**   | 최대 세션 기간(시간)입니다. 이 기간이 초과되면 시험 사용 세션이 자동으로 종료됩니다.  |
|**시험 사용 ARM 템플릿**| 이 시험 사용과 연결된 Azure Resource Manager 템플릿을 업로드합니다. 자세한 내용은 [시험 사용을 위한 Virtual Machine 배포 템플릿 변환](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)을 참조하세요. |
| **액세스 정보**    | 일반 텍스트 또는 단순 HTML 형식으로 작성된 Azure Resource Manager 액세스 및 시험판 로그인 정보입니다. |
| *시험 사용 배포 구독 세부 정보* |  |
| **Azure 구독 ID** | 이 ID는 [Microsoft Azure Portal](https://ms.portal.azure.com)에 로그인하고, 왼쪽 메뉴 모음에서 **구독**을 클릭하여 가져올 수 있습니다 (예: "a83645ac-1234-5ab6-6789-1h234g764ghty"). 이 식별자는 `a83645ac-1234-5ab6-6789-1h234g764ghty` 형식의 GUID여야 합니다.|
| **Azure AD 테넌트 ID**    | Azure Active Directory 테넌트 ID입니다.  이 ID는 [Microsoft Azure Portal](https://ms.portal.azure.com)에 로그인하고, 왼쪽 메뉴 모음에서 **Azure Active Directory**를 클릭하고, 중간 메뉴 모음에서 **속성**을 클릭한 다음, 양식의 **디렉터리 ID**를 복사하여 가져올 수 있습니다.  이 식별자도 GUID여야 합니다.  비워 두면 조직에 대한 테넌트 ID를 만들어야 합니다. |
| **Azure AD 앱 ID**       | 등록된 Azure VM 솔루션에 대한 식별자입니다.  |
| **Azure AD 앱 키**      | 등록된 솔루션에 대한 인증 키입니다. |
|  |  |

<br/>

다음으로, [Marketplace](./cpp-marketplace-tab.md) 탭에서 솔루션에 대한 마케팅 및 법률 정보를 제공합니다.
