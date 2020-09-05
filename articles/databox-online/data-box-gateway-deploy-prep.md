---
title: Data Box Gateway를 배포하기 위해 Azure Portal을 준비하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Data Box Gateway를 배포하는 첫 번째 자습서에는 Azure Portal 준비가 포함됩니다.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 8a8b53af6e23b2dd0ff011a6bfbf99a1da427063
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611639"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>자습서: Azure Data Box Gateway 배포 준비

이 문서는 Azure Data Box Gateway를 완전히 배포하는 데 필요한 배포 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 Data Box Gateway 리소스를 배포하기 위해 Azure Portal을 준비하는 방법을 설명합니다.

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 포털 준비에는 10분 미만이 소요됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 새 리소스 만들기
> * 가상 디바이스 이미지 다운로드
> * 활성화 키 가져오기

## <a name="get-started"></a>시작하기

Data Box Gateway를 배포하려면 다음 자습서를 규정된 순서대로 참조하세요.

| **#** | **단계** | **이러한 문서를 사용합니다.** |
| --- | --- | --- | 
| 1. |**[Data Box Gateway를 위해 Azure Portal 준비](data-box-gateway-deploy-prep.md)** |Data Box Gateway 가상 디바이스를 프로비전하기 전에 Data Box Gateway 리소스를 만들고 구성합니다. |
| 2. |**[Hyper-V에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[VMware에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V의 경우 Windows Server 2016이나 Windows Server 2012 R2에서 Hyper-V를 실행하는 호스트 시스템에서 Data Box Gateway 가상 디바이스를 프로비전하고 연결합니다. <br><br><br> VMware의 경우 VMware ESXi 6.0, 6.5 또는 6.7을 실행하는 호스트 시스템에서 Data Box Gateway 가상 디바이스에 프로비전하고 연결합니다.<br></br> |
| 3. |**[Data Box Gateway 연결, 설정, 활성화](data-box-gateway-deploy-connect-setup-activate.md)** |로컬 웹 UI에 연결하고 디바이스 설정을 완료한 다음, 디바이스를 활성화합니다. 그런 다음 SMB 공유를 프로비전할 수 있습니다.  |
| 4. |**[Data Box Gateway를 통해 데이터 전송](data-box-gateway-deploy-add-shares.md)** |공유를 추가하고, SMB 또는 NFS를 통해 공유에 연결합니다. |

이제 Azure Portal 설치를 시작할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

여기에는 Data Box Gateway 리소스, Data Box Gateway 디바이스 및 데이터 센터 네트워크에 대한 구성 필수 조건이 있습니다.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

* Data Stack Edge 리소스에 대해 Microsoft Azure 구독이 활성화되어 있습니다. [Microsoft EA(기업계약)](https://azure.microsoft.com/overview/sales-number/), [CSP(클라우드 솔루션 공급자)](https://docs.microsoft.com/partner-center/azure-plan-lp) 또는 [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/)과 같은 지원되는 구독을 사용했는지 확인합니다.
* Azure Stack Edge / Data Box Gateway, IoT Hub 및 Azure Storage 리소스에 대한 리소스 그룹 수준의 소유자 또는 기여자 액세스 권한이 있습니다.
    - Azure Stack Edge / Data Box Gateway 리소스를 만들려면 리소스 그룹 수준에서 범위가 지정된 기여자(또는 그 이상)로서 권한이 있어야 합니다. 또한 `Microsoft.DataBoxEdge` 공급자가 등록되어 있는지 확인해야 합니다. 등록 방법에 대한 정보는 [리소스 공급자 등록](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)으로 이동하세요.
    - 스토리지 계정 리소스를 만들려면 리소스 그룹 수준에서 범위가 지정되는 기여자 이상의 액세스 권한이 다시 필요합니다. Azure Storage는 기본적으로 등록된 리소스 공급자입니다.
- Microsoft Graph API에 대한 관리자 또는 사용자 액세스 권한이 있습니다. 자세한 내용은 [Microsoft Graph 사용 권한 참조](https://docs.microsoft.com/graph/permissions-reference)를 참조하세요.
- 액세스 자격 증명이 있는 Microsoft Azure Storage 계정이 있습니다.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway 디바이스의 경우

가상 디바이스를 배포하기 전에 다음 사항을 확인해야 합니다.

- 디바이스를 프로비전하는 데 사용할 수 있는 Windows Server 2012 R2 이상 또는 VMware(ESXi 6.0, 6.5 또는 6.7)에서 Hyper-V를 실행하는 호스트 시스템에 액세스할 수 있습니다.
- 호스트 시스템은 다음 리소스를 전용으로 사용하여 Data Box 가상 디바이스를 프로비전할 수 있습니다.
  
  - 최소 4개의 가상 프로세서
  - RAM 8GB 이상
  - 네트워크 인터페이스 하나
  - 250GB OS 디스크
  - 시스템 데이터용 가상 디스크 2TB

### <a name="for-the-datacenter-network"></a>데이터 센터 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

- 데이터 센터의 네트워크가 Data Box Gateway 디바이스에 대한 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [Data Box Gateway 시스템 요구 사항](data-box-gateway-system-requirements.md)을 참조하세요.

- Data Box Gateway가 정상 작동하려면 다음이 필요합니다.

    - 디바이스가 업데이트 상태를 유지하기 위한 10Mbps의 최소 다운로드 대역폭.
    - 파일을 전송하기 위한 20Mbps의 최소 전용 업로드 및 다운로드 대역폭.

## <a name="create-a-new-resource"></a>새 리소스 만들기

가상 디바이스를 관리할 기존 Data Box Gateway 리소스가 있으면 이 단계를 건너뛰고 [활성화 키 가져오기](#get-the-activation-key)로 이동합니다.

Data Box Gateway 리소스를 만들려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음에 로그인합니다.

    - URL [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인하거나
    - 또는 URL [https://portal.azure.us](https://portal.azure.us)에서 Azure Government 포털에 로그인합니다. 자세한 내용을 보려면 [포털을 사용하여 Azure Government에 연결](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)로 이동합니다.
2. 왼쪽 창에서 **+ 리소스 만들기**를 선택합니다. **Azure Stack Edge / Data Box Gateway**를 검색합니다. Azure Stack Edge / Data Box Gateway를 선택합니다. **만들기**를 선택합니다.
3. Data Box Gateway 디바이스에서 사용하려는 구독을 선택합니다. Data Box Gateway 리소스를 배포할 지역을 선택합니다. Azure Stack Edge 리소스를 사용할 수 있는 모든 지역 목록을 보려면 [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)을 참조하세요. 디바이스를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다. **Data Box Gateway** 옵션에서 **만들기**를 선택합니다.

    ![Data Box Gateway 서비스 검색](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. **기본 사항** 탭에서 다음 **프로젝트 세부 정보**를 입력하거나 선택합니다.
    
    |설정  |값  |
    |---------|---------|
    |Subscription    |이 항목은 이전에 선택한 내용에 따라 자동으로 채워집니다. 구독은 청구 계정에 연결됩니다. |
    |Resource group  |기존 그룹을 선택하거나 새 그룹을 만듭니다.<br>[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.     |

5. 다음 **인스턴스 세부 정보**를 입력하거나 선택합니다.

    |설정  |값  |
    |---------|---------|
    |속성   | 리소스를 식별하기 위한 이름.<br>이름에는 문자, 숫자 및 하이픈을 포함하여 2~50자가 포함됩니다.<br> 이름은 문자 또는 숫자로 시작하고 끝납니다.        |   
    |지역     |Azure Stack Edge 리소스를 사용할 수 있는 모든 지역 목록을 보려면 [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)을 참조하세요. Azure Government의 경우 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 나열된 모든 정부 지역에서 사용 가능합니다. <br> 디바이스를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다.|
    
    ![Data Box 게이트웨이 리소스 만들기](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. **검토 + 만들기**를 선택합니다.
 
7. **검토 + 만들기** 탭에서 **가격 책정 세부 정보**, **사용 약관** 및 리소스 세부 정보를 살펴봅니다. **만들기**를 선택합니다.

    ![Data Box Gateway 리소스 세부 정보 검토](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

리소스 생성에는 몇 분 정도가 소요됩니다. 리소스가 생성되고 배포된 후에는 알림이 표시됩니다. **리소스로 이동**을 선택합니다.

![Data Box Gateway 리소스 세부 정보 검토](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>가상 디바이스 이미지 다운로드

Data Box Gateway가 만들어진 후에는 적절한 가상 디바이스 이미지를 다운로드하여 호스트 시스템에 가상 디바이스를 프로비전합니다. 가상 디바이스 이미지는 운영 체제에 따라 다릅니다.

> [!IMPORTANT]
> Data Box Gateway에서 실행되는 소프트웨어는 Data Box Gateway 리소스에서만 사용할 수 있습니다.

[Azure Portal](https://portal.azure.com/)에서 이러한 단계를 따라 가상 디바이스 이미지를 다운로드합니다.

1. 사용자가 만든 리소스에서 **개요**를 선택합니다. 기존 Azure Data Box Gateway 리소스가 있는 경우 리소스를 선택하고 **개요**로 이동합니다. **디바이스 설정**을 선택합니다.

    ![새로운 Data Box 게이트웨이 리소스](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. **이미지 다운로드** 타일에서 VM을 프로비저닝하는 데 사용되는 호스트 서버의 운영 체제에 해당하는 가상 디바이스 이미지를 선택합니다. 이미지 파일은 약 5.6GB입니다.
   
   * [Windows Server 2012 R2 이상의 Hyper-V용 VHDX](https://aka.ms/dbe-vhdx-2012).
   * [VMware ESXi 6.0, 6.5 또는 6.7용 VMDK](https://aka.ms/dbe-vmdk).

    ![Data Box Gateway 가상 디바이스 이미지 다운로드](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. 파일을 다운로드하고 로컬 드라이브에 압축을 푼 다음 압축을 푼 위치를 적어둡니다.


## <a name="get-the-activation-key"></a>활성화 키 가져오기

Data Box Gateway 리소스가 가동하여 실행되면 활성화 키를 받아야 합니다. 이 키는 Data Box Gateway 디바이스를 활성화하고 리소스와 연결하는 데 사용됩니다. 이제 Azure Portal에서 이 키를 얻을 수 있습니다.

1. 사용자가 만든 리소스를 선택한 다음, **개요**를 선택합니다. **디바이스 설정**에서 **구성 및 활성화** 타일로 이동합니다.

    ![구성 및 활성화 타일](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. **키 생성**을 선택하여 활성화 키를 만듭니다. 복사 아이콘을 선택하여 키를 복사하고 나중에 사용할 수 있도록 저장합니다.

    ![활성화 키 가져오기](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 활성화 키는 생성되고 3일 후에 만료됩니다.
> - 키가 만료된 경우 새 키를 만듭니다. 이전 키는 유효하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Gateway 토픽에 대해 알아보았습니다.

> [!div class="checklist"]
> * 새 리소스 만들기
> * 가상 디바이스 이미지 다운로드
> * 활성화 키 가져오기

Data Box Gateway를 위한 가상 머신을 프로비전하는 방법을 알아보려면 다음 자습서로 계속 진행하세요. 호스트 운영 체제에 따라서 다음의 자세한 지침을 참조하세요.

> [!div class="nextstepaction"]
> [Hyper-V에서 Data Box Gateway 프로비전](./data-box-gateway-deploy-provision-hyperv.md)

또는

> [!div class="nextstepaction"]
> [VMware에서 Data Box Gateway 프로비전](./data-box-gateway-deploy-provision-vmware.md)


