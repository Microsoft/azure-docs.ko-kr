---
title: Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포 | Microsoft Docs
description: Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 413e449f005d288f66da0257f5b800733e58eb94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836588"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포
이 문서에서는 SAP CAL(SAP 클라우드 어플라이언스 라이브러리) 3.0을 통해 Azure에서 SQL Server 및 Windows OS와 함께 실행되는 SAP IDES 시스템을 배포하는 방법을 설명합니다. 스크린샷은 단계별 프로세스를 보여 줍니다. 다른 솔루션을 배포하려면 동일한 단계를 수행합니다.

SAP CAL을 시작하려면 [SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/) 웹 사이트로 이동합니다. SAP에는 새로운 [SAP 클라우드 어플라이언스 라이브러리 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)에 대한 블로그가 있습니다. 

> [!NOTE]
> 2017년 5월 29일 기준으로 SAP CAL을 배포하는 데 덜 선호되는 클래식 배포 모델 외에도 Azure Resource Manager 배포 모델을 사용할 수 있습니다. 새 리소스 관리자 배포 모델을 사용하고 클래식 배포 모델을 무시하는 것이 좋습니다.

클래식 모델을 사용하는 SAP CAL 계정을 이미 만든 경우 *다른 SAP CAL 계정을 만들어야 합니다*. 이 계정은 리소스 관리자 모델을 사용하여 단독으로 Azure에 배포해야 합니다.

SAP CAL에 로그인한 후 첫 번째 페이지는 일반적으로 **솔루션** 페이지로 이동할 수 있도록 해 줍니다. SAP CAL에서 제공되는 솔루션은 계속 증가하므로 원하는 솔루션을 찾으려면 상당히 스크롤해야 할 수도 있습니다. Azure에서 단독으로 사용할 수 있는 강조 표시된 Windows 기반 SAP IDE 솔루션은 배포 프로세스를 보여 줍니다.

