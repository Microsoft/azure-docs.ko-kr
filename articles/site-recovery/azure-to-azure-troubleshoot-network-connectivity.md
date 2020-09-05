---
title: Azure Site Recovery를 사용 하 여 azure에서 Azure로 재해 복구에 대 한 연결 문제 해결
description: Azure VM 재해 복구의 연결 문제 해결
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: afa2cbdb7b0703f9fc0b419442570744c6fefae1
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049692"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure 간 VM 네트워크 연결 문제 해결

이 문서에서는 한 지역에서 다른 지역으로 Azure VM (가상 머신)을 복제 및 복구할 때 네트워크 연결과 관련 된 일반적인 문제를 설명 합니다. 네트워킹 요구 사항에 대 한 자세한 내용은 [Azure vm 복제를 위한 연결 요구 사항](azure-to-azure-about-networking.md)을 참조 하세요.

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

| **이름**                  | **상업용**                               | **정부**                                 | **설명** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 스토리지                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. Vm에 대 한 모든 캐시 저장소 계정을 알고 있는 경우 특정 저장소 계정 Url에 대해 허용 목록을 사용할 수 있습니다. 예를 들어 `cache1.blob.core.windows.net` `cache2.blob.core.windows.net` 대신 및입니다 `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다. |
| 복제               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다. 방화벽 프록시가 Ip를 지 원하는 경우 해당 _SITE RECOVERY IP_ 를 사용할 수 있습니다. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다. 방화벽 프록시가 ip를 지 원하는 경우 해당 하는 _Site Recovery 모니터링 IP_ 를 사용할 수 있습니다. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195).

#### <a name="possible-cause"></a>가능한 원인

DNS (도메인 이름 시스템) 확인 오류로 인해 Site Recovery 끝점에 대 한 연결을 설정할 수 없습니다. VM을 장애 조치 (failover) 했지만 DR (재해 복구) 영역에서 DNS 서버에 연결할 수 없는 경우이 문제는 다시 보호 하는 동안 더 일반적입니다.

#### <a name="resolution"></a>해결 방법

사용자 지정 DNS를 사용 하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인 합니다.

VM에서 사용자 지정 DNS 설정을 사용 하는지 확인 하려면:

1. **가상 컴퓨터** 를 열고 VM을 선택 합니다.
1. Vm **설정** 으로 이동 하 고 **네트워킹**을 선택 합니다.
1. **가상 네트워크/서브넷**에서 링크를 선택 하 여 가상 네트워크의 리소스 페이지를 엽니다.
1. **설정** 으로 이동 하 고 **DNS 서버**를 선택 합니다.

가상 머신에서 DNS 서버에 액세스를 시도 합니다. DNS 서버에 액세스할 수 없는 경우 DNS 서버를 장애 조치 (failover) 하거나 DR 네트워크와 DNS 사이에 사이트의 줄을 만들어 해당 서버에 액세스할 수 있도록 합니다.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

> [!NOTE]
> Vm이 **표준** 내부 부하 분산 장치 뒤에 있는 경우 기본적으로와 같은 Office 365 ip에 액세스할 수 없습니다 `login.microsoftonline.com` . [Azure CLI를 사용 하 여 표준 Load Balancer에서 부하 분산 및 아웃 바운드 규칙 구성](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)문서에 설명 된 대로 **기본** 내부 부하 분산 장치 유형으로 변경 하거나 아웃 바운드 액세스를 만듭니다.

#### <a name="possible-cause"></a>가능한 원인

Office 365 인증 및 id IP4 끝점에 대 한 연결을 설정할 수 없습니다.

#### <a name="resolution"></a>해결 방법

- Azure Site Recovery 인증을 위해 Office 365 IP 범위에 액세스할 수 있어야 합니다.
- Azure NSG (네트워크 보안 그룹) 규칙/방화벽 프록시를 사용 하 여 VM에서 아웃 바운드 네트워크 연결을 제어 하는 경우 Office 365 IP 범위에 대 한 통신을 허용 해야 합니다. Azure AD에 해당 하는 모든 IP 주소에 대 한 액세스를 허용 하는 NSG 규칙을 기반으로 하는 [Azure Active Directory (AZURE ad) 서비스 태그](../virtual-network/security-overview.md#service-tags) 를 만듭니다.
- 향후 Azure AD에 새 주소를 추가 하는 경우 새 NSG 규칙을 만들어야 합니다.

### <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용 하 여 아웃 바운드 연결을 제어 하는 경우 필요한 모든 IP 주소 범위에 대해 **HTTPS 아웃 바운드** 규칙을 포트 443로 허용을 사용 합니다.
- 이 예제에서는 VM 원본 위치가 **미국 동부** 이 고 대상 위치가 **미국 중부**인 것으로 가정 합니다.

#### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 다음 스크린샷에 표시 된 것 처럼 NSG에 대 한 HTTPS 아웃 바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**를 사용 합니다. 즉, _저장소. eastus_ 및 **대상 포트 범위**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 다음 스크린샷에 표시 된 것 처럼 NSG에 대 한 HTTPS 아웃 바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**: _AzureActiveDirectory_ 및 **대상 포트 범위**: _443_을 사용 합니다.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 위의 보안 규칙과 마찬가지로 대상 위치에 해당 하는 NSG에서 "CentralUS"에 대 한 아웃 바운드 HTTPS (443) 보안 규칙을 만듭니다. 이를 통해 Site Recovery 모니터링에 액세스할 수 있습니다.
1. NSG에서 "AzureSiteRecovery"에 대 한 아웃 바운드 HTTPS (443) 보안 규칙을 만듭니다. 이를 통해 모든 지역에서 Site Recovery 서비스에 액세스할 수 있습니다.

#### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이 예에서는 대상 지역에서 원본 지역으로 장애 조치 후에 복제를 사용 하도록 설정할 수 있도록 이러한 NSG 규칙이 필요 합니다.

1. _CentralUS_에 대 한 HTTPS 아웃 바운드 보안 규칙을 만듭니다.

   - **대상 서비스 태그**: _CentralUS_
   - **대상 포트 범위**: _443_

1. _AzureActiveDirectory_에 대 한 HTTPS 아웃 바운드 보안 규칙을 만듭니다.

   - **대상 서비스 태그**: _AzureActiveDirectory_
   - **대상 포트 범위**: _443_

1. 위의 보안 규칙과 마찬가지로, 원본 위치에 해당 하는 NSG에 대 한 아웃 바운드 HTTPS (443) 보안 규칙을 만듭니다. 이를 통해 Site Recovery 모니터링에 액세스할 수 있습니다.
1. NSG에서 "AzureSiteRecovery"에 대 한 아웃 바운드 HTTPS (443) 보안 규칙을 만듭니다. 이를 통해 모든 지역에서 Site Recovery 서비스에 액세스할 수 있습니다.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).

#### <a name="possible-cause"></a>가능한 원인

Azure Site Recovery 서비스 끝점에 대 한 연결을 설정할 수 없습니다.

#### <a name="resolution"></a>해결 방법

Azure Site Recovery는 지역에 따라 [Site Recovery IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)에 액세스할 수 있어야 합니다. VM에서 필요한 IP 범위에 액세스할 수 있는지 확인 합니다.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버를 통과 하는 경우 Azure-Azure 복제가 실패 함 (151072)

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못 되어 Azure Site Recovery 모바일 서비스 에이전트가 Internet Explorer (IE)에서 프록시 설정을 자동으로 검색 하지 못했습니다.

#### <a name="resolution"></a>해결 방법

1. 모바일 서비스 에이전트는 Windows 및 Linux에서 IE의 프록시 설정을 검색 합니다 `/etc/environment` .
1. Azure Site Recovery 모바일 서비스에 대해서만 프록시를 설정 하는 것을 선호 하는 경우에는 다음 위치에 있는 _Proxyinfo._ 에 프록시 세부 정보를 제공 하면 됩니다.

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _Proxyinfo_ 는 다음과 같은 _INI_ 형식의 프록시 설정이 있어야 합니다.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery 모바일 서비스 에이전트는 **인증 되지 않은 프록시**만 지원 합니다.

### <a name="fix-the-problem"></a>문제 해결

[필요한 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필수 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)를 허용 하려면 [네트워킹 지침 문서의](./azure-to-azure-about-networking.md)단계를 수행 합니다.

## <a name="next-steps"></a>다음 단계

[Azure VM을 다른 Azure 지역에 복제](azure-to-azure-how-to-enable-replication.md)