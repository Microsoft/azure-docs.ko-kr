---
title: StorSimple Virtual Array를 위한 포털 준비 | Microsoft Docs
description: StorSimple 가상 배열을 배포하는 첫 번째 자습서에는 Azure 포털 준비가 포함됩니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6f0a6371b38f0271237db0f7d80b831ecc145c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127143"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple 가상 배열 배포 – Azure Portal 준비

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>개요

Resource Manager 모델을 사용하여 가상 배열을 파일 서버 또는 iSCSI 서버로 완전히 배포하는 데 필요한 배포 자습서 시리즈의 첫 번째 문서입니다. 이 문서는 가상 배열을 프로비전하기 전에 StorSimple 디바이스 관리자 서비스를 만들고 구성하는 데 필요한 준비 사항을 설명합니다. 이 문서는 배포 구성 검사 목록 및 필수 조건과도 연결됩니다.

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 시작하기 전에 배포 구성 검사 목록을 검토하는 것이 좋습니다. 포털 준비에는 10분 미만이 소요됩니다.

이 문서에 게시된 정보는 Azure Portal 및 Microsoft Azure Government 클라우드에서 StorSimple 가상 배열의 배포에 적용됩니다.

### <a name="get-started"></a>시작하기
배포 워크플로는 포털 준비, 가상화된 환경에 가상 배열 프로비전, 설치 완료로 구성됩니다. 파일 서버 또는 iSCSI 서버로 StorSimple 가상 배열 배포를 시작하려면 다음 표에 준비된 리소스를 참고해야 합니다.

#### <a name="deployment-articles"></a>배포 문서

StorSimple 가상 배열을 배포하려면 다음 문서를 지정된 순서대로 참조하세요.

