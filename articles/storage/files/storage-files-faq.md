---
title: Azure Files에 대한 FAQ(질문과 대답) | Microsoft Docs
description: Azure Files에 대한 질문과 대답을 확인합니다.
services: storage
author: roygara
ms.service: storage
ms.date: 01/02/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 77f07394eb895c08ef3e366bd486b4270714c2fc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766273"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Azure Files에 대한 FAQ(질문과 대답)
[Azure Files](storage-files-introduction.md)는 산업 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure 파일 공유를 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포에 동시에 탑재할 수 있습니다. 데이터가 사용되는 위치 가까이에 대한 빠른 액세스를 위해 Azure 파일 동기화를 사용하여 Windows Server 컴퓨터에서 Azure 파일 공유를 캐시할 수도 있습니다.

이 문서는 Azure Files와 Azure 파일 동기화 사용을 비롯하여 Azure Files 기능에 대한 일반적인 질문에 대답합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. 이 문서의 의견 섹션입니다.
2. [Azure Storage 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft 지원 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택합니다.

## <a name="general"></a>일반
* <a id="why-files-useful"></a>
  **Azure Files는 어떻게 유용한가요?**  
   Azure Files를 사용하여 물리적 서버, 디바이스 또는 어플라이언스의 오버 헤드를 관리하지 않고도 클라우드에서 파일 공유를 만들 수 있습니다. OS 업데이트 적용 및 잘못된 디스크 교체를 포함한 사용자를 위한 단조로운 작업을 수행합니다. Azure Files를 유용하게 사용할 수 있는 시나리오에 대한 자세한 내용은 [Azure Files가 유용한 이유](storage-files-introduction.md#why-azure-files-is-useful)를 참조하세요.

* <a id="file-access-options"></a>
  **Azure Files에서 파일에 액세스하는 다른 방법은 무엇인가요?**  
    SMB 3.0 프로토콜을 사용하여 로컬 컴퓨터에 파일 공유를 탑재하거나 [Storage 탐색기](https://storageexplorer.com/)와 같은 도구를 사용하여 파일 공유의 파일을 액세스할 수 있습니다. 애플리케이션에서 스토리지 클라이언트 라이브러리, REST API, Powershell 또는 Azure CLI를 사용하여 Azure 파일 공유의 파일에 액세스할 수 있습니다.

* <a id="what-is-afs"></a>
  **Azure 파일 동기화란?**  
    Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server 컴퓨터를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS(네트워크 파일 시스템) 및 FTPS(파일 전송 프로토콜 서비스)를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

* <a id="files-versus-blobs"></a>
  **데이터를 위해 Azure 파일 공유 및 Azure Blob Storage를 사용해야 하는 이유는 각각 무엇인가요?**  
    Azure Files 및 Azure Blob Storage는 둘 다 대량의 데이터를 클라우드에 저장하는 방법을 제공하지만 약간 다른 용도에서 유용합니다. 
    
    Azure Blob 스토리지는 구조화되지 않은 데이터를 저장해야 하는 대규모 클라우드 네이티브 애플리케이션에 유용합니다. 성능 및 확장성을 최대화하기 위해 Azure Blob Storage는 실제 파일 시스템보다 더 간단한 스토리지 추상화를 구현합니다. REST 기반 클라이언트 라이브러리를 통해서만(또는 REST 기반 프로토콜을 통해 직접) Azure Blob Storage에 액세스할 수 있습니다.

    Azure Files는 특히 파일 시스템입니다. Azure Files에는 다년간의 온-프레미스 운영 체제 사용에서 알고 있고 애용하는 모든 파일 요약이 있습니다. Azure Blob Storage와 같이 Azure Files에서는 REST 인터페이스 및 REST 기반 클라이언트 라이브러리를 제공합니다. Azure Blob Storage와 달리 Azure Files는 Azure 파일 공유에 대한 SMB 액세스를 제공합니다. SMB를 사용하여 코드를 쓰거나 파일 시스템에 특수한 드라이버를 연결하지 않고도, Windows, Linux 또는 macOS, 온-프레미스 또는 클라우드 VM에 Azure 파일 공유를 직접 탑재할 수 있습니다. 데이터가 사용되는 위치 가까이에 대한 빠른 액세스를 위해 Azure 파일 동기화를 사용하여 온-프레미스 파일 서버에서 Azure 파일 공유를 캐시할 수도 있습니다. 
   
    Azure Files 및 Azure Blob Storage 간의 차이점에 대한 심도 있는 설명을 보려면 [Azure Blob Storage, Azure Files 또는 Azure 디스크를 사용해야 하는 경우 결정](../common/storage-decide-blobs-files-disks.md)을 참조하세요. Azure Blob Storage에 대한 자세한 내용은 [Blob Storage 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

* <a id="files-versus-disks"></a>**Azure 디스크 대신 Azure 파일 공유를 사용하는 이유는 무엇인가요?**  
    Azure 디스크의 디스크는 단순히 디스크입니다. Azure 디스크에서 값을 가져오려면 Azure에서 실행 중인 가상 머신에 디스크를 연결해야 합니다. 온-프레미스 서버에서 디스크를 사용하는 모든 항목에 대해 Azure 디스크를 사용할 수 있습니다. OS 시스템 디스크, OS에 대한 스왑 공간 또는 애플리케이션에 대한 전용 스토리지로 사용할 수 있습니다. Azure 디스크의 유용한 용도는 Azure 파일 공유를 사용할 수 있는 동일한 장소에서 사용하기 위해 클라우드에 파일 서버를 만들 수 있다는 것입니다. Azure Virtual Machines에 파일 서버를 배포하는 일은 현재 Azure Files에서 지원하지 않는 배포 옵션(예: NFS 프로토콜 지원 또는 Premium Storage)이 필요할 때 Azure에서 파일 스토리지를 얻는 고성능 방식입니다. 

    그러나 Azure 디스크를 백 엔드 저장소로 사용해서 파일 서버를 실행하게 되면 몇 가지 이유로 인해 Azure 파일 공유를 사용할 때보다 일반적으로 훨씬 더 많은 비용이 초래됩니다. 첫째, 디스크 저장소에 대해 요금을 지불하는 것 외에, 하나 이상의 Azure VM을 실행하는 비용도 지불해야 합니다. 둘째, 파일 서버를 실행하는 데 사용 되는 VM도 관리해야 합니다. 예를 들어, OS 업그레이드에 대한 책임이 있습니다. 마지막으로 온-프레미스에 데이터를 캐시하려는 경우 사용자가 이를 위해 DFSR(분산 파일 시스템 복제)과 같은 복제 토폴로지를 설정하고 관리해야 합니다.

    Azure Virtual Machines에 호스트된 Azure Files 및 파일 서버를 둘 다 적절히 활용하는 방법 중 하나는(Azure 디스크를 백 엔드 저장소로 사용하는 것 외에) 클라우드 VM에서 호스트되는 파일 서버에 Azure 파일 동기화를 설치하는 것입니다. Azure 파일 공유가 파일 서버와 동일한 지역에 있으면 클라우드 계층화를 사용하도록 설정하고 사용 가능한 볼륨 공간 비율을 최대값(99%)으로 설정할 수 있습니다. 이렇게 하면 데이터의 최소 중복을 보장합니다. NFS 프로토콜 지원을 필요로 하는 애플리케이션과 같이 파일 서버와 함께 원하는 애플리케이션을 사용할 수도 있습니다.

    Azure에서 고성능 및 고가용성 파일 서버를 설정하는 옵션에 대한 자세한 내용은 [Microsoft Azure에서 IaaS VM 게스트 클러스터 배포](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)를 참조하세요. Azure Files 및 Azure 디스크 간의 차이점에 대한 심도 있는 설명을 보려면 [Azure Blob Storage, Azure Files 또는 Azure 디스크를 사용해야 하는 경우 결정](../common/storage-decide-blobs-files-disks.md)을 참조하세요. Azure 디스크에 대한 자세한 내용은 [Azure Managed Disks 개요](../../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

* <a id="get-started"></a>
  **Azure Files 사용을 시작하려면 어떻게 해야 하나요?**  
   Azure 파일을 시작하는 것은 쉽습니다. 먼저 [파일 공유를 만든](storage-how-to-create-file-share.md) 다음 기본 운영 체제에서 탑재합니다. 

  * [Windows에서 탑재](storage-how-to-use-files-windows.md)
  * [Linux에서 탑재](storage-how-to-use-files-linux.md)
  * [macOS에서 탑재](storage-how-to-use-files-mac.md)

    Azure 파일 공유를 배포하여 조직에서 프로덕션 파일 공유를 대신하는 방법에 대한 자세한 지침을 보려면 [Azure Files 배포에 대한 계획](storage-files-planning.md)을 참조하세요.

* <a id="redundancy-options"></a>
  **Azure Files에서 지원되는 저장소 중복 옵션은 무엇인가요?**  
    현재 Azure Files는 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소) 및 GRS(지역 중복 저장소)를 지원합니다. 앞으로 RA-GRS(읽기 액세스 지역 중복 저장소)를 지원할 예정이지만 지금은 일정 계획이 나와 있지 않습니다.

* <a id="tier-options"></a>
  **Azure Files에서 어떤 저장소 계층이 지원되나요?**  
    현재 Azure Files는 표준 저장소 계층만 지원합니다. 이 시점에서 Premium Storage 및 쿨 스토리지 지원에 대해 공유할 타임라인이 없습니다. 
    
    > [!NOTE]
    > Blob 전용 스토리지 계정 또는 Premium Storage 계정에서 Azure 파일 공유를 만들 수 없습니다.

* <a id="give-us-feedback"></a>
  **Azure Files에 특정 기능이 추가되는 것을 정말 보고 싶습니다. 추가해줄 수 있나요?**  
    Azure Files 팀은 당사 서비스에 대한 여러분의 모든 의견을 환영합니다. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 기능 요청에 투표하세요. 많은 새로운 기능으로 고객에게 만족을 드리고 싶습니다.

## <a name="azure-file-sync"></a>Azure 파일 동기화

* <a id="afs-region-availability"></a>
  **어떤 지역에서 Azure 파일 동기화가 지원되나요?**  
    사용 가능한 지역은 Azure 파일 동기화 계획 가이드의 [지역 가용성](storage-sync-files-planning.md#region-availability) 섹션에서 찾을 수 있습니다. 비 공용 지역을 포함한 더 많은 지역이 계속 추가될 것입니다.

* <a id="cross-domain-sync"></a>
  **도메인에 가입된 서버와 도메인에 가입되지 않은 서버를 같은 동기화 그룹에 둘 수 있나요?**  
    예. 동기화 그룹에는 도메인에 가입되지 않은 경우에도 다른 Active Directory 구성원 자격을 갖는 서버 엔드포인트가 포함될 수 있습니다. 이 구성이 기술적으로는 작동하지만, 한 서버의 파일 및 폴더에 대해 정의된 ACL(액세스 제어 목록)은 동기화 그룹의 다른 서버에 의해 적용될 수 없으므로 이러한 구성은 일반적인 구성으로 권장되지 않습니다. 최상의 결과를 얻으려면 동일한 Active Directory 포리스트에 있는 서버 간, 다른 Active Directory 포리스트에 있지만 설정된 트러스트 관계에 있는 서버 간 또는 도메인에 있지 않은 서버 간의 동기화를 권장합니다. 이러한 구성 간의 혼합 사용은 방지하는 것이 좋습니다.

* <a id="afs-change-detection"></a>
  **SMB를 사용하여 또는 포털에서 Azure Files 공유에서 직접 파일을 만들었습니다. 파일을 동기화 그룹의 서버와 동기화하는 데 얼마나 소요되나요?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**같은 파일이 두 서버에서 거의 동시에 변경하는 경우 어떻게 되나요?**  
    Azure 파일 동기화는 간단한 충돌 해결 전략을 사용합니다. 두 개의 서버에서 동시에 변경된 파일에 대한 변경 내용을 유지합니다. 가장 최근에 기록된 변경 내용에 원래 파일 이름이 사용됩니다. 이전 파일에 '원본' 컴퓨터가 있으며 이름 앞에 충돌하는 번호가 추가됩니다. 이 분류법을 따릅니다. 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    예를 들어, CompanyReport.docx의 첫 번째 충돌은 CentralServer가 이전 쓰기가 발생한 위치인 경우 CompanyReport-CentralServer.docx가 됩니다. 두 번째 충돌은 CompanyReport-CentralServer-1.docx라는 이름이 됩니다.

* <a id="afs-storage-redundancy"></a>
  **지역 중복 저장소가 Azure 파일 동기화에서 지원되나요?**  
    예, Azure Files는 LRS(로컬 중복 저장소) 및 GRS(지역 중복 저장소)를 모두 지원합니다. GRS에 대해 구성된 계정에서 쌍으로 연결된 지역 간에 스토리지 계정 장애 조치를 시작하는 경우 새 지역을 데이터 백업으로만 처리하는 것이 좋습니다. Azure 파일 동기화는 새 주 영역과의 동기화를 자동으로 시작하지 않습니다. 

* <a id="sizeondisk-versus-size"></a>
  **Azure 파일 동기화를 사용한 후에 Azure 파일에 대한 *디스크 크기* 속성이 *크기* 속성과 일치하지 않는 이유는 무엇인가요?**  
  [클라우드 계층화 이해](storage-sync-cloud-tiering.md#sizeondisk-versus-size) 참조

* <a id="is-my-file-tiered"></a>
  **파일이 계층화되어 있는지 여부를 어떻게 알 수 있나요?**  
  [클라우드 계층화 이해](storage-sync-cloud-tiering.md#is-my-file-tiered) 참조

* <a id="afs-recall-file"></a>**사용하려는 파일이 계층화되어 있습니다. 파일을 로컬에서 사용하기 위해 디스크로 회수할 수 있는 방법은 무엇인가요?**  
  [클라우드 계층화 이해](storage-sync-cloud-tiering.md#afs-recall-file) 참조

* <a id="afs-force-tiering"></a>
  **파일 또는 디렉터리를 강제로 계층화하려면 어떻게 해야 하나요?**  
  [클라우드 계층화 이해](storage-sync-cloud-tiering.md#afs-force-tiering) 참조

* <a id="afs-effective-vfs"></a>
  **볼륨에 여러 서버 엔드포인트가 있는 경우 *사용 가능한 볼륨 공간*은 어떻게 해석되나요?**  
  [클라우드 계층화 이해](storage-sync-cloud-tiering.md#afs-effective-vfs) 참조

* <a id="afs-files-excluded"></a>
  **어떤 파일과 폴더가 Azure 파일 동기화에서 자동으로 제외되나요?**  
    기본적으로 Azure 파일 동기화는 다음 파일을 제외합니다.
  * desktop.ini
  * thumbs.db
  * ehthumbs.db
  * ~$\*.\*
  * \*.laccdb
  * \*.tmp
  * 635D02A9D91C401B97884B82B3BCDAEA.\*

    또한 다음 폴더는 기본적으로 제외됩니다.

  * \System Volume Information
  * \$RECYCLE.BIN
  * \SyncShareState

* <a id="afs-os-support"></a>
  **Windows Server 2008 R2, Linux 또는 NAS(Network Attached Storage) 디바이스와 함께 Azure 파일 동기화를 사용할 수 있습니까?**  
    현재, Azure 파일 동기화는 Windows Server 2016 및 Windows Server 2012 R2만 지원합니다. 지금은 알려드릴 수 있는 다른 계획이 없지만 고객의 요구에 따라 얼마든지 추가 플랫폼을 지원할 수도 있습니다. 지원받고 싶은 플랫폼이 있으면 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 알려주세요.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **계층화된 파일이 서버 엔드포인트 네임스페이스 외부에 존재하는 이유는 무엇인가요?**  
    Azure 파일 동기화 에이전트 버전 3 이전에 Azure 파일 동기화는 서버 엔드포인트인 동일한 볼륨이 아닌 서버 엔드포인트 외부에서 계층화된 파일의 이동을 차단합니다. 복사 작업, 계층화되지 않은 파일의 이동 및 다른 볼륨에 계층화된 파일의 이동은 영향을 받지 않았습니다. 이 동작의 이유는 파일 탐색기 및 다른 Windows API에서 동일한 볼륨에서의 이동 작업이 거의 순간적인 이름 바꾸기 작업이라는 암시적 가정 때문이었습니다. 즉, 이동하면 Azure 파일 동기화가 클라우드의 데이터를 다시 호출하는 동안 파일 탐색기 또는 다른 이동 방법(예: 명령줄 또는 PowerShell)이 응답하지 않는다고 표시됩니다. [Azure 파일 동기화 에이전트 버전 3.0.12.0](storage-files-release-notes.md#supported-versions)부터 Azure 파일 동기화를 사용하면 외부 서버 엔드포인트에서 계층화된 파일을 이동할 수 있습니다. 계층화된 파일을 서버 엔드포인트 외부에서 계층화된 파일로 유지한 다음, 백그라운드에서 파일을 회수하여 앞에서 언급한 부정적인 영향을 방지합니다. 즉, 동일한 볼륨에서의 이동은 즉각적이고, 이동이 완료되면 파일을 디스크로 회수하는 모든 작업을 수행합니다. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **서버의 Azure 파일 동기화에 문제가 발생했습니다(동기화, 클라우드 계층화 등). 서버 엔드포인트를 제거하고 다시 만들어야 하나요?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **저장소 동기화 서비스 및/또는 저장소 계정을 다른 리소스 그룹이나 구독으로 이동할 수 있나요?**  
   예, 저장소 동기화 서비스 및/또는 저장소 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 저장소 계정이 이동되는 경우 저장소 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 저장소 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동할 수 없습니다.
    
* <a id="afs-ntfs-acls"></a>
  **Azure 파일 동기화는 Azure Files에 저장된 데이터와 함께 디렉터리/파일 수준 NTFS ACL을 보존하나요?**

    온-프레미스 파일 서버에서 수행된 NTFS ACL은 Azure 파일 동기화를 통해 메타데이터로 지속됩니다. Azure Files는 Azure 파일 동기화 서비스에서 관리되는 파일 공유에 액세스하기 위한 Azure AD 자격 증명을 통한 인증을 지원하지 않습니다.
    
## <a name="security-authentication-and-access-control"></a>보안, 인증 및 액세스 제어
* <a id="ad-support"></a>
**Azure Files에서 Active Directory 기반 인증 및 액세스 제어를 지원하나요?**  
    
    예. Azure Files는 Azure AD(Azure Active Directory)(미리 보기)를 통한 ID 기반 인증 및 액세스 제어를 지원합니다. Azure Files에 대한 SMB를 통한 Azure AD 인증을 사용하면 Azure Active Directory Domain Services를 통해 도메인 가입 VM이 Azure AD 자격 증명으로 공유, 디렉터리 및 파일에 액세스할 수 있습니다. 자세한 내용은 [Azure Files(미리 보기)에 대한 SMB를 통한 Azure Active Directory 인증 개요](storage-files-active-directory-overview.md)를 참조하세요. 

    Azure Files는 액세스 제어를 관리하기 위한 두 가지 방법을 추가로 제공합니다.

    - SAS(공유 액세스 서명)를 사용하여 특정 사용 권한을 가지며, 지정된 시간 간격에 유효한 토큰을 생성할 수 있습니다. 예를 들어, 만료 시간으로 10분이 있는 특정 파일에 대한 읽기 전용 액세스 권한이 있는 토큰을 생성할 수 있습니다. 토큰이 유효한 동안 토큰을 소유한 사람은 해당 10분 동안 해당 파일에 대한 읽기 전용 액세스 권한을 가집니다. 현재, 공유 액세스 서명 키는 REST API를 통해 또는 클라이언트 라이브러리에서만 지원됩니다. 저장소 계정 키를 사용하여 SMB를 통해 Azure 파일 공유를 탑재해야 합니다.

    - Azure 파일 동기화는 동기화하는 모든 서버 엔드포인트에 대한 모든 임의 ACL 또는 DACL(Active Directory 기반 또는 로컬 여부)을 유지합니다. Windows Server는 Active Directory에서 이미 인증을 받을 수 있으므로 Azure 파일 동기화는 Active Directory 기반 인증 및 ACL의 전체 지원이 제공될 때까지 효과적인 일시적 옵션입니다.

* <a id="ad-support-regions"></a>
**Azure Files에 대한 SMB를 통한 Azure AD의 미리 보기는 모든 Azure 지역에서 사용할 수 있나요?**

    이 미리 보기는 모든 공용 지역에서 사용할 수 있습니다.

* <a id="ad-support-on-premises"></a>
**Azure Files(미리 보기)에 대한 SMB를 통한 Azure AD 인증은 온-프레미스 컴퓨터에서 Azure AD를 사용한 인증을 지원하나요?**

    아니요. Azure Files는 미리 보기 릴리스에서는 온-프레미스 컴퓨터에서 Azure AD를 사용한 인증을 지원하지 않습니다.

* <a id="ad-support-devices"></a>
**Azure Files(미리 보기)에 대한 SMB를 통한 Azure AD 인증은 Azure AD에 가입되거나 등록된 디바이스에서 Azure AD 자격 증명을 사용한 SMB 액세스를 지원하나요?**

    아니요. 이 시나리오는 지원되지 않습니다.

* <a id="ad-support-rest-apis"></a>
**디렉터리/파일 NTFS ACL 가져오기/설정/복사를 지원하는 REST API가 있나요?**

    미리 보기 릴리스에서는 디렉터리 또는 파일에 대한 NTFS ACL을 가져오거나, 설정하거나, 복사하는 REST API를 지원하지 않습니다.

* <a id="ad-vm-subscription"></a>
**다른 구독으로 VM에서 Azure AD 자격 증명을 사용하여 Azure Files에 액세스할 수 있나요?**

    파일 공유가 배포된 구독과 VM이 도메인에 조인된 Azure AD Domain Services 배포와 동일한 Azure AD 테넌트가 연결되어 있는 경우 동일한 Azure AD 자격 증명을 사용하여 Azure Files에 액세스할 수 있습니다. 제한 사항은 연결된 Azure AD 테넌트가 아닌 구독에만 적용됩니다.    
    
* <a id="ad-support-subscription"></a>
**파일 공유가 연결된 기본 테넌트와는 다른 Azure AD 테넌트를 사용하여 Azure Files에 대한 SMB를 통한 Azure AD 인증을 사용하도록 설정할 수 있나요?**

    아니요. Azure Files는 파일 공유와 동일한 구독에 있는 Azure AD 테넌트와의 Azure AD 통합만 지원합니다. 하나의 구독만 Azure AD 테넌트와 연결할 수 있습니다.

* <a id="ad-linux-vms"></a>
**Azure Files(미리 보기)에 대한 SMB를 통한 Azure AD 인증은 Linux VM을 지원하나요?**

    아니요. 미리 보기 릴리스에서는 Linux VM의 인증을 지원하지 않습니다.

* <a id="ad-aad-smb-afs"></a>
**Azure 파일 동기화에서 관리되는 파일 공유에서 SMB 기능을 통한 Azure AD 인증을 이용할 수 있나요?**

    아니요. Azure Files는 Azure 파일 동기화에서 관리되는 파일 공유에서 NTFS ACL 보존을 지원하지 않습니다. 온-프레미스 파일 서버에서 수행된 파일 ACL은 Azure 파일 동기화를 통해 지속됩니다. Azure Files에 대해 기본적으로 구성된 NTFS ACL은 Azure 파일 동기화 서비스가 덮어씁니다. 또한 Azure Files는 Azure 파일 동기화 서비스에서 관리되는 파일 공유에 액세스하기 위한 Azure AD 자격 증명을 통한 인증을 지원하지 않습니다.

* <a id="encryption-at-rest"></a>
**Azure 파일 공유가 미사용 암호화되도록 하려면 어떻게 해야 하나요?**  

    Azure Storage 서비스는 암호화는 모든 지역에서 기본적으로 활성화되도록 처리되고 있습니다. 이런 지역에서는 암호화를 사용하기 위해 아무런 작업도 수행할 필요가 없습니다. 다른 지역의 경우 [서버 쪽 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* <a id="access-via-browser"></a>
**웹 브라우저를 사용하여 특정 파일에 대한 액세스를 제공하려면 어떻게 해야 하나요?**  

    공유 액세스 서명을 사용하여 특정 사용 권한을 가지며, 지정된 시간 간격에 유효한 토큰을 생성할 수 있습니다. 예를 들어, 설정된 기간 동안 특정 파일에 대해 읽기 전용 액세스 권한을 주는 토큰을 생성할 수 있습니다. 이 URL을 소유한 사람은 토큰이 유효한 동안 모든 웹 브라우저에서 파일에 직접 액세스할 수 있습니다. Storage 탐색기와 같은 UI에서 공유 액세스 서명 키를 쉽게 생성할 수 있습니다.

* <a id="file-level-permissions"></a>
**공유 내 폴더에 대한 읽기 전용 또는 쓰기 전용 권한을 지정할 수 있나요?**  

    SMB를 사용하여 파일 공유를 마운트하는 경우 사용 권한에 대한 폴더 수준 제어는 없습니다. 그러나 REST API 또는 클라이언트 라이브러리를 사용하여 공유 액세스 서명을 만드는 경우 공유 내의 폴더에 읽기 전용 또는 쓰기 전용 권한을 지정할 수 있습니다.

* <a id="ip-restrictions"></a>
**Azure 파일 공유에 대한 IP 제한을 구현할 수 있나요?**  

    예. Azure 파일 공유에 대한 액세스는 저장소 계정 수준에서 제한할 수 있습니다. 자세한 내용은 [Azure Storage 방화벽 및 Virtual Networks 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

* <a id="data-compliance-policies"></a>
**Azure Files는 어떤 데이터 규정 준수 정책을 지원하나요?**  

   Azure Files는 Azure Storage의 다른 스토리지 서비스에서 사용되는 동일한 스토리지 아키텍처를 기반으로 하여 실행됩니다. Azure Files는 다른 Azure 저장소 서비스에서 사용되는 동일한 데이터 규정 준수 정책을 적용합니다. Azure Storage 데이터 규정 준수에 대한 자세한 내용은 [Azure Storage 준수 제품](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)을 참조하고, [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx)로 이동하여 참조할 수 있습니다.

## <a name="on-premises-access"></a>온-프레미스 액세스

* <a id="port-445-blocked"></a>
**내 ISP 또는 Azure Files를 실패 하는 IT 블록 포트 445 탑재 합니다. 어떻게 해야 하나요?**

    알아볼 수 있습니다 [차단 문제를 해결 하려면 다양 한 포트 445 여기](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)합니다. Azure Files에서 SMB 3.0을 사용 하 여 (암호화 지원과) 연결만 허용 지역 또는 데이터 센터 외부입니다. SMB 3.0 프로토콜은 매우 안전한 인터넷을 통해 사용 하는 채널 암호화를 비롯 한 여러 보안 기능을 도입 했습니다. 그러나 포트 445는 수는 더 낮은 SMB 버전에서 발견 된 취약점의 기록 이유로 인해 차단 되었습니다. 이상적인 경우 포트에 대 한 SMB 1.0 트래픽에 대해서만 차단 해야 하 고 모든 클라이언트에서 SMB 1.0를 해제 해야 합니다.

* <a id="expressroute-not-required"></a>
**Azure Files에 연결하거나 온-프레미스에서 Azure 파일 동기화를 사용하려면 Azure ExpressRoute를 사용해야 하나요?**  

    아니요. ExpressRoute는 Azure 파일 공유에 액세스하는 데 필요하지 않습니다. Azure 파일 공유를 온-프레미스에 직접 탑재하는 경우 인터넷 액세스를 위해 포트 445(TCP 아웃바운드)만 열어 두면 됩니다(SMB가 통신하기 위해 사용하는 포트). Azure 파일 동기화를 사용하는 경우에는 HTTPS 액세스를 위해 포트 443(TCP 아웃바운드)만 열어 두면 됩니다(SMB 필요 없음). 그러나 이러한 액세스 옵션 중 하나로 ExpressRoute를 사용*할 수 있습니다*.

* <a id="mount-locally"></a>
**내 로컬 컴퓨터에서 Azure 파일 공유를 탑재하려면 어떻게 해야 하나요?**  

    445 포트(TCP 아웃바운드)가 열려 있고 클라이언트(예: Windows 10 또는 Windows Server 2016을 사용하는 경우)에서 SMB 3.0 프로토콜을 지원하는 경우 SMB 프로토콜을 사용하여 파일 공유를 탑재할 수 있습니다. 포트 445가 조직의 정책이나 ISP에 의해 차단되어 있으면 Azure 파일 동기화를 사용하여 Azure 파일 공유에 액세스할 수 있습니다.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**내 Azure 파일 공유를 백업하려면 어떻게 하나요?**  
    실수로 삭제하지 않도록 보호하기 위해 주기적인 [공유 스냅숏](storage-snapshots-files.md)을 사용할 수 있습니다. 탑재된 파일 공유를 백업할 수 있는 AzCopy, RoboCopy 또는 타사 백업 도구를 사용할 수도 있습니다. Azure Backup에서는 Azure Files의 백업을 제공합니다. [Azure Backup으로 Azure 파일 공유 백업](https://docs.microsoft.com/azure/backup/backup-azure-files)에 대해 자세히 알아봅니다.

## <a name="share-snapshots"></a>공유 스냅숏

### <a name="share-snapshots-general"></a>공유 스냅숏: 일반
* <a id="what-are-snaphots"></a>
**파일 공유 스냅숏이란?**  
    Azure 파일 공유 스냅숏을 사용하여 파일 공유의 읽기 전용 버전을 만들 수 있습니다. 또한 Azure Files를 사용하여 이전 버전의 콘텐츠를 같은 공유 또는 Azure 또는 온-프레미스의 대체 위치에 복사하여 추가로 수정할 수도 있습니다. 공유 스냅숏에 대해 더 자세히 알아보려면 [공유 스냅숏 개요](storage-snapshots-files.md)를 참조하세요.

* <a id="where-are-snapshots-stored"></a>
**내 공유 스냅숏은 어디에 저장되나요?**  
    공유 스냅숏은 파일 공유와 동일한 저장소 계정에 저장됩니다.

* <a id="snapshot-perf-impact"></a>
**공유 스냅숏 사용으로 성능에 미치는 영향이 있나요?**  
    공유 스냅숏은 성능 오버헤드를 발생하지 않습니다.

* <a id="snapshot-consistency"></a>
**공유 스냅숏은 애플리케이션 일치 스냅숏인가요?**  
    아니요, 공유 스냅숏은 애플리케이션 일치 스냅숏이 아닙니다. 사용자는 공유 스냅숏을 작성하기 전에 애플리케이션에서 공유로 쓰기를 플러시해야 합니다.

* <a id="snapshot-limits"></a>
**사용할 수 있는 공유 스냅숏 수에 제한이 있나요?**  
    예. Azure Files는 최대 200개의 공유 스냅숏을 유지할 수 있습니다. 공유 스냅숏은 공유 할당량에 포함되지 않으므로 모든 공유 스냅숏에서 사용되는 총 공간에 대한 공유별 제한은 없습니다. 저장소 계정 제한은 여전히 적용됩니다. 공유 스냅숏이 200개를 넘는 경우 새 공유 스냅숏을 생성하려면 이전 스냅숏을 삭제해야 합니다.

* <a id="snapshot-cost"></a>
**스냅숏 공유 비용은 얼마나 되나요?**  
    표준 트랜잭션 및 표준 저장소 비용이 스냅숏에 적용됩니다. 스냅숏은 기본적으로 증분식입니다. 기본 스냅숏은 공유 자체입니다. 모든 후속 스냅숏은 증분이며, 이전 스냅숏과의 차이만 저장합니다. 즉, 청구서에 표시되는 델타 변경 내용은 워크로드 변동이 적은 경우 최소화됩니다. 표준 Azure 파일 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요. 현재 공유 스냅숏으로 소비되는 크기를 보는 방법은 청구된 용량과 사용된 용량을 비교하는 것입니다. 보고를 개선하기 위한 도구를 개발 중입니다.

* <a id="ntfs-acls-snaphsots"></a>
**디렉터리 및 파일에 대한 NTFS ACL은 공유 스냅숏에서 지속되나요?**
    디렉터리 및 파일에 대한 NTFS ACL은 공유 스냅숏에서 지속됩니다.

### <a name="create-share-snapshots"></a>공유 스냅숏 만들기
* <a id="file-snaphsots"></a>
**개별 파일의 공유 스냅숏을 만들 수 있나요?**  
    공유 스냅숏은 파일 공유 수준에서 만들어집니다. 파일 공유 스냅숏에서 개별 파일을 복원할 수 있지만 파일 수준 공유 스냅숏을 만들 수 없습니다. 그러나 공유 수준 공유 스냅숏을 작성했으며 특정 파일이 변경된 공유 스냅숏을 나열하려면 Windows 탑재 공유의 **이전 버전**에서 이 작업을 수행할 수 있습니다. 
    
    파일 스냅숏 기능이 필요하면 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에 알려주세요.

* <a id="encrypted-snapshots"></a>
**암호화된 파일 공유의 공유 스냅숏을 만들 수 있나요?**  
    미사용 암호화가 사용하도록 설정된 Azure 파일 공유의 공유 스냅숏을 작성할 수 있습니다. 공유 스냅숏에서 암호화된 파일 공유로 파일을 복원할 수 있습니다. 공유가 암호화된 경우 공유 스냅숏도 암호화됩니다.

* <a id="geo-redundant-snaphsots"></a>
**공유 스냅숏이 지역 중복 스냅숏인가요?**  
    공유 스냅숏은 생성된 Azure 파일 공유와 동일한 중복성을 갖습니다. 계정에 대해 지역 중복 저장소를 선택한 경우 공유 스냅숏이 페어링된 지역에도 중복해서 저장됩니다.

### <a name="manage-share-snapshots"></a>공유 스냅숏 관리
* <a id="browse-snapshots-linux"></a>
**Linux에서 내 공유 스냅숏을 찾아볼 수 있나요?**  
    Azure CLI를 사용하여 Linux에서 공유 스냅숏을 생성, 나열, 검색 및 복원할 수 있습니다.

* <a id="copy-snapshots-to-other-storage-account"></a>
**다른 저장소 계정으로 공유 스냅숏을 복사할 수 있나요?**  
    공유 스냅숏의 파일을 다른 위치로 복사할 수 있지만 공유 스냅숏 자체는 복사할 수 없습니다.

### <a name="restore-data-from-share-snapshots"></a>공유 스냅숏에서 데이터 복원
* <a id="promote-share-snapshot"></a>
**공유 스냅숏을 기본 공유로 승격할 수 있나요?**  
    공유 스냅숏의 데이터를 다른 대상으로 복사할 수 있습니다. 공유 스냅숏을 기본 공유로 승격할 수 없습니다.

* <a id="restore-snapshotted-file-to-other-share"></a>
**공유 스냅숏의 데이터를 다른 저장소 계정으로 복원할 수 있나요?**  
    예. 동일한 지역 또는 서로 다른 지역에서 원래 위치 또는 동일한 저장소 계정 또는 다른 저장소 계정을 포함하는 대체 위치로 공유 스냅숏의 파일을 복사할 수 있습니다. 온-프레미스 위치 또는 다른 클라우드로 파일을 복사할 수도 있습니다.    
  
### <a name="clean-up-share-snapshots"></a>공유 스냅숏 정리
* <a id="delete-share-keep-snapshots"></a>
**내 공유 스냅숏을 제외하고 내 공유만 삭제할 수 있나요?**  
    공유에 활성 공유 스냅숏이 있는 경우 공유를 삭제할 수 없습니다. API를 사용하여 공유와 함께 공유 스냅숏을 삭제할 수 있습니다. Azure Portal에서 공유 스냅숏과 공유를 모두 삭제할 수도 있습니다.

* <a id="delete-share-with-snapshots"></a>
**저장소 계정을 삭제하는 경우 공유 스냅숏은 어떻게 되나요?**  
    저장소 계정을 삭제하는 경우 공유 스냅숏도 삭제됩니다.

## <a name="billing-and-pricing"></a>대금 청구 및 가격 책정
* <a id="vm-file-share-network-traffic"></a>
**Azure VM과 Azure 파일 공유 사이의 네트워크 트래픽은 구독에 청구되는 외부 대역폭으로 간주되나요?**  
    파일 공유 및 VM이 동일한 Azure 지역에 있는 경우 파일 공유와 VM 간의 트래픽에 대한 추가 비용은 없습니다. 파일 공유와 VM이 서로 다른 지역에 있는 경우 이들 사이의 트래픽은 외부 대역폭으로 비용 청구됩니다.

* <a id="share-snapshot-price"></a>
**스냅숏 공유 비용은 얼마나 되나요?**  
     미리 보기 동안 스냅숏 용량 공유에는 요금이 청구되지 않습니다. 표준 저장소 송신 및 트랜잭션 비용이 적용됩니다. 일반 공급 후에는 공유 스냅숏에 대한 용량 및 트랜잭션에 대해 구독에 요금이 청구될 예정입니다.
     
     공유 스냅숏은 기본적으로 증분합니다. 기본 공유 스냅숏은 공유 자체입니다. 모든 후속 공유 스냅숏은 증분하며, 이전 공유 스냅숏과의 차이만 저장합니다. 변경된 콘텐츠에 대해서만 요금이 청구됩니다. 100GiB의 데이터 공유가 있으나 마지막 공유 스냅숏 후에 5GiB만 변경된 경우 해당 공유 스냅숏은 추가로 5GiB만 사용하게 되며 105GiB에 대한 요금만 청구됩니다. 트랜잭션 및 표준 송신 요금에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

## <a name="scale-and-performance"></a>확장 및 성능
* <a id="files-scale-limits"></a>
**Azure Files의 크기 제한은 무엇인가요?**  
    Azure Files의 확장성 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](storage-files-scale-targets.md)를 참조하세요.

* <a id="need-larger-share"></a>
**현재 지원하는 Azure Files보다 큰 파일 공유가 필요합니다. 내 Azure 파일 공유 크기를 늘릴 수 있나요?**  
    아니요. Azure 파일 공유의 최대 크기는 5TiB입니다. 현재, 조정할 수 없는 하드 한도입니다. 공유 크기를 100TiB로 늘리기 위한 솔루션을 개발 중이지만 지금은 일정이 나와 있지 않습니다.

* <a id="open-handles-quota"></a>
**동일한 파일에 동시에 액세스할 수 있는 클라이언트는 몇 개인가요?**   
    단일 파일에 대한 열린 핸들 할당량은 2000개입니다. 2000개의 열린 핸들이 있는 경우 할당량에 도달했다는 오류 메시지가 표시됩니다.

* <a id="zip-slow-performance"></a>
**Azure Files의 파일 압축을 풀면 내 성능이 저하됩니다. 어떻게 해야 하나요?**  
    많은 파일을 Azure Files로 전송하려면 AzCopy(Windows용, Linux 및 UNIX용 미리 보기에서) 또는 Azure PowerShell을 사용하는 것이 좋습니다. 이러한 도구는 네트워크 전송을 위해 최적화되었습니다.

* <a id="slow-perf-windows-81-2012r2"></a>
**Windows Server 2012 R2 또는 Windows 8.1에 Azure 파일 공유를 탑재한 후 성능이 느려진 이유는 무엇인가요?**  
    Windows Server 2012 R2 및 Windows 8.1에 Azure 파일 공유를 탑재하는 경우 알려진 문제가 있습니다. Windows 8.1 및 Windows Server 2012 R2용 2014년 4월 누적 업데이트에서 문제가 패치되었습니다. 최적의 성능을 위해 Windows Server 2012 R2 및 Windows 8.1의 모든 인스턴스에 이 패치가 적용되었는지 확인합니다. (Windows 업데이트를 통해 Windows 패치를 항상 받아야 합니다.) 자세한 내용은 관련된 Microsoft 기술 자료 문서인 [Windows 8.1 또는 Server 2012 R2에서 Azure Files에 액세스할 때 성능 저하](https://support.microsoft.com/kb/3114025)를 확인하세요.

## <a name="features-and-interoperability-with-other-services"></a>다른 서비스와의 기능 및 상호 운용성
* <a id="cluster-witness"></a>
**내 Windows Server 장애 조치(Failover) 클러스터에 대해 Azure 파일 공유를 *파일 공유 감시*로 사용할 수 있나요?**  
    현재, 이 구성은 Azure 파일 공유에서 지원되지 않습니다. Azure Blob Storage에 대해 이 기능을 설정하는 방법에 대한 자세한 내용은 [장애 조치(Failover) 클러스터에 대한 클라우드 감시 배포](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요.

* <a id="containers"></a>
**Azure Container Instance에 Azure 파일 공유를 탑재할 수 있나요?**  
    예, Azure 파일 공유는 컨테이너 인스턴스의 수명을 넘어 정보를 유지하려는 경우 탁월한 옵션입니다. 자세한 내용은 [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](../../container-instances/container-instances-mounting-azure-files-volume.md)를 참조하세요.

* <a id="rest-rename"></a>
**REST API에 이름 바꾸기 작업이 있나요?**  
    지금은 없습니다.

* <a id="nested-shares"></a>
**중첩된 공유를 설정할 수 있나요? 즉, 공유 아래에 공유를 설정할 수 있나요?**  
    아니요. 파일 공유*는* 마운트할 수 있는 가상 드라이버이므로 중첩된 공유는 지원되지 않습니다.

* <a id="ibm-mq"></a>
**IBM MQ에서 Azure Files를 어떻게 사용하나요?**  
    IBM은 IBM MQ 고객이 IBM 서비스로 Azure Files를 구성할 수 있도록 돕는 문서를 발표했습니다. 자세한 내용은 [Microsoft Azure 파일 서비스와 IBM MQ 다중 인스턴스 큐 관리자를 설치하는 방법](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
