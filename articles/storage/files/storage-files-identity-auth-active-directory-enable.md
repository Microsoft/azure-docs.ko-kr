---
title: 개요-온-프레미스 AD DS Azure 파일 공유에 대 한 인증
description: Azure 파일 공유에 대 한 AD DS (Active Directory Domain Services) 인증에 대해 알아봅니다. 이 문서에서는 지원 시나리오와 가용성을 자세히 설명 하 고 AD DS와 Azure active directory 간의 사용 권한이 어떻게 작동 하는지 설명 합니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/12/2020
ms.author: rogarana
ms.openlocfilehash: 3faa86fe67e3f0a208bf42dc3e49de8335b25c95
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272334"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>개요-Azure 파일 공유에 대 한 SMB를 통한 온-프레미스 Active Directory Domain Services 인증

[Azure Files](storage-files-introduction.md)   는 온-프레미스 Active Directory Domain Services (AD DS) 및 Azure Active Directory Domain Services (Azure AD DS)의 두 가지 유형의 도메인 서비스를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 인증은 지원에 대 한 올바른 도메인 서비스를 선택 하려면 [작동 방법 섹션](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) 을 검토 하는 것이 좋습니다. 설치는 선택한 도메인 서비스에 따라 달라 집니다. 이러한 일련의 문서는 Azure 파일 공유를 사용 하 여 인증을 위해 온-프레미스 AD DS을 설정 및 구성 하는 데 중점을 둡니다.

Azure 파일 공유를 처음 접하는 경우 다음 일련의 문서를 읽기 전에 [계획 가이드](storage-files-planning.md) 를 읽어 보는 것이 좋습니다.

## <a name="supported-scenarios-and-restrictions"></a>지원 되는 시나리오 및 제한 사항

- Azure Files 온-프레미스 AD DS 인증에 사용 되는 AD DS Id를 Azure AD에 동기화 해야 합니다. 암호 해시 동기화는 선택 사항입니다. 
- Azure File Sync에서 관리 하는 Azure 파일 공유를 지원 합니다.
- 는 RC4-HMAC 암호화를 사용 하 여 AD에서 Kerberos 인증을 지원 합니다. AES Kerberos 암호화는 아직 지원 되지 않습니다.
- Single Sign-On 환경을 지원 합니다.
- Windows 7 또는 Windows Server 2008 r 2 보다 최신 버전의 OS에서 실행 되는 클라이언트 에서만 지원 됩니다.
- 저장소 계정이 등록 된 AD 포리스트에 대해서만 지원 됩니다. 기본적으로 단일 포리스트의 AD DS 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 다른 포리스트에서 Azure 파일 공유에 액세스 해야 하는 경우 적절 한 포리스트 트러스트를 구성 했는지 확인 하세요. 자세한 내용은 [FAQ](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) 를 참조 하세요.
- 는 AD DS에서 생성 되는 컴퓨터 계정에 대 한 인증을 지원 하지 않습니다. 

