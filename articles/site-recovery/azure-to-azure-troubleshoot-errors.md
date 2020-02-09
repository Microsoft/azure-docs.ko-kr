---
title: Azure Site Recovery에서 Azure VM 복제 문제 해결
description: 재해 복구를 위해 Azure 가상 머신을 복제할 때 발생 하는 오류 문제를 해결 합니다.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 3f97975f09d846cd3277bb8a53a4ad922f1b5b69
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902548"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure 간 VM 복제 오류 문제 해결

이 문서에서는 한 지역에서 다른 지역으로 Azure Vm (가상 머신)을 복제 및 복구 하는 동안 Azure Site Recovery에서 발생 하는 일반적인 오류를 해결 하는 방법을 설명 합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)

구독에서 재해 복구 지역으로 사용할 대상 지역에 Azure Vm을 만들 수 있는지 확인 합니다. 또한 구독에 필요한 크기의 Vm을 만들 수 있는 충분 한 할당량이 있는지 확인 합니다. 기본적으로 Site Recovery는 원본 VM 크기와 동일한 대상 VM 크기를 선택 합니다. 일치 하는 크기를 사용할 수 없는 경우 Site Recovery에서 사용 가능한 가장 가까운 크기를 자동으로 선택 합니다.

원본 VM 구성을 지 원하는 크기가 없는 경우 다음과 같은 메시지가 나타납니다.

> "가상 컴퓨터 *VmName*에 대 한 복제를 사용 하도록 설정할 수 없습니다."

### <a name="possible-causes"></a>가능한 원인

- 구독 ID를 사용 하 여 대상 지역 위치에서 Vm을 만들 수 없습니다.
- 구독 ID가 사용 하도록 설정 되지 않았거나, 대상 지역 위치에서 특정 VM 크기를 만드는 데 충분 한 할당량이 없습니다.
- 대상 지역 위치의 구독 ID에 대해 원본 VM의 NIC (네트워크 인터페이스 카드) 수 (2)와 일치 하는 적합 한 대상 VM 크기를 찾을 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

[Azure 청구 지원](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) 에 문의 하 여 구독에서 대상 위치에 필요한 크기의 vm을 만들 수 있도록 합니다. 그런 다음 실패 한 작업을 다시 시도 합니다.

대상 위치에 용량 제약 조건이 있으면 복제를 사용 하지 않도록 설정 합니다. 그런 다음, 구독에 필요한 크기의 Vm을 만들 수 있는 충분 한 할당량이 있는 다른 위치로 복제를 사용 하도록 설정 합니다.

## <a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

VM에 신뢰할 수 있는 최신 루트 인증서가 모두 없는 경우 "복제 사용" Site Recovery 작업이 실패할 수 있습니다. 이러한 인증서 없이 VM의 Site Recovery 서비스 호출에 대 한 인증 및 권한 부여가 실패 합니다. 

"복제 사용" 작업이 실패 하는 경우 다음과 같은 메시지가 나타납니다.

> "Site Recovery 구성에 실패 했습니다."

### <a name="possible-cause"></a>가능한 원인

권한 부여 및 인증에 필요한 신뢰할 수 있는 루트 인증서가 가상 컴퓨터에 없습니다.

### <a name="fix-the-problem"></a>문제 해결

#### <a name="windows"></a>Windows

Windows 운영 체제를 실행 하는 VM의 경우 모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 표시 되도록 최신 Windows 업데이트를 VM에 설치 합니다. 조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스에 따라 Vm에서 최신 루트 인증서 및 업데이트 된 인증서 해지 목록을 가져옵니다.

연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다. 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

문제가 해결되었는지 확인하려면 VM의 브라우저에서 login.microsoftonline.com으로 이동합니다.

