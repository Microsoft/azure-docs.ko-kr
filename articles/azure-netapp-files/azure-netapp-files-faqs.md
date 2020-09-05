---
title: Azure NetApp Files에 대 한 Faq | Microsoft Docs
description: 네트워킹, 보안, 성능, 용량 관리 및 데이터 마이그레이션/보호와 같은 Azure NetApp Files에 대 한 질문과 대답을 검토 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: b-juche
ms.openlocfilehash: eeb22672829dca9ba342079183dcc5ed7c35393c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590373"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files에 대 한 Faq

이 문서에서는 Azure NetApp Files에 대 한 Faq (질문과 대답)에 답변 합니다. 

## <a name="networking-faqs"></a>네트워킹 Faq

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 데이터 경로는 인터넷을 통해 이동 하나요?  

아니요. NFS 데이터 경로는 인터넷을 통해 이동 하지 않습니다. Azure NetApp Files은 서비스를 사용할 수 있는 Azure Virtual Network (VNet)에 배포 되는 Azure native service입니다. Azure NetApp Files는 위임 된 서브넷을 사용 하 고 VNet에서 직접 네트워크 인터페이스를 프로 비전 합니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 을 참조 하세요.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>이미 만든 VNet을 Azure NetApp Files 서비스에 연결할 수 있나요?

예, 만든 Vnet을 서비스에 연결할 수 있습니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 을 참조 하세요.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 이름을 사용 하 여 Azure NetApp Files의 NFS 볼륨을 탑재할 수 있나요?

예, 필요한 DNS 항목을 만드는 경우 수행할 수 있습니다. Azure NetApp Files는 프로 비전 된 볼륨에 대 한 서비스 IP를 제공 합니다. 

> [!NOTE] 
> 필요에 따라 서비스에 대 한 추가 Ip를 배포할 수 Azure NetApp Files.  DNS 항목을 정기적으로 업데이트 해야 할 수 있습니다.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨에 대해 자체 IP 주소를 설정 하거나 선택할 수 있나요?  

아니요. Azure NetApp Files 볼륨에 대 한 IP 할당은 동적입니다. 고정 IP 할당은 지원 되지 않습니다. 
 
## <a name="security-faqs"></a>보안 FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM과 저장소 간의 네트워크 트래픽이 암호화 될 수 있나요?

NFSv 4.1 클라이언트와 Azure NetApp Files 볼륨 간의 데이터 트래픽은 AES-256 암호화를 사용 하는 Kerberos를 사용 하 여 암호화할 수 있습니다. 자세한 내용은 [nfsv 4.1 Kerberos 암호화 구성 Azure NetApp Files을](configure-kerberos-encryption.md) 참조 하세요.   

NFSv3 또는 SMBv3 클라이언트 간의 데이터 트래픽이 Azure NetApp Files 볼륨으로 암호화 되지 않습니다. 그러나 Azure VM (NFS 또는 SMB 클라이언트를 실행 하는)에서 Azure NetApp Files로의 트래픽은 다른 Azure VM 간 트래픽과도 안전 합니다. 이 트래픽은 Azure 데이터 센터 네트워크에 대 한 로컬 트래픽입니다. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>미사용 저장소를 암호화할 수 있나요?

모든 Azure NetApp Files 볼륨은 FIPS 140-2 표준을 사용 하 여 암호화 됩니다. 모든 키는 Azure NetApp Files 서비스에서 관리 됩니다. 

### <a name="how-are-encryption-keys-managed"></a>암호화 키는 어떻게 관리 되나요? 

Azure NetApp Files에 대 한 키 관리는 서비스에 의해 처리 됩니다. 각 볼륨에 대해 고유한 XTS-256 데이터 암호화 키가 생성 됩니다. 암호화 키 계층은 모든 볼륨 키를 암호화 하 고 보호 하는 데 사용 됩니다. 이러한 암호화 키는 암호화 되지 않은 형식으로 표시 되거나 보고 되지 않습니다. 볼륨을 삭제 하면 암호화 키가 즉시 삭제 됩니다.