SMB를 통해 Azure 파일 공유에 대 한 AD DS를 사용 하도록 설정 하면 AD DS 가입 된 컴퓨터에서 기존 AD DS 자격 증명을 사용 하 여 Azure 파일 공유를 탑재할 수 있습니다. 온-프레미스 컴퓨터 또는 Azure에서 호스트 되는 AD DS 환경을 사용 하 여이 기능을 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 몇 가지 일반적인 사용 사례에 대 한 Azure Files AD 인증을 설정 하는 데 도움이 되도록 다음 시나리오에 대 한 단계별 지침을 포함 하는 두 개의 비디오를 게시 했습니다.
> - [온-프레미스 파일 서버를 Azure Files로 바꾸기 (파일 및 AD 인증을 위한 개인 링크의 설정 포함)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Windows 가상 데스크톱에 대 한 프로필 컨테이너로 Azure Files 사용 (AD 인증 및 FsLogix 구성의 설정 포함)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>사전 요구 사항 

Azure 파일 공유에 대 한 AD DS 인증을 사용 하도록 설정 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다. 

- [AD DS 환경을](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 선택 하거나 만들고 Azure AD Connect를 사용 [하 여 Azure AD에 동기화](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 합니다. 

    새 온-프레미스 또는 기존 온-프레미스 AD DS 환경에서이 기능을 사용 하도록 설정할 수 있습니다. 액세스에 사용 되는 id를 Azure AD와 동기화 해야 합니다. 액세스 하는 Azure AD 테 넌 트와 파일 공유는 동일한 구독과 연결 되어 있어야 합니다.

- 온-프레미스 컴퓨터 또는 Azure VM을 온-프레미스 AD DS에 도메인 가입 합니다. 도메인에 가입 하는 방법에 대 한 자세한 내용은 [도메인에 컴퓨터 가입](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)을 참조 하세요.

    컴퓨터가 AD DS 도메인에 가입 되지 않은 경우에도 컴퓨터에 AD 도메인 컨트롤러의 시야가 있는 경우 인증을 위해 AD 자격 증명을 활용할 수 있습니다.

- Azure storage 계정을 선택 하거나 만듭니다.  최적의 성능을 위해서는 공유에 액세스할 계획인 클라이언트와 동일한 지역에 저장소 계정을 배포 하는 것이 좋습니다. 그런 다음 저장소 계정 키를 사용 하 여 [Azure 파일 공유를 탑재](storage-how-to-use-files-windows.md) 합니다. 저장소 계정 키를 탑재 하면 연결이 확인 됩니다.

    파일 공유가 포함 된 저장소 계정이 Azure AD DS 인증에 대해 아직 구성 되지 않았는지 확인 합니다. 저장소 계정에서 Azure AD DS 인증을 사용 하도록 설정한 Azure Files 경우 온-프레미스 AD DS를 사용 하도록 변경 하기 전에 사용 하지 않도록 설정 해야 합니다. 이는 Azure AD DS 환경에서 구성 된 기존 Acl을 적절 한 사용 권한을 적용 하도록 다시 구성 해야 함을 의미 합니다.

    Azure Files에 연결 하는 데 문제가 발생 하는 경우 [Windows에서 Azure Files 탑재 오류에 대해 게시 된 문제 해결 도구](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)를 참조 하세요. 또한 포트 445이 차단 될 때 시나리오를 해결 하기 위한 [지침](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 을 제공 합니다. 

- Azure 파일 공유에 대 한 AD DS 인증을 설정 하 고 구성 하기 전에 관련 네트워킹 구성을 수행 합니다. 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md) 을 참조 하세요.

## <a name="regional-availability"></a>국가별 가용성

AD DS를 사용 하는 Azure Files 인증은 [모든 Azure 공용 및 .gov 지역](https://azure.microsoft.com/global-infrastructure/locations/)에서 사용할 수 있습니다.

## <a name="overview"></a>개요

파일 공유에서 네트워킹 구성을 사용 하도록 설정 하려는 경우 [네트워킹 고려 사항](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) 문서를 읽고 AD DS 인증을 사용 하도록 설정 하기 전에 관련 구성을 완료 하는 것이 좋습니다.

Azure 파일 공유에 대 한 AD DS 인증을 사용 하도록 설정 하면 온-프레미스 AD DS 자격 증명을 사용 하 여 Azure 파일 공유에 인증할 수 있습니다. 또한 세부적인 액세스 제어를 허용 하기 위해 사용 권한을 보다 잘 관리할 수 있습니다. 이렇게 하려면 AD connect를 사용 하 여 온-프레미스 AD DS에서 Azure AD로 id를 동기화 해야 합니다. 온-프레미스 AD DS 자격 증명을 사용 하 여 파일/공유 수준 액세스를 관리 하는 동안 Azure AD에 동기화 된 id로 공유 수준 액세스를 제어 합니다.

다음으로 AD DS 인증에 대 한 Azure Files를 설정 하려면 다음 단계를 수행 합니다. 

1. [1 부: 저장소 계정에 대 한 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)

1. [2 부: 대상 AD id와 동기화 되는 Azure AD id (사용자, 그룹 또는 서비스 주체)에 공유에 대 한 액세스 권한 할당](storage-files-identity-ad-ds-assign-permissions.md)

1. [3 부: 디렉터리 및 파일에 대해 SMB를 통한 Windows Acl 구성](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [4 부: AD DS에 조인 된 VM에 Azure 파일 공유 탑재](storage-files-identity-ad-ds-mount-file-share.md)

1. [AD DS에서 저장소 계정 id의 암호를 업데이트 합니다.](storage-files-identity-ad-ds-update-password.md)

다음 다이어그램에서는 Azure 파일 공유에 대해 SMB를 통한 Azure AD 인증을 사용 하도록 설정 하는 종단 간 워크플로를 보여 줍니다. 

![파일 광고 워크플로 다이어그램](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Azure 파일 공유에 액세스 하는 데 사용 되는 id를 azure AD에 동기화 하 여 azure [역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md) 모델을 통해 공유 수준 파일 권한을 적용 해야 합니다. 기존 파일 서버에서 전달 되는 파일/디렉터리의 [Windows 스타일 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 은 유지 되 고 적용 됩니다. 이를 통해 엔터프라이즈 AD DS 환경과 원활한 통합이 가능 합니다. 온-프레미스 파일 서버를 Azure 파일 공유로 바꾸면 기존 사용자가 사용 중인 자격 증명을 변경 하지 않고 Single Sign-On 환경을 사용 하 여 현재 클라이언트에서 Azure 파일 공유에 액세스할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

Azure 파일 공유에 대해 온-프레미스 AD DS 인증을 사용 하도록 설정 하려면 다음 문서를 계속 진행 합니다.

[1 부: 계정에 대 한 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)
