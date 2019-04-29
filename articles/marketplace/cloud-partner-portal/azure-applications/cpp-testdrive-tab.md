---
title: Azure 애플리케이션 제품 시험 사용 | Microsoft Docs
description: Azure Marketplace의 Azure 애플리케이션 제품에 대한 시험 사용을 구성하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744728"
---
# <a name="azure-applications-test-drive-tab"></a>Azure 애플리케이션 시험 사용 탭

시험 사용 탭을 사용하여 고객에게 체험 환경을 제공합니다.

## <a name="test-drive-benefits"></a>시험 사용의 이점

고객이 확신을 가지고 구매할 수 있도록 고객의 평가판 환경을 만드는 것이 좋습니다. 사용 가능한 평가판 옵션 중에서 시험 사용은 우수한 잠재 고객 생성 및 이러한 잠재 고객의 전환율을 높이는 데 가장 효과적입니다.

시험 사용은 실제 구현 시나리오에서 입증된 제품의 주요 기능 및 이점을 고객이 직접 체험할 수 있는 평가판을 제공합니다.

## <a name="how-a-test-drive-works"></a>시험 사용의 작동 방식

잠재 고객은 Marketplace에서 애플리케이션을 검색합니다. 고객은 로그인하여 사용 약관에 동의합니다. 이때 고객은 일정 시간 동안 사용해 보도록 미리 구성된 환경을 받고 여러분은 향후 연락할 우수한 잠재 고객을 얻게 됩니다. 자세한 내용은 [시험 사용이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)을 참조하세요.

## <a name="setting-up-a-test-drive"></a>시험 사용 설정

다음 단계에 따라 시험 사용을 사용하도록 설정하고 구성합니다.

### <a name="to-enable-a-test-drive"></a>시험 사용을 사용하도록 설정하려면:

1. **새 제품** 아래에서 **시험 사용** 탭을 선택합니다.
2. **시험 사용**에서 **시험 사용**에 대해 **예**를 선택합니다.

   ![시험 사용을 사용하도록 설정](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>시험 사용을 구성하려면

시험 사용을 사용하도록 설정한 후에는 다음 양식을 작성하여 시험 사용을 설정합니다.
  
 - 세부 정보
 - 기술 정보 구성
 - 시험 사용 배포 구독 세부 정보

다음 화면 캡처는 모든 시험 사용 형태를 보여 줍니다. 필드 이름에 추가된 별표(*)는 필수를 나타냅니다. 

![시험 사용 구성](./media/managed-app-configure-testdrive.png)

다음 표에서는 관리되는 애플리케이션에 대해 시험 사용을 설정하는 데 필요한 필드를 설명합니다.

|    **필드**       |  **설명**  |
|  ---------------   |  ---------------  |
|      설명              |   시험 사용에서 수행할 수 있는 작업을 설명합니다. 기본 HTML 태그를 사용하여 이 설명의 서식을 지정할 수 있습니다. 예: &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; 및 제목                |
|    사용자 설명서                |     고객이 시험 사용 환경을 진행하는 데 사용할 수 있는 사용자 설명서를 업로드합니다. 이 문서는 .pdf 파일이어야 합니다.              |
|         시험 사용 데모 비디오(선택 사항)           |       시험 사용에 대한 연습 비디오를 제공할 수 있습니다. 고객은 시험 사용을 진행하기 전에 이 비디오를 시청할 수 있습니다. YouTube 또는 Vimeo에 비디오 URL을 제공합니다. **+비디오 추가**를 선택하면 다음 정보를 제공하라는 메시지가 표시됩니다.<ul><li>이름</li><li>URL</li><li>썸네일(PNG 형식, 533x324픽셀)</li></ul>            |
|       인스턴스             |        원하는 인스턴스 수, 해당 지역 및 고객이 시험 사용을 가져올 수 있는 속도를 구성합니다. 자세한 내용은 [시험 사용을 게시하는 방법](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)을 참조하세요.           |
|       시험 사용 기간(시)             |       시간을 정수로 입력합니다. 허용되는 범위는 1에서 999 사이입니다.            |
|        시험 사용 ARM 템플릿            |        앱용 Azure Resource Manager 템플릿을 포함하는 압축(.zip) 파일을 업로드합니다. 자세한 내용은 [Azure Resource Manager 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)을 참조하세요.            |
|        액세스 정보            |         고객이 시험 사용을 가져오면 액세스 정보를 제공합니다. 예를 들어 시험 사용에 액세스하기 위한 URL, 로그인 정보가 여기에 해당합니다. . 기본 HTML 태그를 사용하여 이 설명의 서식을 지정할 수 있습니다. 예: &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; 및 제목          |
|       액세스 구독 ID             |       Azure 서비스 및 Azure Portal 액세스 권한을 부여합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. 별도의 시험 사용 전용 Azure 구독이 없으면 구독을 만듭니다.             |
|          Azure AD 테넌트 ID          |        Azure Active Directory의 기존 테넌트를 제공하거나 이 시험 사용을 위한 테넌트를 만듭니다.           |
|         Azure AD 앱 ID           |       새 애플리케이션을 만들고 등록합니다. Microsoft는 이 애플리케이션을 사용하여 시험 사용 인스턴스에 대한 작업을 수행합니다.            |
|          Azure AD 앱 키          |         앱에 대한 인증 키를 만들고 이 필드에 붙여 넣습니다.          |

필요한 모든 정보를 제공한 후에 **저장**을 선택하여 시험 사용 설정을 완료합니다.

## <a name="next-steps"></a>다음 단계

[Marketplace 탭](./cpp-marketplace-tab.md)