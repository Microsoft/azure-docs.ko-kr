---
title: 호스트 이름 보기 및 수정 | Microsoft Docs
description: 이름 확인을 위해 Azure 가상 머신, 웹 및 작업자 역할에 대한 호스트 이름을 보고 변경하는 방법
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3fdb0f566789382a1606b19e4fac179f9ecf40cd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122960"
---
# <a name="viewing-and-modifying-hostnames"></a>호스트 이름 보기 및 수정
호스트 이름으로 역할 인스턴스를 참조하려면 각 역할의 서비스 구성 파일에서 호스트 이름의 값을 설정해야 합니다. 이렇게 하려면 **역할** 요소의 **vmName** 특성에 원하는 호스트 이름을 추가합니다. **vmName** 특성의 값은 각 역할 인스턴스의 호스트 이름에 대한 기준으로 사용됩니다. 예를 들어 경우 **vmName**이 *webrole*이고 해당 역할의 인스턴스가 3개 있으면 인스턴스의 호스트 이름은 *webrole0*, *webrole1*, *webrole2*가 됩니다. 가상 머신의 호스트 이름은 가상 머신 이름을 기준으로 채워지므로 구성 파일에서 가상 머신의 호스트 이름을 지정하지 않아도 됩니다. Microsoft Azure 서비스 구성에 대한 자세한 내용은 [Azure 서비스 구성 스키마(.cscfg 파일)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>호스트 이름 보기
아래 도구를 사용하여 가상 머신의 호스트 이름과 역할 인스턴스를 확인할 수 있습니다.

### <a name="service-configuration-file"></a>서비스 구성 파일
Azure 포털의 서비스 **구성** 블레이드에서 배포된 서비스의 서비스 구성 파일을 다운로드할 수 있습니다. 그런 후에 **역할 이름** 요소의 **vmName** 특성에서 호스트 이름을 확인할 수 있습니다. 이 호스트 이름은 각 역할 인스턴스의 호스트 이름 기준으로 사용됩니다. 예를 들어 경우 **vmName**이 *webrole*이고 해당 역할의 인스턴스가 3개 있으면 인스턴스의 호스트 이름은 *webrole0*, *webrole1*, *webrole2*가 됩니다.

### <a name="remote-desktop"></a>원격 데스크톱
가상 머신 또는 역할 인스턴스에 대한 원격 데스크톱(Windows), Windows PowerShell 원격(Windows) 또는 SSH(Linux/Windows) 연결을 사용하도록 설정하고 나면 여러 가지 방법으로 활성 원격 데스크톱 연결에서 호스트 이름을 확인할 수 있습니다.

* 명령 프롬프트 또는 SSH 터미널에 hostname을 입력합니다.
* 명령 프롬프트에 ipconfig /all을 입력합니다(Windows에만 해당).
* 시스템 설정에서 컴퓨터 이름을 확인합니다(Windows에만 해당).

### <a name="azure-service-management-rest-api"></a>Azure 서비스 관리 REST API
REST 클라이언트에서 다음 지침을 따릅니다.

1. Azure 포털에 연결하기 위한 클라이언트 인증서가 있는지 확인합니다. 클라이언트 인증서를 가져오려면에 나오는 단계에 따라 [방법: 게시 설정과 구독 정보 다운로드 및 가져오기](https://msdn.microsoft.com/library/dn385850.aspx)합니다. 
2. 값이 2013-11-01인 x-ms-version 헤더 항목을 설정합니다.
3. 다음 형식으로 요청을 보냅니다: https:\//management.core.windows.net/\<subscrition id\>/services/hostedservices\<-n a m\>? 포함할 세부 정보 = true
4. 각 **RoleInstance** 요소에 대해 **HostName** 요소를 찾습니다.

> [!WARNING]
> **InternalDnsSuffix** 요소를 확인하거나, 원격 데스크톱 세션(Windows)의 명령 프롬프트에서 ipconfig /all을 실행하거나, SSH 터미널(Linux)에서 cat /etc/resolv.conf를 실행하여 REST 호출 응답에서 클라우드 서비스의 내부 도메인 접미사를 확인할 수도 있습니다.
> 
> 

## <a name="modifying-a-hostname"></a>호스트 이름 수정
수정된 서비스 구성 파일을 업로드하거나 원격 데스크톱 세션에서 컴퓨터 이름을 바꿔 역할 인스턴스 또는 가상 머신의 호스트 이름을 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[이름 확인(DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure 서비스 구성 스키마(.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure Virtual Network 구성 스키마](https://go.microsoft.com/fwlink/?LinkId=248093)

[네트워크 구성 파일을 사용하여 DNS 설정 지정](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