Azure 전용 HSM을 사용 하 여 사용자 관리 키 (사용자 고유 키 사용)에 대 한 지원은 미국 동부, 미국 West2 및 미국 남부 중부 지역에서 제어 되는 기준으로 제공 됩니다.  에서 액세스를 요청할 수 있습니다 **anffeedback@microsoft.com** . 용량을 사용할 수 있으므로 요청이 승인 됩니다.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp Files service mount 대상에 대 한 액세스를 제어 하도록 NFS 내보내기 정책 규칙을 구성할 수 있나요?


예, 단일 NFS 내보내기 정책에서 최대 5 개의 규칙을 구성할 수 있습니다.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>네트워크 보안 그룹을 지원 Azure NetApp Files?

아니요. 현재는 네트워크 보안 그룹을 Azure NetApp Files의 위임 된 서브넷 이나 서비스에서 만든 네트워크 인터페이스에 적용할 수 없습니다.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure IAM을 사용할 수 있나요?

예, Azure NetApp Files는 Azure IAM에서 RBAC 기능을 지원 합니다.

## <a name="performance-faqs"></a>성능 FAQ

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files 성능을 최적화 하거나 조정 하려면 어떻게 해야 하나요?

성능 요구 사항에 따라 다음 작업을 수행할 수 있습니다. 
- 가상 컴퓨터의 크기가 적절 하 게 조정 되었는지 확인 합니다.
- VM에 가속화 된 네트워킹을 사용 하도록 설정 합니다.
- 원하는 서비스 수준 및 용량 풀 크기를 선택 합니다.
- 용량 및 성능에 대해 원하는 할당량 크기의 볼륨을 만듭니다.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files의 처리량 기반 서비스 수준을 IOPS로 변환 어떻게 할까요?

다음 수식을 사용 하 여 m b/s를 IOPS로 변환할 수 있습니다.  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>볼륨의 서비스 수준을 변경 어떻게 할까요??

볼륨에 대해 원하는 [서비스 수준을](azure-netapp-files-service-levels.md) 사용 하는 다른 용량 풀로 볼륨을 이동 하 여 기존 볼륨의 서비스 수준을 변경할 수 있습니다. [볼륨의 서비스 수준 동적 변경을](dynamic-change-volume-service-level.md)참조 하세요. 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>어떻게 할까요? Azure NetApp Files 성능 모니터링

Azure NetApp Files는 볼륨 성능 메트릭을 제공 합니다. Azure NetApp Files에 대 한 사용 메트릭을 모니터링 하는 Azure Monitor 사용할 수도 있습니다.  Azure NetApp Files에 대 한 성능 메트릭 목록은 [Azure NetApp Files 메트릭](azure-netapp-files-metrics.md) 을 참조 하세요.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv 4.1의 Kerberos에 대 한 성능 영향은 무엇 인가요?