| **#** | **단계** | **이 작업을 수행합니다…** | **그리고 이러한 문서를 사용합니다.** |
| --- | --- | --- | --- |
| 1. |**Azure Portal 설정** |StorSimple 가상 배열을 프로비전하기 전에 StorSimple 디바이스 관리자 서비스를 만들고 구성합니다. |[포털 준비](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**가상 배열 프로비전** |Hyper-V의 경우 Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2에서 Hyper-V를 실행하는 호스트 시스템의 StorSimple 가상 배열을 프로비전하고 연결합니다. <br></br> <br></br> VMware의 경우 VMware ESXi 5.0, 5.5, 6.0 또는 6.5를 실행하는 호스트 시스템에서 StorSimple 가상 배열을 프로비전하고 해당 배열에 연결합니다.<br></br> |[Hyper-V에서 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware에서 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**가상 배열 설정** |파일 서버에 대해 초기 설정을 수행하고 StorSimple 파일 서버를 등록하고 디바이스 설정을 완료합니다. 그런 다음 SMB 공유를 프로비전할 수 있습니다. <br></br> <br></br> iSCSI 서버에 대해 초기 설정을 수행하고 StorSimple iSCSI 서버를 등록하고 디바이스 설정을 완료합니다. 그런 다음 iSCSI 볼륨을 프로비전할 수 있습니다. |[파일 서버로 가상 배열 설정](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[iSCSI 서버로 가상 배열 설정](storsimple-virtual-array-deploy3-iscsi-setup.md) |

이제 Azure Portal 설치를 시작할 수 있습니다.

## <a name="configuration-checklist"></a>구성 검사 목록

구성 검사 목록에서는 StorSimple 가상 배열에 소프트웨어를 구성하기 전에 수집해야 하는 정보를 설명합니다. 이 정보를 미리 준비하면 사용자 환경에서 StorSimple 디바이스를 배포하는 과정을 간소화하는 데 도움이 됩니다. StorSimple 가상 배열을 파일 서버로 배포할지 iSCSI 서버로 배포할지에 따라서 다음 검사 목록 중 하나가 필요합니다.

* [StorSimple 가상 배열 파일 서버 구성 검사 목록](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)을 다운로드합니다.
* [StorSimple 가상 배열 iSCSI 서버 구성 검사 목록](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)을 다운로드합니다.

## <a name="prerequisites"></a>필수 조건

여기에는 StorSimple 디바이스 관리자 서비스, StorSimple 가상 배열, 데이터 센터 네트워크에 대한 필수 조건이 제공됩니다.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스의 경우

시작하기 전에 다음 사항을 확인합니다.

* 액세스 자격 증명이 있는 Microsoft 계정이 있습니다.
* 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.
* 사용자의 Microsoft Azure 구독을 StorSimple 디바이스 관리자 서비스에 사용할 수 있습니다.

### <a name="for-the-storsimple-virtual-array"></a>StorSimple 가상 배열의 경우

가상 배열을 배포하기 전에 다음 사항을 확인해야 합니다.

* 디바이스를 프로비전하는 데 사용할 수 있는 Windows Server 2008 R2 이상 또는 VMware(ESXi 5.0, 5.5, 6.0 또는 6.5)에서 Hyper-V를 실행하는 호스트 시스템에 액세스할 수 있습니다.
* 가상 배열을 프로비전하려면 호스트 시스템에서 다음 리소스를 전용으로 사용할 수 있습니다.
  
  * 코어 4개 이상
  * RAM 8GB 이상 가상 배열을 파일 서버로서 구성하려는 경우 8GB는 2백만 개의 파일을 지원합니다. 2 - 4백만 개의 파일을 지원하려면 16GB RAM이 필요합니다.
  * 네트워크 인터페이스 하나
  * 시스템 데이터용 가상 디스크 500GB

### <a name="for-the-datacenter-network"></a>데이터 센터 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

* 데이터 센터의 네트워크가 StorSimple 디바이스에 대한 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [StorSimple 가상 배열 시스템 요구 사항](storsimple-ova-system-requirements.md)을 참조하세요.
* StorSimple 가상 배열에는 전용 5Mbps 인터넷 대역폭(또는 그 이상)을 항상 사용할 수 있습니다. 이 대역폭은 다른 애플리케이션과 공유하면 안됩니다.

## <a name="step-by-step-preparation"></a>단계별 준비

다음 단계별 지침을 사용하여 StorSimple 디바이스 관리자 서비스용 포털을 준비합니다.

## <a name="step-1-create-a-new-service"></a>1단계: 새 서비스 만들기

StorSimple 디바이스 관리자 서비스 단일 인스턴스는 여러 StorSimple 가상 배열을 관리할 수 있습니다. StorSimple 디바이스 관리자 서비스의 인스턴스를 만들려면 다음 단계를 수행합니다. 가상 배열을 관리하는 기존 StorSimple 디바이스 관리자 서비스가 있는 경우에는 이 단계를 건너뛰고 [2단계: 서비스 등록 키 가져오기](#step-2-get-the-service-registration-key)로 이동합니다.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> 서비스와 함께 저장소 계정을 자동으로 만들도록 설정하지 않은 경우, 서비스를 성공적으로 만든 후 하나 이상의 저장소 계정을 만들어야 합니다.
> 
> * 저장소 계정을 자동으로 만들지 않은 경우 자세한 지침은 [서비스에 대한 새 저장소 계정 구성](#optional-step-configure-a-new-storage-account-for-the-service) 을 참조하세요.
> * 저장소 계정을 자동으로 생성하도록 설정한 경우 [2단계: 서비스 등록 키 가져오기](#step-2-get-the-service-registration-key)로 이동합니다.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2단계: 서비스 등록 키 가져오기

StorSimple 디바이스 관리자 서비스를 실행한 후에는 서비스 등록 키를 받아야 합니다. 이 키는 StorSimple 디바이스를 서비스에 등록 및 연결하는 데 사용됩니다.

[Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> 서비스 등록 키는 StorSimple 디바이스 관리자 서비스에 등록해야 하는 모든 StorSimple 디바이스 관리자 디바이스를 등록하는 데 사용됩니다.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3단계: 가상 배열 이미지 다운로드

서비스 등록 키를 확보한 후에는 호스트 시스템에 가상 배열을 프로비전할 적절한 가상 배열 이미지를 다운로드해야 합니다. 가상 배열 이미지는 운영 체제 별로 구분되며 Azure Portal의 빠른 시작 페이지에서 다운로드할 수 있습니다.

> [!IMPORTANT]
> StorSimple 가상 배열에서 실행되는 소프트웨어는 StorSimple 디바이스 관리자 서비스와 함께 사용해야 합니다.
> 
> 

[Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

#### <a name="to-get-the-virtual-array-image"></a>가상 배열 이미지를 가져오려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 
2. Azure Portal에서 **찾아보기 &gt; StorSimple 디바이스 관리자**를 클릭합니다.
3. 기존 StorSimple 디바이스 관리자 서비스를 선택합니다. **StorSimple 디바이스 관리자** 블레이드에서 **빠른 시작**을 클릭합니다. 
4. Microsoft 다운로드 센터에서 다운로드하려는 이미지에 해당하는 링크를 클릭합니다. 이미지 파일은 약 4.8GB입니다.
   
   * Windows Server 2012 이상의 Hyper-V용 VHDX
   * Windows Server 2008 R2 이상의 Hyper-V용 VHD
   * VMWare ESXi 5.0, 5.5, 6.0 또는 6.5용 VMDK
5. 파일을 다운로드하고 로컬 드라이브에 압축을 푼 다음 압축을 푼 위치를 적어둡니다.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>선택적 단계: 서비스에 대한 새 저장소 계정 구성

이 단계는 선택 사항이며 서비스를 사용하여 저장소 계정을 자동으로 생성하도록 설정하지 않은 경우에만 수행해야 합니다.

다른 지역에 Azure Storage 계정을 만들어야 하는 경우 단계별 지침은 [스토리지 계정을 만드는 방법](../storage/common/storage-quickstart-create-account.md)을 참조하세요.

기존 Microsoft Azure Storage 계정을 추가하려면 StorSimple 디바이스 관리자 서비스 페이지의 [Azure Portal](https://ms.portal.azure.com/)에서 다음 단계를 수행합니다.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>디바이스 관리자 서비스와 동일한 Azure 구독에 있는 저장소 계정 자격 증명을 추가하려면

1. Device Manager 서비스를 찾아 선택하고 두 번 클릭합니다. 그러면 **개요** 블레이드가 열립니다.
2. **구성** 섹션 내의 **Storage 계정 자격 증명**을 선택합니다.
3. **추가**를 클릭합니다.
4. **저장소 계정 추가** 블레이드에서 다음을 수행합니다.
   
   1. **구독**에 **현재**를 선택합니다.
   
   2. Azure 저장소 계정의 이름을 제공합니다.
   
   3. **사용**을 선택하여 StorSimple 디바이스와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 사설 클라우드 내에서 작동하는 경우에만 **사용 안 함**을 선택합니다.
   
   4. **추가**를 클릭합니다. 저장소 계정이 성공적으로 만들어진 후 알림이 표시됩니다.<br></br>
   
      ![기존 저장소 계정 자격 증명 추가](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>다음 단계

다음 단계는 StorSimple 가상 배열에 대한 가상 머신을 프로비전하는 것입니다. 호스트 운영 체제에 따라서 다음의 자세한 지침을 참조하세요.

* [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [VMware에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-vmware.md)

