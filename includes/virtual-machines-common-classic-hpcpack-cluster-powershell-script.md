---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485602"
---
사용자의 환경과 선택 사항에 따라 스크립트를 사용하여 Azure 가상 네트워크, 저장소 계정, 클라우드 서비스, 도메인 컨트롤러, 원격 또는 로컬 SQL 데이터베이스, 헤드 노드, 추가 클러스터 노드를 포함한 모든 클러스터 인프라를 만들 수 있습니다. 또는 스크립트에서 기존 Azure 인프라를 사용하여 HPC 클러스터 노드만 만들 수도 있습니다.

HPC 팩 클러스터 계획에 대한 배경 정보는 HPC 팩 2012 R2 TechNet 라이브러리에서 [제품 평가 및 계획](https://technet.microsoft.com/library/jj899596.aspx)과 [시작하기](https://technet.microsoft.com/library/jj899590.aspx) 콘텐츠를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**: Azure Global 또는 Azure 중국 서비스에서 구독을 사용할 수 있습니다. 구독 제한은 배포할 수 있는 클러스터 노드의 수와 유형에 영향을 줍니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../articles/azure-subscription-service-limits.md)을 참조하세요.
* **Windows 클라이언트 컴퓨터와 Azure PowerShell 0.8.10 이상이 설치 및 구성**: 참조 [Azure PowerShell을 사용 하 여 시작](/powershell/azureps-cmdlets-docs) 설치 하기 위한 지침 및 단계 Azure 구독에 연결 합니다.
* **HPC Pack IaaS 배포 스크립트**: 다운로드 하 고 최신 버전에서 스크립트의 압축을 풉니다 합니다 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)합니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인합니다. 이 문서는 버전 4.5.2의 스크립트를 기반으로 합니다.
* **스크립트 구성 파일**: 스크립트를 사용 하 여 HPC 클러스터를 구성 하는 XML 파일을 만듭니다. 정보 및 예제는 이 문서의 후반부에 나오는 단원 및 배포 스크립트와 함께 제공되는 Manual.rtf 파일을 참조하세요.

## <a name="syntax"></a>구문
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> 스크립트는 관리자 권한으로 실행합니다.
> 
> 

### <a name="parameters"></a>매개 변수
* **ConfigFile**: HPC 클러스터를 설명 하는 구성 파일의 파일 경로 지정 합니다. 자세한 내용은 이 토픽의 구성 파일 또는 스크립트가 포함된 폴더의 Manual.rtf 파일을 참조하세요.
* **AdminUserName**: 사용자 이름을 지정합니다. 스크립트로 도메인 포리스트를 만든 경우 모든 VM의 로컬 관리자 사용자 이름과 도메인 관리자 이름이 됩니다. 도메인 포리스트가 이미 있는 경우에는 로컬 관리자 이름으로 도메인 사용자를 지정하여 HPC 팩을 설치합니다.
* **AdminPassword**: 관리자 암호를 지정합니다. 명령줄에서 이 매개 변수를 지정하지 않은 경우 스크립트에서 암호를 입력하라는 메시지를 표시합니다.
* **HPCImageName** (선택 사항): HPC 클러스터를 배포 하는 데 사용 하는 HPC Pack VM 이미지 이름을 지정 합니다. Microsoft에서 제공한 Azure Marketplace의 HPC 팩 이미지여야 합니다. 이 매개 변수를 지정하지 않을 경우(일반적으로 지정하는 것이 좋음) 스크립트에서 최근 게시된 [HPC Pack 2012 R2 이미지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)를 선택합니다. 최신 이미지는 HPC Pack 2012 R2 업데이트 3이 설치된 Windows Server 2012 R2 Datacenter를 기준으로 합니다.
  
  > [!NOTE]
  > 유효한 HPC 팩 이미지를 지정하지 않으면 배포가 실패합니다.
  > 
  > 
* **로그 파일** (선택 사항): 배포 로그 파일 경로 지정합니다. 이 매개 변수를 지정하지 않을 경우 스크립트를 실행하는 컴퓨터의 temp 디렉터리에 로그 파일이 만들어집니다.
* **Force** (선택 사항): 모든 확인 메시지를 표시 하지 않습니다.
* **NoCleanOnFailure** (선택 사항): 성공적으로 배포 되지 않은 Azure Vm이 제거 되지 않습니다 지정 합니다. 배포를 계속하려면 스크립트를 다시 실행하기 전에 이러한 VM을 수동으로 제거합니다. 그렇지 않을 경우 배포가 실패합니다.
* **PSSessionSkipCACheck** (선택 사항): 이 스크립트에서 배포한 Vm 사용 하 여 모든 클라우드 서비스에 대 한 자체 서명 된 인증서를 Azure에서 자동으로 생성 됩니다 및 클라우드 서비스의 모든 Vm이이 인증서를 기본 Windows 원격 관리 (WinRM) 인증서로 사용 합니다. 스크립트는 이러한 Azure VM에 HPC 기능을 배포하기 위해 기본적으로 이러한 인증서를 로컬 컴퓨터\\클라이언트 컴퓨터의 신뢰할 수 있는 루트 인증 기관 저장소에 일시적으로 설치하여 스크립트 실행 중 "신뢰할 수 없는 CA" 보안 오류를 표시하지 않습니다. 스크립트가 완료되면 인증서가 제거됩니다. 이 매개 변수를 지정할 경우 인증서가 클라이언트 컴퓨터에 설치되지 않고 보안 경고가 표시되지 않습니다.
  
  > [!IMPORTANT]
  > 이 매개 변수는 프로덕션 배포에 권장되지 않습니다.
  > 
  > 

### <a name="example"></a>예
다음 예제는 *MyConfigFile.xml*구성 파일을 사용하여 HPC 팩 클러스터를 만들며 클러스터 설치를 위한 관리자 자격 증명을 지정합니다.

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>추가 고려 사항
* 이 스크립트는 HPC 팩 웹 포털 또는 HPC 팩 REST API를 통한 작업 제출을 선택적으로 활성화할 수 있습니다.
* 추가 소프트웨어를 설치하거나 다른 설정을 구성하려는 경우 이 스크립트로 헤드 노드에서 사용자 지정 사전/사후 구성 스크립트를 선택적으로 실행할 수 있습니다.

## <a name="configuration-file"></a>구성 파일
배포 스크립트의 구성 파일은 XML 파일입니다. 스키마 파일 HPCIaaSClusterConfig.xsd는 HPC 팩 IaaS 배포 스크립트 폴더에 있습니다. **IaaSClusterConfig** 는 배포 스크립트 폴더의 Manual.rtf 파일에 자세히 설명된 하위 요소가 포함된 구성 파일의 루트 요소입니다.

