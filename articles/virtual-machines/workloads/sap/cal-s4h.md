---
title: Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포 | Microsoft Docs
description: Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c59fcf43cb4767f1d95d769dfce4d5c8755e45ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836860"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Azure에서 SAP S/4HANA 또는 BW/4HANA 배포
이 문서에서는 SAP CAL(SAP 클라우드 어플라이언스 라이브러리) 3.0을 통해 Azure에서 S/4HANA를 배포하는 방법을 설명합니다. BW/4HANA와 같은 다른 SAP HANA 기반 솔루션을 배포하려면 동일한 단계를 수행합니다.

> [!NOTE]
> SAP CAL에 대한 자세한 내용은 [SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/) 웹 사이트로 이동하세요. SAP에는 [SAP 클라우드 어플라이언스 라이브러리 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)에 대한 블로그도 있습니다.
> 
> [!NOTE]
> 2017년 5월 29일 기준으로 SAP CAL을 배포하는 데 덜 선호되는 클래식 배포 모델 외에도 Azure Resource Manager 배포 모델을 사용할 수 있습니다. 새 리소스 관리자 배포 모델을 사용하고 클래식 배포 모델을 무시하는 것이 좋습니다.

## <a name="step-by-step-process-to-deploy-the-solution"></a>솔루션을 배포하는 단계별 프로세스

다음과 같은 일련의 스크린샷은 Azure에서 SAP CAL을 사용하여 S/4HANA를 배포하는 방법을 보여 줍니다. 이 프로세스는 BW/4HANA와 같은 다른 솔루션과 동일한 방식으로 작동합니다.

**솔루션** 페이지에서는 Azure에서 사용할 수 있는 일부 SAP CAL HANA 기반 솔루션을 보여 줍니다. **SAP S/4HANA 1610 FPS01, 완전히 활성화된 어플라이언스**는 중간 행에 있습니다.