NFSv 4.1의 보안 옵션, 테스트 된 성능 벡터 및 예상 되는 성능 영향에 대 한 자세한 내용은 [nfsv 4.1의 Kerberos 성능에 미치는 영향](configure-kerberos-encryption.md#kerberos_performance) 을 참조 하세요. 

## <a name="nfs-faqs"></a>NFS FAQ

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM이 시작 되거나 다시 부팅 될 때 볼륨이 자동으로 탑재 되도록 합니다.  영구 NFS 볼륨에 대해 내 호스트를 구성 어떻게 할까요??

VM 시작 또는 다시 부팅 시 NFS 볼륨이 자동으로 탑재 되도록 하려면 호스트의 파일에 항목을 추가 `/etc/fstab` 합니다. 

자세한 내용은 [Windows 또는 Linux 가상 머신에 대 한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 를 참조 하세요.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>NFS 클라이언트의 DF 명령이 프로 비전 된 볼륨 크기를 표시 하지 않는 이유는 무엇 인가요?

DF에서 보고 되는 볼륨 크기는 Azure NetApp Files 볼륨의 크기를 늘릴 수 있는 최대 크기입니다. DF 명령의 Azure NetApp Files 볼륨 크기는 볼륨의 할당량 또는 크기를 반영 하지 않습니다.  Azure Portal 또는 API를 통해 Azure NetApp Files 볼륨 크기 또는 할당량을 가져올 수 있습니다.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>어떤 NFS 버전이 지원 Azure NetApp Files?

Azure NetApp Files은 NFSv3 및 NFSv 4.1을 지원 합니다. NFS 버전 중 하나를 사용 하 여 [볼륨을 만들](azure-netapp-files-create-volumes.md) 수 있습니다. 

### <a name="how-do-i-enable-root-squashing"></a>루트 squash 병합를 사용 하도록 설정 어떻게 할까요??

루트 계정이 볼륨의 내보내기 정책을 사용 하 여 볼륨에 액세스할 수 있는지 여부를 지정할 수 있습니다. 자세한 내용은 [NFS 볼륨에 대 한 내보내기 정책 구성](azure-netapp-files-configure-export-policy.md) 을 참조 하세요.

## <a name="smb-faqs"></a>SMB FAQ

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files에서 지 원하는 SMB 버전은 무엇 인가요?

Azure NetApp Files smb 2.1 및 smb 3.1 (SMB 3.0에 대 한 지원 포함)을 지원 합니다.    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 액세스에 Active Directory 연결이 필요 한가요? 

예, SMB 볼륨을 배포 하기 전에 Active Directory 연결을 만들어야 합니다. 연결에 성공 하려면 Azure NetApp Files의 위임 된 서브넷에서 지정 된 도메인 컨트롤러에 액세스할 수 있어야 합니다.  자세한 내용은 [SMB 볼륨 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 를 참조 하세요. 

### <a name="how-many-active-directory-connections-are-supported"></a>지원 되는 Active Directory 연결은 몇 개입니까?

AD 연결이 다른 NetApp 계정에 있는 경우에도 Azure NetApp Files는 단일 *지역*에서 여러 ad (Active Directory) 연결을 지원 하지 않습니다. 그러나 AD 연결이 서로 다른 지역에 있는 한 단일 *구독*에서 여러 ad 연결을 사용할 수 있습니다. 단일 지역에 여러 AD 연결이 필요한 경우 별도의 구독을 사용 하 여이 작업을 수행할 수 있습니다. 

AD 연결은 NetApp 계정에 따라 구성 됩니다. AD 연결은 생성 된 NetApp 계정을 통해서만 볼 수 있습니다.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure Active Directory 지원 Azure NetApp Files? 

[AD (Azure Active Directory) 도메인 서비스](https://docs.microsoft.com/azure/active-directory-domain-services/overview) 와 [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 이 둘 다 지원 됩니다. Azure NetApp Files에서 기존 Active Directory 도메인 컨트롤러를 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 머신으로 또는 Express 경로 또는 S2S VPN을 통해 온-프레미스에 상주할 수 있습니다. Azure NetApp Files은 현재 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) 에 대 한 AD 조인을 지원 하지 않습니다.

Azure Active Directory Domain Services와 함께 Azure NetApp Files를 사용하는 경우 NetApp 계정에 대해 Active Directory를 구성할 때 조직 구성 단위 경로는 `OU=AADDC Computers`입니다.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>지원 되는 Windows Server Active Directory 버전은 무엇 인가요?

Azure NetApp Files는 Windows Server 2008 R2sp1-2019 버전의 Active Directory Domain Services를 지원 합니다.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>SMB 클라이언트에서 사용 가능한 공간에 프로 비전 된 크기가 표시 되지 않는 이유는 무엇 인가요?

SMB 클라이언트에서 보고 하는 볼륨 크기는 Azure NetApp Files 볼륨의 크기를 늘릴 수 있는 최대 크기입니다. SMB 클라이언트에 표시 되는 Azure NetApp Files 볼륨의 크기는 볼륨의 할당량 또는 크기를 반영 하지 않습니다. Azure Portal 또는 API를 통해 Azure NetApp Files 볼륨 크기 또는 할당량을 가져올 수 있습니다.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="dual-protocol-faqs"></a>이중 프로토콜 Faq

### <a name="i-tried-to-use-the-root-and-local-users-to-access-a-dual-protocol-volume-with-the-ntfs-security-style-on-a-unix-system-why-did-i-encounter-a-permission-denied-error"></a>' Root ' 및 로컬 사용자를 사용 하 여 UNIX 시스템의 NTFS 보안 스타일로 이중 프로토콜 볼륨에 액세스 하려고 했습니다. "사용 권한이 거부 되었습니다." 오류가 발생 하는 이유는 무엇 인가요?   

이중 프로토콜 볼륨은 NFS 및 SMB 프로토콜을 모두 지원 합니다.  UNIX 시스템의 탑재 된 볼륨에 액세스 하려고 하면 시스템에서 사용 하는 UNIX 사용자를 Windows 사용자에 매핑하려고 시도 합니다. 매핑이 발견 되지 않으면 "사용 권한이 거부 되었습니다." 오류가 발생 합니다.  이 상황은 액세스에 ' root ' 사용자를 사용 하는 경우에도 적용 됩니다.    

"사용 권한이 거부 되었습니다." 문제를 방지 하려면 `pcuser` 탑재 지점에 액세스 하기 전에 Windows Active Directory에가 포함 되어 있는지 확인 합니다. `pcuser`"사용 권한이 거부 되었습니다." 문제가 발생 한 후를 추가 하는 경우 액세스를 다시 시도 하기 전에 캐시 항목을 제거 하기 위해 24 시간 동안 기다립니다.

### <a name="when-i-try-to-create-a-dual-protocol-volume-why-does-the-creation-process-fail-with-the-error-failed-to-validate-ldap-configuration-try-again-after-correcting-ldap-configuration"></a>이중 프로토콜 볼륨을 만들려고 할 때 "LDAP 구성의 유효성을 검사 하지 못했습니다." 라는 오류와 함께 만들기 프로세스가 실패 하는 이유는 무엇 인가요?  

DNS 서버에 AD 호스트 컴퓨터의 포인터 (PTR) 레코드가 없을 수 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에서 AD 호스트 컴퓨터의 PTR 레코드를 추가 해야 합니다.

예를 들어 AD 컴퓨터의 IP 주소가이 고 `1.1.1.1` , 명령을 사용 하 여 발견 된 ad 컴퓨터의 호스트 이름이이 `hostname` `AD1` 고, 도메인 이름이 인 것으로 가정 합니다 `myDomain.com` .  역방향 조회 영역에 추가 된 PTR 레코드는 이어야 합니다 `1.1.1.1`  ->  `AD1.myDomain.com` .

## <a name="capacity-management-faqs"></a>용량 관리 Faq

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>용량 풀 및 Azure NetApp Files 볼륨에 대 한 사용량을 모니터링 어떻게 할까요?? 

Azure NetApp Files는 용량 풀 및 볼륨 사용 메트릭을 제공 합니다. Azure Monitor를 사용 하 여 Azure NetApp Files에 대 한 사용량을 모니터링할 수도 있습니다. 자세한 내용은 [Azure NetApp Files에 대 한 메트릭](azure-netapp-files-metrics.md) 을 참조 하세요. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Storage 탐색기를 통해 Azure NetApp Files를 관리할 수 있나요?

아니요. Azure Storage 탐색기에서 Azure NetApp Files 지원 되지 않습니다.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>디렉터리가 제한 크기에 근접 하 고 있는지 확인 어떻게 할까요??

`stat`클라이언트에서 명령을 사용 하 여 디렉터리가 디렉터리 메타 데이터에 대 한 최대 크기 제한 (320 MB)에 근접 하 고 있는지 확인할 수 있습니다.

320 디렉터리의 경우 블록 수는 655360 이며 각 블록 크기는 512 바이트입니다.  (즉, 320x1024x1024/512)  

예제:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>데이터 마이그레이션 및 보호 Faq

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>데이터를 Azure NetApp Files로 어떻게 할까요? 마이그레이션
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공 합니다.  모든 파일 기반 복사 도구를 사용 하 여 데이터를 서비스로 마이그레이션할 수 있습니다. 

NetApp은 SaaS 기반 솔루션인 [Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공 합니다.  이 솔루션을 사용 하면 nfs 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

광범위 한 무료 도구를 사용 하 여 데이터를 복사할 수도 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html) 와 같은 작업 도구를 사용 하 여 원본 데이터를 Azure NetApp Files 볼륨에 복사 하 고 동기화 할 수 있습니다. SMB의 경우 동일한 방식으로 워크 로드 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 를 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 사용 권한을 복제할 수도 있습니다. 

온-프레미스에서 Azure NetApp Files로 데이터 마이그레이션에 대 한 요구 사항은 다음과 같습니다. 

- 대상 Azure 지역에서 Azure NetApp Files를 사용할 수 있는지 확인 합니다.
- 원본 및 Azure NetApp Files 대상 볼륨 IP 주소 간의 네트워크 연결을 확인 합니다. 온-프레미스와 Azure NetApp Files 서비스 간의 데이터 전송은 Express 경로를 통해 지원 됩니다.
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용 하 여 원본 데이터를 대상 볼륨으로 전송 합니다.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>다른 Azure 지역에서 Azure NetApp Files 볼륨의 복사본을 만들 어떻게 할까요? 있나요?
    
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공 합니다.  모든 파일 기반 복사 도구를 사용 하 여 Azure 지역 간에 데이터를 복제할 수 있습니다. 

NetApp은 SaaS 기반 솔루션인 [Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공 합니다.  이 솔루션을 사용 하면 nfs 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

광범위 한 무료 도구를 사용 하 여 데이터를 복사할 수도 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html) 와 같은 작업 도구를 사용 하 여 원본 데이터를 Azure NetApp Files 볼륨에 복사 하 고 동기화 할 수 있습니다. SMB의 경우 동일한 방식으로 워크 로드 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 를 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 사용 권한을 복제할 수도 있습니다. 

Azure NetApp Files 볼륨을 다른 Azure 지역으로 복제 하기 위한 요구 사항은 다음과 같습니다. 
- 대상 Azure 지역에서 Azure NetApp Files를 사용할 수 있는지 확인 합니다.
- 각 지역에서 Vnet 간 네트워크 연결의 유효성을 검사 합니다. 현재 Vnet 간의 전역 피어 링은 지원 되지 않습니다.  Express 경로 회로와 연결 하거나 S2S VPN 연결을 사용 하 여 Vnet 간의 연결을 설정할 수 있습니다. 
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 파일 복사 도구를 사용 하 여 원본 데이터를 대상 볼륨으로 전송 합니다.

### <a name="is-migration-with-azure-data-box-supported"></a>마이그레이션이 Azure Data Box 지원 되나요?

아니요. Azure Data Box은 현재 Azure NetApp Files를 지원 하지 않습니다. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export 서비스를 사용 하 여 마이그레이션이 지원 되나요?

아니요. Azure Import/Export 서비스는 현재 Azure NetApp Files을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [Microsoft Azure ExpressRoute Faq](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox)
- [Azure NetApp Files의 SMB 성능에 대 한 Faq](azure-netapp-files-smb-performance.md)