![SAP CAL 솔루션](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL에서 계정 만들기
1. SAP CAL에 처음으로 로그인하려면 SAP S-사용자 또는 SAP로 등록된 다른 사용자를 사용합니다. 그런 다음 SAP CAL에서 Azure에 어플라이언스를 배포하는 데 사용되는 SAP CAL 계정을 정의합니다. 계정 정의에서 다음을 수행해야 합니다.

    a. Azure에서 배포 모델을 선택합니다(리소스 관리자 또는 클래식).

    b. Azure 구독을 입력합니다. SAP CAL 계정은 하나의 구독에만 할당될 수 있습니다. 둘 이상의 구독이 필요한 경우 다른 SAP CAL 계정을 만들어야 합니다.
    
    다. Azure 구독에 배포하도록 SAP CAL 권한을 부여 합니다.

   > [!NOTE]
   >  다음 단계에서는 리소스 관리자 배포를 위한 SAP CAL 계정을 만드는 방법을 보여 줍니다. 클래식 배포 모델에 연결된 SAP CAL 계정이 이미 있는 경우 다음 단계를 따라 새 SAP CAL 계정을 *만들어야* 합니다. 새 SAP CAL 계정을 리소스 관리자 모델에 배포해야 합니다.

1. 새 SAP CAL 계정을 만들기 위해 **계정** 페이지는 Azure에 대한 두 가지 선택 항목을 표시합니다. 

    a. **Microsoft Azure(클래식)** 는 클래식 배포 모델이며 더 이상 선호하지 않습니다.

    b. **Microsoft Azure**는 새 리소스 관리자 배포 모델입니다.

    ![SAP CAL 계정](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    리소스 관리자 모델을 배포하려면 **Microsoft Azure**를 선택합니다.

    ![SAP CAL 계정](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Azure Portal에서 찾을 수 있는 Azure **구독 ID**를 입력합니다. 

    ![SAP CAL 구독 ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. 정의한 Azure 구독에 배포하도록 SAP CAL에 권한을 부여하려면 **권한 부여**를 클릭합니다. 다음 페이지가 브라우저 탭에 나타납니다.

    ![Internet Explorer 클라우드 서비스 로그인](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. 둘 이상의 사용자가 나열되는 경우 선택한 Azure 구독의 공동 관리자가 되도록 연결된 Microsoft 계정을 선택합니다. 다음 페이지가 브라우저 탭에 나타납니다.

    ![Internet Explorer 클라우드 서비스 확인](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. **Accept**를 클릭합니다. 권한 부여에 성공한 경우 SAP CAL 계정 정의가 다시 표시됩니다. 짧은 시간 후 메시지에서 권한 부여 프로세스 성공적으로 수행되었는지 확인합니다.

1. 새로 만든 SAP CAL 계정을 사용자에게 할당하려면 오른쪽의 텍스트 상자에 **사용자 ID**를 입력하고 **추가**를 클릭합니다. 

    ![사용자 연결에 대한 계정](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. SAP CAL에 로그인하는 데 사용하는 사용자로 계정을 연결하려면 **검토**를 클릭합니다. 

1. 사용자와 새로 만든 SAP CAL 계정 간의 연결을 만들려면 **만들기**를 클릭합니다.

    ![계정 연결에 대한 사용자](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

다음을 할 수 있는 SAP CAL 계정을 성공적으로 만들었습니다.

- 리소스 관리자 배포 모델을 사용합니다.
- Azure 구독에 SAP 시스템을 배포합니다.

> [!NOTE]
> Windows 및 SQL Server를 기반으로 SAP IDE 솔루션을 배포하려면 먼저 SAP CAL 구독에 등록해야 합니다. 그렇지 않은 경우 솔루션은 개표 페이지에 **잠금**으로 표시될 수 있습니다.

### <a name="deploy-a-solution"></a>솔루션 배포
1. SAP CAL 계정을 설정한 후 **Windows 및 SQL Server의 SAP IDE 솔루션** 솔루션을 선택합니다. **인스턴스 만들기**를 클릭하고 사용 및 약관을 확인합니다. 

1. **기본 모드: 인스턴스 만들기** 페이지에서 다음을 수행해야 합니다.

    a. 인스턴스 **이름**을 입력합니다.

    b. Azure **지역**을 선택합니다. 제공된 여러 Azure 지역을 가져오려면 SAP CAL 구독이 필요할 수 있습니다.

    다.  표시된 것처럼 솔루션에 대한 마스터 **암호**를 입력합니다.

    ![SAP CAL 기본 모드: 인스턴스 만들기](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. **만들기**를 클릭합니다. 솔루션의 크기 및 복잡성에 따라 잠시 후에(SAP CAL에서 예측값 제공) 상태가 활성으로 표시되고 사용할 준비가 됩니다. 

    ![SAP CAL 인스턴스](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. 리소스 그룹 및 SAP CAL에서 생성된 모든 해당 개체를 찾으려면 Azure Portal로 이동합니다. 가상 머신이 SAP CAL에 지정된 것과 동일한 인스턴스 이름으로 시작하는 것을 확인할 수 있습니다.

    ![리소스 그룹 개체](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. SAP CAL 포털에서 배포된 인스턴스로 이동하고 **연결**을 클릭합니다. 다음과 같은 팝업 창이 나타납니다. 

    ![인스턴스에 연결](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. 배포된 시스템에 연결하는 옵션 중 하나를 사용하려면 **시작 가이드**를 클릭합니다. 설명서는 각 연결 방법에 대한 사용자 이름을 지정합니다. 해당 사용자에 대한 암호는 배포 프로세스의 시작 부분에서 정의한 마스터 암호로 설정됩니다. 설명서에서 배포된 시스템에 로그인하는 데 사용할 수 있는 암호와 함께 더 많은 기능 사용자가 나열됩니다.

    ![SAP 시작 설명서](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

몇 시간 이내에 정상 SAP IDES 시스템이 Azure에 배포됩니다.

SAP CAL 구독을 구입한 경우 SAP는 Azure에서 SAP CAL을 통해 배포를 완벽하게 지원합니다. 지원 큐는 BC-VCM-CAL입니다.