![SAP CAL 솔루션](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL에서 계정 만들기
1. SAP CAL에 처음으로 로그인하려면 SAP S-사용자 또는 SAP로 등록된 다른 사용자를 사용합니다. 그런 다음 SAP CAL에서 Azure에 어플라이언스를 배포하는 데 사용되는 SAP CAL 계정을 정의합니다. 계정 정의에서 다음을 수행해야 합니다.

    a. Azure에서 배포 모델을 선택합니다(리소스 관리자 또는 클래식).

    b. Azure 구독을 입력합니다. SAP CAL 계정은 하나의 구독에만 할당될 수 있습니다. 둘 이상의 구독이 필요한 경우 다른 SAP CAL 계정을 만들어야 합니다.

    다. Azure 구독에 배포하도록 SAP CAL 권한을 부여 합니다.

   > [!NOTE]
   >  다음 단계에서는 리소스 관리자 배포를 위한 SAP CAL 계정을 만드는 방법을 보여 줍니다. 클래식 배포 모델에 연결된 SAP CAL 계정이 이미 있는 경우 다음 단계를 따라 새 SAP CAL 계정을 *만들어야* 합니다. 새 SAP CAL 계정을 리소스 관리자 모델에 배포해야 합니다.

1. 새 SAP CAL 계정을 만듭니다. **계정** 페이지는 Azure에 대한 세 가지 선택 항목을 보여 줍니다. 

    a. **Microsoft Azure(클래식)** 는 클래식 배포 모델이며 더 이상 선호하지 않습니다.

    b. **Microsoft Azure**는 새 리소스 관리자 배포 모델입니다.

    다. **21Vianet에서 운영하는 Windows Azure**는 클래식 배포 모델을 사용하는 중국의 옵션입니다.

    리소스 관리자 모델을 배포하려면 **Microsoft Azure**를 선택합니다.

    ![SAP CAL 계정 세부 정보](./media/cal-s4h/s4h-pic-2a.png)

1. Azure Portal에서 찾을 수 있는 Azure **구독 ID**를 입력합니다.

   ![SAP CAL 계정](./media/cal-s4h/s4h-pic3c.png)

1. 정의한 Azure 구독에 배포하도록 SAP CAL에 권한을 부여하려면 **권한 부여**를 클릭합니다. 다음 페이지가 브라우저 탭에 나타납니다.

   ![Internet Explorer 클라우드 서비스 로그인](./media/cal-s4h/s4h-pic4c.png)

1. 둘 이상의 사용자가 나열되는 경우 선택한 Azure 구독의 공동 관리자가 되도록 연결된 Microsoft 계정을 선택합니다. 다음 페이지가 브라우저 탭에 나타납니다.

   ![Internet Explorer 클라우드 서비스 확인](./media/cal-s4h/s4h-pic5a.png)

1. **Accept**를 클릭합니다. 권한 부여에 성공한 경우 SAP CAL 계정 정의가 다시 표시됩니다. 짧은 시간 후 메시지에서 권한 부여 프로세스 성공적으로 수행되었는지 확인합니다.

1. 새로 만든 SAP CAL 계정을 사용자에게 할당하려면 오른쪽의 텍스트 상자에 **사용자 ID**를 입력하고 **추가**를 클릭합니다.

   ![사용자 연결에 대한 계정](./media/cal-s4h/s4h-pic8a.png)

1. SAP CAL에 로그인하는 데 사용하는 사용자로 계정을 연결하려면 **검토**를 클릭합니다. 
 
1. 사용자와 새로 만든 SAP CAL 계정 간의 연결을 만들려면 **만들기**를 클릭합니다.

   ![SAP CAL 계정 연결에 대한 사용자](./media/cal-s4h/s4h-pic9b.png)

다음을 할 수 있는 SAP CAL 계정을 성공적으로 만들었습니다.

- 리소스 관리자 배포 모델을 사용합니다.
- Azure 구독에 SAP 시스템을 배포합니다.

이제 Azure에서 사용자 구독에 S/4HANA 배포를 시작할 수 있습니다.

> [!NOTE]
> 계속하기 전에 Azure H 시리즈 VM에 대한 Azure vCPU 할당량이 있는지 확인합니다. 현재 SAP CAL은 Azure의 H 시리즈 VM을 사용하여 일부 SAP HANA 기반 솔루션을 배포합니다. Azure 구독은 H 시리즈에 대한 H 시리즈 vCPU 할당량이 없을 수 있습니다. 이 경우 최소한 16 H 시리즈 vCPU의 할당량을 가져오도록 Azure 지원에 문의해야 합니다.
> 
> [!NOTE]
> SAP CAL에서 Azure에 솔루션을 배포하는 경우 Azure 지역 하나만 선택할 수 있는 경우가 있습니다. SAP CAL에서 제안한 것과 다른 Azure 지역에 배포하려면 SAP에서 CAL 구독을 구입해야 합니다. 또한 CAL 계정이 처음 제안된 곳 외의 Azure 지역에 제공할 수 있도록 SAP로 메시지를 열어야 할 수 있습니다.

### <a name="deploy-a-solution"></a>솔루션 배포

SAP CAL의 **솔루션** 페이지에서 솔루션을 배포해 보겠습니다. SAP CAL에는 배포를 위한 두 개의 시퀀스가 있습니다.

- 배포될 시스템을 정의하도록 하나의 페이지를 사용하는 기본 시퀀스
- VM 크기에 특정 선택 사항을 제공하는 고급 시퀀스 

여기에서 배포에 대한 기본 경로를 설명합니다.

1. **계정 세부 정보** 페이지에서 다음을 수행해야 합니다.

    a. SAP CAL 계정을 선택합니다. (리소스 관리자 배포 모델을 사용하여 배포하도록 연결된 계정을 사용합니다.)

    b. 인스턴스 **이름**을 입력합니다.

    다. Azure **지역**을 선택합니다. SAP CAL은 지역을 제안합니다. 다른 Azure 지역이 필요하고 SAP CAL 구독이 없는 경우 SAP로 CAL 구독을 주문해야 합니다.

    d. 8자 또는 9자로 솔루션에 대한 마스터 **암호**를 입력합니다. 암호는 다양한 구성 요소의 관리자에서 사용됩니다.

   ![SAP CAL 기본 모드: 인스턴스 만들기](./media/cal-s4h/s4h-pic10a.png)

1. **만들기**를 클릭하고 나타나는 메시지 상자에서 **확인**을 클릭합니다.

   ![SAP CAL 지원되는 VM 크기](./media/cal-s4h/s4h-pic10b.png)

1. **개인 키** 대화 상자에서 **저장**을 클릭하여 SAP CAL에 개인 키를 저장합니다. 개인 키에 대해 암호 보호를 사용하려면 **다운로드**를 클릭합니다. 

   ![SAP CAL 개인 키](./media/cal-s4h/s4h-pic10c.png)

1. SAP CAL **경고** 메시지를 읽고 **확인**을 클릭합니다.

   ![SAP CAL 경고](./media/cal-s4h/s4h-pic10d.png)

    이제 배포가 수행됩니다. 솔루션의 크기 및 복잡성에 따라 잠시 후에(SAP CAL에서 예측값 제공) 상태가 활성으로 표시되고 사용할 준비가 됩니다.

1. 하나의 리소스 그룹에서 다른 연결된 리소스를 사용하여 수집된 가상 머신을 찾으려면 Azure Portal로 이동합니다. 

   ![새 포털에 배포된 SAP CAL 개체](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. SAP CAL 포털에서 상태는 **활성**으로 나타납니다. 솔루션에 연결하려면 **연결**을 클릭합니다. 다양한 구성 요소에 연결하기 위한 다른 옵션이 이 솔루션 내에서 배포됩니다.

   ![SAP CAL 인스턴스](./media/cal-s4h/active_solution.png)

1. 배포된 시스템에 연결하는 옵션 중 하나를 사용하려면 **시작 가이드**를 클릭합니다. 

   ![인스턴스에 연결](./media/cal-s4h/connect_to_solution.png)

    설명서는 각 연결 방법에 대한 사용자 이름을 지정합니다. 해당 사용자에 대한 암호는 배포 프로세스의 시작 부분에서 정의한 마스터 암호로 설정됩니다. 설명서에서 배포된 시스템에 로그인하는 데 사용할 수 있는 암호와 함께 더 많은 기능 사용자가 나열됩니다. 

    예를 들어 Windows 원격 데스크톱 컴퓨터에 미리 설치되어 있는 SAP GUI를 사용하는 경우 S/4 시스템은 다음과 같이 표시될 수 있습니다.

   ![사전 설치된 SAP GUI의 SM50](./media/cal-s4h/gui_sm50.png)

    또는 DBACockpit을 사용하는 경우 인스턴스는 다음과 같이 표시될 수 있습니다.

   ![DBACockpit SAP GUI의 SM50](./media/cal-s4h/dbacockpit.png)

몇 시간 이내에 정상 SAP S/4 어플라이언스가 Azure에 배포됩니다.

SAP CAL 구독을 구입한 경우 SAP는 Azure에서 SAP CAL을 통해 배포를 완벽하게 지원합니다. 지원 큐는 BC-VCM-CAL입니다.