자세한 내용은 [신뢰할 수 있는 루트 및 허용 되지 않는 인증서 구성](https://technet.microsoft.com/library/dn265983.aspx)을 참조 하세요.

#### <a name="linux"></a>Linux

Linux 운영 체제 버전의 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 VM의 최신 인증서 해지 목록을 가져옵니다.

SUSE Linux는 기호화 된 링크 (또는 *symlink*)를 사용 하 여 인증서 목록을 유지 하기 때문에 다음 단계를 수행 합니다.

1. 루트 사용자로 로그인합니다.

1. 다음 명령을 실행 하 여 디렉터리를 변경 합니다.

    **# cd/etc/ssl/certs**

1. Symantec 루트 CA 인증서가 있는지 확인 합니다.

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Symantec 루트 CA 인증서를 찾을 수 없는 경우 다음 명령을 실행 하 여 파일을 다운로드 합니다. 오류를 확인 하 고 네트워크 오류에 대 한 권장 조치를 따릅니다.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Baltimore 루트 CA 인증서가 있는지 확인 합니다.

    **# ls Baltimore_CyberTrust_Root. pem**

1. Baltimore 루트 CA 인증서를 찾을 수 없는 경우 다음 명령을 실행 하 여 인증서를 다운로드 합니다.

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root. pem**

1. DigiCert_Global_Root_CA 인증서가 있는지 확인 합니다.

    **# ls DigiCert_Global_Root_CA. pem**

1. DigiCert_Global_Root_CA를 찾을 수 없는 경우 다음 명령을 실행 하 여 인증서를 다운로드 합니다.

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509-DigiCertGlobalRootCA-outform pem 아웃 DigiCert_Global_Root_CA. pem**

1. Rehash 스크립트를 실행 하 여 새로 다운로드 한 인증서에 대 한 인증서 주체 해시를 업데이트 합니다.

    **# c_rehash**

1. 다음 명령을 실행 하 여 인증서에 대해 symlink로 주체 해시가 생성 되었는지 여부를 확인 합니다.

    - 명령:

        **# ls-l | grep Baltimore**

    - 출력:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 명령:

        **# ls-l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 출력:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 명령:

        **# ls-l | grep DigiCert_Global_Root**

    - 출력:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. VeriSign_Class_3_Public_Primary_Certification_Authority_G5 파일 이름 b204d74a를 사용 하 여 파일의 복사본을 만듭니다.:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Baltimore_CyberTrust_Root 파일 이름 653b494a를 사용 하 여 파일의 복사본을 만듭니다.:

    **# cp Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. 파일 이름이 3513523f 인 DigiCert_Global_Root_CA 파일의 복사본을 만듭니다. 0:

    **# cp DigiCert_Global_Root_CA. pem 3513523f. 0**

1. 파일이 있는지 여부를 확인 합니다.

    - 명령:

        **# ls-l 653b494a b204d74a. 0 35 13523f. 0**

    - 출력

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

Site Recovery 복제가 작동 하려면 VM에서 특정 Url 또는 IP 범위에 대 한 아웃 바운드 연결이 필요 합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>문제 1: Site Recovery을 사용 하 여 Azure 가상 머신을 등록 하지 못했습니다 (오류 코드 151195).

#### <a name="possible-cause"></a>가능한 원인 

DNS 확인 오류로 인해 Site Recovery 엔드포인트에 대 한 연결을 설정할 수 없습니다.

이 문제는 가상 머신을 장애 조치 (failover) 했지만 DR (재해 복구) 영역에서 DNS 서버에 연결할 수 없는 경우 다시 보호 중에 가장 자주 발생 합니다.

#### <a name="fix-the-problem"></a>문제 해결

사용자 지정 DNS를 사용 하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인 합니다. VM에서 사용자 지정 DNS가 있는지 여부를 확인 하려면 *재해 복구 네트워크* > **dns 서버**로 이동 합니다.

![사용자 지정 DNS 서버 목록](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

가상 머신에서 DNS 서버에 액세스를 시도합니다. 서버에 액세스할 수 없는 경우 DNS 서버를 장애 조치 (failover) 하거나 DR 네트워크와 DNS 사이에 사이트의 줄을 만들어 액세스할 수 있도록 합니다.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>문제 2: Site Recovery 구성 실패 (오류 코드 151196)

#### <a name="possible-cause"></a>가능한 원인

Office 365 인증 및 id IP4 엔드포인트에 대 한 연결을 설정할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Site Recovery 인증을 위해 Office 365 IP 범위에 대 한 액세스가 필요 합니다.
Azure NSG 규칙 또는 방화벽 프록시를 사용 하 여 VM에서 아웃 바운드 네트워크 연결을 제어 하는 경우 Office 365 IP 범위에 대 한 통신을 허용 해야 합니다. Azure AD에 해당 하는 모든 IP 주소에 대 한 액세스를 허용 하는 [Azure Active Directory (AZURE ad) 서비스 태그](../virtual-network/security-overview.md#service-tags)를 기반으로 nsg 규칙을 만듭니다. 향후 Azure AD에 새 주소를 추가 하는 경우 새 NSG 규칙을 만들어야 합니다.

> [!NOTE]
> Vm이 *표준* 내부 부하 분산 장치 뒤에 있는 경우 부하 분산 장치는 기본적으로 OFFICE 365 IP 범위 (즉, login.microsoftonline.com)에 액세스할 수 없습니다. 내부 부하 분산 장치 유형을 *기본* 으로 변경 하거나 [부하 분산 및 아웃 바운드 규칙 구성](https://aka.ms/lboutboundrulescli)문서에 설명 된 대로 아웃 바운드 액세스를 만듭니다.

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>문제 3: Site Recovery 구성 실패 (오류 코드 151197)

#### <a name="possible-cause"></a>가능한 원인

Site Recovery 서비스 엔드포인트에 대 한 연결을 설정할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Site Recovery 지역에 따라 [SITE RECOVERY IP 범위](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)에 액세스할 수 있어야 합니다. 가상 머신에서 필요한 IP 범위에 액세스할 수 있는지 확인 합니다.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버를 통과 하는 경우 Azure에서 Azure로 복제 하지 못했습니다 (오류 코드 151072).

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못 되었으며 Site Recovery Mobility Service agent가 Internet Explorer에서 프록시 설정을 자동으로 검색 하지 못했습니다.

#### <a name="fix-the-problem"></a>문제 해결

모바일 서비스 에이전트는 Windows의 Internet Explorer 및 Linux의/etc/environment에서 프록시 설정을 검색 합니다.

모바일 서비스에 대해서만 프록시를 설정 하려는 경우 다음 위치의 ProxyInfo. o f f 파일에 프록시 세부 정보를 제공 하면 됩니다.

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure Site Recovery\Config

ProxyInfo에서 다음 초기화 파일 형식으로 프록시 설정을 제공 합니다.

> [*프록시*]

> 주소 = *http://1.2.3.4*

> 포트 =*567*


> [!NOTE]
> Site Recovery 모바일 서비스 에이전트는 인증 되지 않은 *프록시*만 지원 합니다.

### <a name="more-information"></a>자세한 정보

[필수 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필수 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)를 지정 하려면 azure [에서 Azure로 복제에 대 한 정보](site-recovery-azure-to-azure-networking-guidance.md)를 참조 하세요.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>컴퓨터에서 디스크를 찾을 수 없음(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다. 디스크를 찾을 수 없는 경우 다음과 같은 메시지가 나타납니다.

> " *Lun* (논리 단위 번호)을 포함 하는 Azure 데이터 디스크 *diskname* *diskname* 가 동일한 lun 값을 가진 VM 내에서 보고 되는 해당 디스크에 매핑되지 않았습니다. *LUNValue* .

### <a name="possible-causes"></a>가능한 원인

- 새 데이터 디스크가 VM에 연결 되었지만 초기화 되지 않았습니다.
- VM 내의 데이터 디스크가 VM에 디스크가 연결 된 LUN (논리 단위 번호) 값을 올바르게 보고 하지 않습니다.

### <a name="fix-the-problem"></a>문제 해결

데이터 디스크가 초기화 되었는지 확인 한 후 작업을 다시 시도 하십시오.

- **Windows**: [새 디스크를 연결 하 고 초기화](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)합니다.

- **Linux**: [linux에서 새 데이터 디스크를 초기화](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)합니다.

문제가 계속되면 지원에 문의하세요.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>하나 이상의 디스크를 보호할 수 있습니다 (오류 코드 153039).

### <a name="possible-causes"></a>가능한 원인

- 보호 후 하나 이상의 디스크가 가상 컴퓨터에 최근에 추가 되었습니다.
- 가상 컴퓨터를 보호 한 후 하나 이상의 디스크가 초기화 되었습니다.

### <a name="fix-the-problem"></a>문제 해결

VM의 복제 상태를 다시 정상으로 설정 하려면 디스크를 보호 하거나 경고를 해제 하도록 선택할 수 있습니다.

#### <a name="to-protect-the-disks"></a>디스크를 보호 하려면

1. *VM 이름* > **디스크** > **복제 된 항목** 으로 이동 합니다.
1. 보호 되지 않은 디스크를 선택한 다음 **복제 사용**을 선택 합니다.

    ![VM 디스크에서 복제 사용](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>경고를 해제 하려면

1. *VM 이름* > **복제 된 항목** 으로 이동 합니다.
1. **개요** 섹션에서 경고를 선택 하 고 **확인**을 선택 합니다.

    ![새로 만들기-디스크 경고](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>정보를 사용 하 여 완료 된 자격 증명 모음에서 가상 컴퓨터 제거 (오류 코드 150225)

가상 컴퓨터를 보호 하는 경우 Site Recovery은 원본 가상 컴퓨터에 몇 가지 링크를 만듭니다. 보호를 제거 하거나 복제를 사용 하지 않도록 설정 하면 Site Recovery는 정리 작업의 일부로 이러한 링크를 제거 합니다. 가상 컴퓨터에 리소스 잠금이 있는 경우 정리 작업은 정보를 사용 하 여 완료 됩니다. 이 정보는 Recovery Services 자격 증명 모음에서 가상 머신이 제거 되었지만 원본 컴퓨터에서 오래 된 링크 중 일부를 정리할 수 없다는 것을 의미 합니다.

이 가상 컴퓨터를 다시 보호 하지 않으려는 경우에는이 경고를 무시 해도 됩니다. 그러나 나중에이 가상 컴퓨터를 보호 해야 하는 경우 "문제 해결" 아래의 단계를 수행 하 여 링크를 정리 합니다.

> [!WARNING]
> 정리 하지 않은 경우:
>
> - Recovery Services 자격 증명 모음을 통해 복제를 사용 하도록 설정 하면 가상 컴퓨터가 나열 되지 않습니다.
> - **가상 컴퓨터** > **설정** > **재해 복구**를 사용 하 여 vm을 보호 하려고 하면 작업이 실패 하 고 "VM의 기존 부실 리소스 링크 때문에 복제를 사용할 수 없습니다." 라는 메시지가 표시 됩니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이러한 단계를 수행 하는 동안 원본 가상 컴퓨터를 삭제 하거나 다른 방식으로 영향을 Site Recovery 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거 합니다. 예를 들어 다음 이미지에서는 "MoveDemo" 라는 VM의 리소스 잠금을 삭제 해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드 하 여 [오래 된 Site Recovery 구성을 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
1. Cleanup-stale-asr-config-Azure-VM 라는 스크립트를 실행 합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
1. Azure 자격 증명을 요청 하는 경우 제공 합니다. 그런 다음 스크립트가 오류 없이 실행 되는지 확인 합니다.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>VM의 오래 된 리소스 링크로 인해 복제를 사용 하도록 설정할 수 없습니다 (오류 코드 150226).

### <a name="possible-cause"></a>가능한 원인

이전 Site Recovery 보호에서 가상 컴퓨터의 구성이 오래 되었습니다.

Site Recovery를 사용 하 여 Azure VM에 대 한 복제를 사용 하도록 설정한 경우 Azure VM에서 오래 된 구성이 발생할 수 있습니다.

- 복제를 사용 하지 않도록 설정 했지만 원본 VM에 리소스 잠금이 있습니다.
- VM에서 복제를 명시적으로 사용 하지 않고 Site Recovery 자격 증명 모음을 삭제 했습니다.
- VM에서 복제를 명시적으로 사용 하지 않고 Site Recovery 자격 증명 모음을 포함 하는 리소스 그룹을 삭제 했습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이러한 단계를 수행 하는 동안 원본 가상 컴퓨터를 삭제 하거나 다른 방식으로 영향을 Site Recovery 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거 합니다. 예를 들어 다음 이미지에서는 "MoveDemo" 라는 VM의 리소스 잠금을 삭제 해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드 하 여 [오래 된 Site Recovery 구성을 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
1. Cleanup-stale-asr-config-Azure-VM 라는 스크립트를 실행 합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
1. Azure 자격 증명을 요청 하는 경우 제공 합니다. 그런 다음 스크립트가 오류 없이 실행 되는지 확인 합니다.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>"복제 사용" 작업에서 선택 항목에 대 한 Azure VM 또는 리소스 그룹을 볼 수 없습니다.

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>원인 1: 리소스 그룹 및 원본 가상 머신이 서로 다른 위치에 있습니다.

Site Recovery 현재 원본 영역 리소스 그룹 및 가상 컴퓨터가 동일한 위치에 있어야 합니다. 그렇지 않으면 보호를 적용 하려고 할 때 가상 머신 또는 리소스 그룹을 찾을 수 없습니다.

이 문제를 해결 하려면 Recovery Services 자격 증명 모음 대신 VM에서 복제를 사용 하도록 설정할 수 있습니다. **원본 VM** > **속성** > **재해 복구** 로 이동 하 여 복제를 사용 하도록 설정 합니다.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>원인 2: 리소스 그룹이 선택한 구독의 일부가 아닙니다.

리소스 그룹이 선택한 구독의 일부가 아닌 경우 보호 시 리소스 그룹을 찾지 못할 수 있습니다. 리소스 그룹이 사용 중인 구독에 속해 있는지 확인 합니다.

### <a name="cause-3-stale-configuration"></a>원인 3: 부실 구성

오래 된 Site Recovery 구성이 Azure VM에 남아 있는 경우 복제를 사용 하도록 설정 하려는 VM이 표시 되지 않을 수 있습니다. 이 문제는 Site Recovery을 사용 하 여 Azure VM에 대 한 복제를 사용 하도록 설정한 경우에 발생할 수 있습니다.

- VM에서 복제를 명시적으로 사용 하지 않고 Site Recovery 자격 증명 모음을 삭제 했습니다.
- VM에서 복제를 명시적으로 사용 하지 않고 Site Recovery 자격 증명 모음을 포함 하는 리소스 그룹을 삭제 했습니다.
- 복제를 사용 하지 않도록 설정 했지만 원본 VM에 리소스 잠금이 있습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이 섹션에 언급 된 스크립트를 사용 하기 전에 "AzureRM" 모듈을 업데이트 해야 합니다.  이러한 단계를 수행 하는 동안 원본 가상 컴퓨터를 삭제 하거나 다른 방식으로 영향을 Site Recovery 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거 합니다 (있는 경우). 예를 들어 다음 이미지에서는 "MoveDemo" 라는 VM의 리소스 잠금을 삭제 해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드 하 여 [오래 된 Site Recovery 구성을 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
1. Cleanup-stale-asr-config-Azure-VM 라는 스크립트를 실행 합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
1. Azure 자격 증명을 요청 하는 경우 제공 합니다. 그런 다음 스크립트가 오류 없이 실행 되는지 확인 합니다.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>보호할 가상 컴퓨터를 선택할 수 없습니다.

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>원인 1: 가상 컴퓨터에 실패 또는 응답 하지 않는 확장 프로그램이 설치 되어 있습니다.

**Virtual machines** > **설정** > **확장** 으로 이동 하 여 실패 상태에 있는 확장을 확인 합니다. 실패 한 확장을 제거 하 고 다시 시도 하 여 가상 머신을 보호 합니다.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>원인 2: VM의 프로 비전 상태가 잘못 되었습니다.

이 문서의 뒷부분에 나오는 [VM의 프로 비전 상태에 대](#the-vms-provisioning-state-is-not-valid-error-code-150019)한 문제 해결 단계는 유효 하지 않음을 참조 하세요.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM의 프로 비전 상태가 잘못 되었습니다 (오류 코드 150019).

VM에서 복제를 사용 하도록 설정 하려면 해당 프로 비전 상태가 **성공**이어야 합니다. 프로 비전 상태를 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스**의 **리소스 탐색기** 를 선택 합니다.
1. **구독** 목록을 확장하고 본인의 구독을 선택합니다.
1. **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
1. **리소스** 목록을 확장 하 고 VM을 선택 합니다.
1. 오른쪽의 인스턴스 뷰에서 **provisioningState** 필드를 확인 합니다.

### <a name="fix-the-problem"></a>문제 해결

- **provisioningState**가 **실패**이면 지원 서비스에 문제 해결을 위한 세부 정보를 문의합니다.
- **ProvisioningState** 를 **업데이트**하는 경우 다른 확장을 배포할 수 있습니다. VM에 진행 중인 작업이 있는지 확인 하 고, 작업이 완료 될 때까지 기다린 후 실패 한 Site Recovery "복제 사용" 작업을 다시 시도 합니다.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>대상 VM을 선택할 수 없습니다 (네트워크 선택 탭을 사용할 수 없음).

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>원인 1: VM이 이미 대상 네트워크에 매핑된 네트워크에 연결 되어 있습니다.

원본 VM이 가상 네트워크의 일부이 고 동일한 가상 네트워크의 다른 VM이 이미 대상 리소스 그룹의 네트워크에 매핑된 경우 기본적으로 네트워크 선택 드롭다운 목록 상자를 사용할 수 없습니다 (흐리게 표시 됨).

![네트워크 선택 목록 사용할 수 없음](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>원인 2: 이전에 Site Recovery를 사용 하 여 VM을 보호 한 후 복제를 사용 하지 않도록 설정 했습니다.

VM의 복제를 사용 하지 않도록 설정 해도 네트워크 매핑이 삭제 되지 않습니다. 이 매핑은 VM이 보호 된 Recovery Services 자격 증명 모음에서 삭제 해야 합니다. *Recovery Services 자격 증명 모음* > **Site Recovery 인프라** > **네트워크 매핑**으로 이동 합니다.

![네트워크 매핑 삭제](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

재해 복구를 설정 하는 동안 구성 된 대상 네트워크는 VM을 보호 한 후 초기 설치 후에 변경할 수 있습니다.

![네트워크 매핑 수정](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

네트워크 매핑을 변경 하면 동일한 네트워크 매핑을 사용 하는 모든 보호 된 Vm에 영향을 줍니다.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM + 또는 볼륨 섀도 복사본 서비스 오류 (오류 코드 151025)

이 오류가 발생 하면 다음과 같은 메시지가 나타납니다.

> "Site Recovery 확장을 설치 하지 못했습니다."

### <a name="possible-causes"></a>가능한 원인

- COM + 시스템 응용 프로그램 서비스를 사용할 수 없습니다.
- 볼륨 섀도 복사본 서비스를 사용할 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

COM + 시스템 응용 프로그램 및 볼륨 섀도 복사본 서비스를 자동 또는 수동 시작 모드로 설정 합니다.

1. Windows에서 서비스 콘솔을 엽니다.
1. COM + 시스템 응용 프로그램 및 볼륨 섀도 복사본 서비스가 **시작 유형**으로 **사용 안 함으로** 설정 되지 않았는지 확인 합니다.

    ![COM + 시스템 응용 프로그램의 시작 유형 및 볼륨 섀도 복사본 서비스를 확인 합니다.](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>지원 되지 않는 관리 디스크 크기 (오류 코드 150172)

이 오류가 발생 하면 다음과 같은 메시지가 나타납니다.

> "가상 컴퓨터에 대 한 보호를 사용 하도록 설정할 수 없습니다 .이는 크기가 *diskname*인 *diskname* ) * 최소 지원 크기인 1024 MB 보다 작은 것입니다."

### <a name="possible-cause"></a>가능한 원인

디스크가 지원 되는 크기인 1024 MB 보다 작습니다.

### <a name="fix-the-problem"></a>문제 해결

디스크 크기가 지원 되는 크기 범위 내에 있는지 확인 한 후 작업을 다시 시도 하십시오.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>GRUB 구성에 UUID 대신 장치 이름이 포함 되어 있으므로 보호가 사용 되지 않습니다 (오류 코드 151126).

### <a name="possible-cause"></a>가능한 원인

Linux GRUB 구성 파일 (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/boot/grub2/grub.cfg 또는/etc/default/grub)은 *root* 및 *resume* 매개 변수에 대해 UUID 값 대신 실제 장치 이름을 지정할 수 있습니다. 장치 이름이 변경 될 수 있으므로 Site Recovery에는 Uuid가 필요 합니다. 다시 시작 시 VM이 장애 조치 (failover) 시 동일한 이름으로 나타나지 않을 수 있으므로 문제가 발생 합니다.

다음 예는 장치 이름 (굵은 글꼴로 표시)이 필요한 Uuid 대신 표시 되는 GRUB 파일의 줄입니다.

- 파일/boot/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- 파일:/boot/grub/menu.lst

  > kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1** 스플래시 = Silent crashkernel = 256M-: 128m showopts vga = 0x314


### <a name="fix-the-problem"></a>문제 해결

각 장치 이름을 해당 하는 UUID로 바꿉니다.

1. **Blkid** ***장치 이름***명령을 실행 하 여 장치의 UUID를 찾습니다. 예:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 장치 이름을 uuid ( **root = uuid**=*uuid* , **resume = uuid**=*uuid*형식으로 바꿉니다. 예를 들어, 바꾼 후/boot/grub/menu.lst (앞에서 설명한)의 줄은 다음과 같습니다.

    > kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** 스플래시 = Silent crashkernel = 256M-: 128m showopts vga = 0x314

1. 보호를 다시 시도 하세요.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>GRUB 구성에서 언급 한 장치가 존재 하지 않으므로 보호를 사용 하도록 설정 하지 못했습니다 (오류 코드 151124).

### <a name="possible-cause"></a>가능한 원인

GRUB 구성 파일 (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/grub2/grub.cfg 또는/etc/default/grub)은 *rd.lvm.lv* 또는 *rd_LVM_LV*매개 변수를 포함할 수 있습니다. 이러한 매개 변수는 부팅 시 검색 되는 LVM (논리 볼륨 관리자) 장치를 식별 합니다. 이러한 LVM 장치가 존재 하지 않는 경우 보호 된 시스템 자체는 부팅 되지 않으므로 부팅 프로세스에서 중단 됩니다. 동일한 문제가 장애 조치 (failover) VM에도 표시 됩니다. 다음은 몇 가지 예입니다.

- File:/boot/grub2/grub.cfg on RHEL7:

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/sv/sii/rhel_mup--rhel7u6 ro crashkernel = 128M\@64M rootvg **/root rootvg/root** rhgb//root quiet LANG = en_US. UTF-8

- File:/etc/default/grub on RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **rootvg/root. lv = rootvg/swap** rhgb quiet"

- File:/boot/grub/menu.lst on RHEL6:

    > kernel/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC keytable = us **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb

각 예제에서 굵게 표시 된 부분은 GRUB가 볼륨 그룹 "rootvg"에서 이름이 "root" 및 "swap" 인 두 개의 LVM 장치를 검색 해야 함을 보여 줍니다.

### <a name="fix-the-problem"></a>문제 해결

LVM 장치가 없으면 해당 장치를 만들거나 GRUB 구성 파일에서 해당 매개 변수를 제거 합니다. 그런 다음 보호를 사용 하도록 다시 시도 합니다.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Site Recovery 모바일 서비스 업데이트가 완료 되었지만 경고가 발생 했습니다 (오류 코드 151083).

Site Recovery 모바일 서비스에는 많은 구성 요소가 있으며,이 중 하나를 필터 드라이버 라고 합니다. 시스템을 다시 시작 하는 동안에만 필터 드라이버가 시스템 메모리에 로드 됩니다. 모바일 서비스 업데이트에 필터 드라이버 변경 내용이 포함 될 때마다 컴퓨터는 업데이트 되지만 일부 수정 프로그램을 다시 시작 해야 한다는 경고가 계속 표시 됩니다. 이 경고는 다시 시작 하는 동안에만 발생 하는 새 필터 드라이버가 로드 된 경우에만 필터 드라이버 수정이 적용 될 수 있기 때문에 나타납니다.

> [!NOTE]
> 단지 경고일 뿐입니다. 기존 복제는 새 에이전트 업데이트 후에도 계속 작동 합니다. 새 필터 드라이버의 이점을 원할 때마다 다시 시작 하도록 선택할 수 있지만,를 다시 시작 하지 않으면 이전 필터 드라이버가 계속 작동 합니다.
>
> 필터 드라이버와는 별도로, 모바일 서비스 업데이트의 다른 향상 된 기능 및 수정 사항에 대 한 이점은 다시 시작 없이 적용 됩니다.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>대상 리소스 그룹에 필요한 태그가 없는 복제본 관리 디스크가 이미 있으므로 보호를 사용 하도록 설정할 수 없습니다 (오류 코드 150161).

### <a name="possible-cause"></a>가능한 원인

이 문제는 이전에 가상 컴퓨터를 보호 하는 경우에 발생할 수 있으며, 복제가 사용 하지 않도록 설정 된 경우에는 복제본 디스크가 정리 되지 않습니다.

### <a name="fix-the-problem"></a>문제 해결

오류 메시지에서 식별 된 복제본 디스크를 삭제 하 고 실패 한 보호 작업을 다시 시도 하십시오.

## <a name="next-steps"></a>다음 단계

[Azure 가상 머신 복제](site-recovery-replicate-azure-to-azure.md)
