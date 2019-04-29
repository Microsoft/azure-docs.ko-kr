---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b0b5e817d5e39dd7800a1482d40c56db5f2be6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127145"
---
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 위에서 시작하여 **리소스 만들기** > **계산** > **Windows Server 2016 Datacenter**를 클릭합니다.

    ![포털에서 Azure VM 이미지로 이동](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Windows Server 2016 Datacenter에서 클래식 배포 모델을 선택합니다. 만들기를 클릭합니다.

    ![포털에서 사용할 수 있는 Azure VM 이미지를 보여 주는 스크린샷](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. 기본 사항 블레이드

기본 사항 블레이드에는 가상 컴퓨터에 대한 관리 정보가 필요합니다.

1. 가상 머신의 **이름**을 입력합니다. 이 예제에서 _HeroVM_은 가상 머신의 이름입니다. 이름은 1-15자 길이여야 하며 특수 문자를 포함할 수 없습니다.

2. **사용자 이름** 및 VM에서 로컬 계정을 만드는 데 사용되는 강력한 **암호**를 입력합니다. 로컬 계정은 VM에 로그온하고 VM을 관리하는 데 사용됩니다. 이 예제에서 _azureuser_는 사용자 이름입니다.

   암호는 8-123자 길이여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 등 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다. 자세한 내용은 [사용자 이름 및 암호 요구 사항](../articles/virtual-machines/windows/faq.md)을 참조하세요.

3. **구독**은 선택 사항입니다. 일반적인 설정 중 하나는 "종량제"입니다.

4. 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 이 예제에서 _HeroVMRG_는 리소스 그룹의 이름입니다.

5. VM을 실행할 Azure 데이터 센터 **위치**를 선택합니다. 이 예제에서 **미국 동부**는 위치입니다.

6. 완료되면 **다음**을 클릭하여 다음 블레이드를 계속 진행합니다.

    ![Azure VM을 구성하기 위한 기본 사항 블레이드에서 설정을 보여 주는 스크린샷](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. 크기 블레이드

크기 블레이드는 VM의 구성 세부 정보를 식별하고 OS, 프로세서 수, 디스크 저장소 유형, 예상 월간 사용 비용 등을 포함하여 다양한 옵션을 나열합니다.  

VM 크기를 선택하고 **선택**을 클릭하여 계속 진행합니다. 이 예제에서 _DS1_\__V2 표준_은 VM 크기입니다.

  ![선택할 수 있는 Azure VM 크기를 보여 주는 크기 블레이드의 스크린샷](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. 설정 블레이드

설정 블레이드에서는 저장소 및 네트워크 옵션을 요청합니다. 기본 설정을 적용할 수 있습니다. Azure는 필요에 따라 적절한 항목을 만듭니다.

지원하는 가상 머신 크기를 선택한 경우 디스크 유형에서 프리미엄(SSD)을 선택하여 Azure Premium Storage를 사용해 볼 수 있습니다.

변경 작업이 완료되면 **확인**을 클릭합니다.

## <a name="4-summary-blade"></a>4. 요약 블레이드

요약 블레이드에는 이전 블레이드에서 지정한 설정이 나열됩니다. 이미지를 만들 준비가 되면 **확인**을 클릭합니다.

 ![가상 머신의 지정된 설정을 제공하는 요약 블레이드 보고서](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

가상 머신이 만들어지면 포털의 **모든 리소스** 아래에 새 가상 머신이 나열되고, 대시보드에 가상 머신의 타일이 표시됩니다. 해당 클라우드 서비스 및 저장소 계정도 생성되고 나열됩니다. 가상 컴퓨터와 클라우드 서비스가 둘 다 자동으로 시작되고 해당 상태가 **실행 중**으로 나열됩니다.

 ![가상 머신의 VM 에이전트 및 엔드포인트 구성](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
