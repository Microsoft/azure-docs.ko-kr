---
title: Azure Migrate 문제 해결 | Microsoft Docs
description: Azure Migrate 서비스의 알려진 문제에 대한 개요와 일반적인 오류 해결 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: dff3c96cf3ac8eea7c1160ee1834cc70390c0333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533218"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate 문제 해결

## <a name="troubleshoot-common-errors"></a>일반적인 오류 문제 해결

[Azure Migrate](migrate-overview.md)는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. Azure Migrate 배포 및 사용과 관련하여 문제가 발생하면 이 문서를 사용하여 해결하세요.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>온-프레미스 환경을 지속적으로 검색하는 OVA를 사용 중인데 온-프레미스 환경에서 삭제된 VM이 포털에 계속 표시됩니다.

지속적인 검색 어플라이언스는 성능 데이터만 지속적으로 수집하고, 온-프레미스 환경의 구성 변경(예: VM 추가, 삭제, 디스크 추가 등)은 검색하지 않습니다. 온-프레미스 환경에서 구성 변경이 있으면 다음을 통해 포털에 변경 내용을 반영할 수 있습니다.

- 항목 추가(VM, 디스크, 코어 등): 이러한 변경을 Azure Portal에 반영하려면 어플라이언스에서 검색을 중지했다가 다시 시작합니다. 이렇게 하면 Azure Migrate 프로젝트에서 변경 내용이 업데이트됩니다.

   ![검색 중지](./media/troubleshooting-general/stop-discovery.png)

- VM 삭제: 어플라이언스 설계 방식으로 인해 검색을 중지했다 시작해도 VM 삭제가 반영되지 않습니다. 이후 검색의 데이터는 기존 검색에 추가되는 것이지 기존 검색을 덮어쓰는 것이 아니기 때문입니다. 이 경우에는 그룹에서 VM을 제거하고 평가를 다시 계산하여 포털에서 VM을 간단히 무시하면 됩니다.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Azure Migrate 프로젝트 및 관련 Log Analytics 작업 영역 삭제

Azure Migrate 프로젝트를 삭제하면 마이그레이션 프로젝트와 모든 그룹 및 평가가 삭제됩니다. 그러나 프로젝트에 연결한 Log Analytics 작업 영역은 자동으로 삭제되지 않습니다. 이는 같은 Log Analytics 작업 영역이 여러 사용 사례에 사용되었을 수 있기 때문입니다. Log Analytics 작업 영역도 삭제하려면 수동으로 작업 영역을 삭제해야 합니다.

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.

   a. 마이그레이션 프로젝트를 아직 삭제하지 않았다면 프로젝트 개요 페이지의 Essentials 섹션에서 작업 영역으로 이동하는 링크를 확인할 수 있습니다.

   ![LA 작업 영역](./media/troubleshooting-general/LA-workspace.png)

   b. 마이그레이션 프로젝트를 이미 삭제한 경우 Azure Portal의 왼쪽 창에서 **리소스 그룹**을 클릭하고 작업 영역을 만든 리소스 그룹으로 이동한 후에 작업 영역을 찾습니다.
2. [이 문서](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)의 지침에 따라 작업 영역을 삭제합니다.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>*요청에 사용자 ID 헤더가 있어야 합니다* 오류 메시지와 함께 마이그레이션 프로젝트 만들기 실패

조직의 Azure AD(Azure Active Directory) 테넌트에 대한 액세스 권한이 없는 사용자에게 이 문제가 발생할 수 있습니다. 사용자가 처음으로 Azure AD 테넌트에 추가되면 해당 사용자는 테넌트에 조인하라는 이메일 초대장을 받게 됩니다. 사용자는 이메일로 이동하여 초대를 수락해야만 테넌트에 성공적으로 추가됩니다. 이메일을 볼 수 없는 경우 테넌트에 액세스할 수 있는 사용자에게 [여기](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)에 설명된 단계에 따라 초대장을 다시 보내 달라고 부탁하면 됩니다.

초대 이메일을 받은 후에는 이메일을 열고 이메일의 링크를 클릭하여 초대를 수락해야 합니다. 여기까지 마친 후 Azure Portal에서 로그아웃했다가 다시 로그인해야 합니다. 브라우저 새로 고침은 작동하지 않습니다. 그 후 마이그레이션 프로젝트 만들기를 시도할 수 있습니다.

### <a name="i-am-unable-to-export-the-assessment-report"></a>평가 보고서를 내보낼 수 없습니다.

포털에서 평가 보고서를 내보낼 수 없는 경우에는 아래 REST API를 사용하여 평가 보고서의 다운로드 URL을 구합니다.

1. 컴퓨터에 *armclient* 설치(아직 설치하지 않은 경우):

   a. 관리자 명령 프롬프트 창에서 다음 명령을 실행합니다. ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

   b. 관리자 Windows PowerShell 창에서 다음 명령을 실행합니다. ```choco install armclient```

2. Azure Migrate REST API를 사용하여 평가 보고서의 다운로드 URL 구하기

   a.    관리자 Windows PowerShell 창에서 다음 명령을 실행합니다. ```armclient login```

        This opens the Azure login pop-up where you need to sign in to Azure.

   b.    동일한 PowerShell 창에서 다음 명령을 실행하여 평가 보고서의 다운로드 URL을 구합니다(URI 매개 변수를 적절한 값, 아래의 샘플 API 요청으로 바꾸기).

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

      샘플 요청 및 출력:

      ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
   esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
   018_12_16_21/downloadUrl?api-version=2018-02-02
   {
   "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
   "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. 응답에서 URL을 복사하고 브라우저에서 열어서 평가 보고서를 다운로드합니다.

4. 보고서가 다운로드되면 Excel을 사용하여 다운로드한 폴더로 이동하여 Excel에서 파일을 열어서 확인합니다.

### <a name="performance-data-for-cpu-memory-and-disks-is-showing-up-as-zeroes"></a>CPU, 메모리 및 디스크에 대 한 성능 데이터가 0으로 표시 됩니다.

Azure Migrate는 온-프레미스 환경을 지속적으로 프로파일링하면서 온-프레미스 VM의 성능 데이터를 수집합니다. 환경 검색을 방금 시작한 경우 성능 데이터 수집이 완료될 때까지 적어도 하루 이상 기다려야 합니다. 하루를 기다리지 않았는데 평가가 작성된 경우 성능 메트릭이 0으로 표시됩니다. 하루를 기다린 후 새 평가를 만들거나 평가 보고서의 '다시 계산' 옵션을 사용하여 기존 평가를 업데이트할 수 있습니다.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>마이그레이션 프로젝트를 만들 때 Azure 지역을 지정했습니다. 검색된 메타데이터가 저장되는 정확한 Azure 지역은 어떻게 확인할 수 있나요?

프로젝트 **개요** 페이지의 **기본 정보** 섹션으로 이동하면 메타데이터가 저장되는 조정확한 위치를 확인할 수 있습니다. 위치는 Azure Migrate를 통해 지역 내에서 임의로 선택되며 수정할 수는 없습니다. 특정 지역에만 프로젝트를 만들려는 경우 REST API를 사용하여 마이그레이션 프로젝트를 만든 다음 원하는 지역을 전달하면 됩니다.

   ![프로젝트 위치](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Collector 문제

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Azure Migrate Collector 배포가 다음 오류로 인해 실패했습니다. 제공된 매니페스트 파일이 잘못되었습니다. 잘못된 OVF 매니페스트 항목입니다.

1. 해당 해시 값을 확인하여 Azure Migrate Collector OVA 파일이 올바르게 다운로드되는지 확인합니다. 해시 값을 확인하려면 [문서](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)를 참조하세요. 해시 값이 일치하지 않는 경우 OVA 파일을 다시 다운로드하고 배포를 다시 시도합니다.
2. 여전히 실패하고 VMware vSphere 클라이언트를 OVF 배포에 사용하는 경우 vSphere Web Client를 통해 배포를 시도합니다. 그래도 실패하는 경우 다른 웹 브라우저를 사용해 보세요.
3. vSphere 웹 클라이언트를 사용 중이며 vCenter Server 6.5 또는 6.7에서 해당 클라이언트를 배포하려는 경우 다음 단계에 따라 ESXi 호스트에서 직접 OVA 배포를 시도합니다.
   - 웹 클라이언트(https://<*host IP Address*>/ui)를 사용하여 ESXi 호스트에 직접 연결합니다(vCenter Server 대신).
   - 홈 > 인벤토리로 이동합니다.
   - 파일 > OVF 템플릿 배포 > OVA로 이동을 클릭하고 배포를 완료합니다.
4. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

### <a name="unable-to-select-the-azure-cloud-in-the-appliance-fails-with-error-azure-cloud-selection-failed"></a>"Azure 클라우드 선택 실패"을 Azure를 선택할 수 없습니다 클라우드 어플라이언스를 오류로 실패

이것은 알려진된 문제 이며 수정 문제에 대해 사용할 수 있습니다. 다운로드 하십시오 합니다 [최신 비트를 업그레이드](https://docs.microsoft.com/azure/migrate/concepts-collector-upgrade#continuous-discovery-upgrade-versions) 어플라이언스 및 수정 사항을 적용 하도록 어플라이언스에 업데이트 합니다.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>수집기가 인터넷에 연결할 수 없음

이 문제는 사용 중인 컴퓨터가 프록시 뒤에 있는 경우 발생할 수 있습니다. 프록시에 필요한 경우 권한 부여 자격 증명을 제공해야 합니다.
URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음과 같은 필수 URL을 허용 목록에 포함해야 합니다.

**URL** | **목적**  
--- | ---
*.portal.azure.com | Azure 서비스와의 연결을 확인하고 시간 동기화 문제의 유효성을 검사하는 데 필요합니다.
*.oneget.org | powershell 기반 vCenter PowerCLI 모듈을 다운로드하는 데 필요합니다.

**인증서 유효성 검사에 실패하여 수집기가 인터넷에 연결할 수 없습니다.**

가로채는 프록시를 사용하여 인터넷에 연결하는 경우 그리고 수집기 VM에 프록시 인증서를 가져오지 않은 경우 이 문제가 발생할 수 있습니다. [여기](https://docs.microsoft.com/azure/migrate/concepts-collector) 설명된 단계에 따라 프록시 인증서를 가져올 수 있습니다.

**포털에서 복사한 프로젝트 ID 및 키를 사용하여 수집기로 프로젝트에 연결할 수 없습니다.**

올바른 정보를 복사하여 붙여넣었는지 확인합니다. 문제를 해결하려면 다음과 같이 MMA(Microsoft Monitoring Agent)를 설치하고 MMA에서 프로젝트에 연결할 수 있는지 확인합니다.

1. 수집기 VM에서 [MMA](https://go.microsoft.com/fwlink/?LinkId=828603)를 다운로드합니다.
2. 설치를 시작하려면 다운로드한 파일을 두 번 클릭합니다.
3. 설치 화면의 **시작** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의**를 클릭하여 라이선스를 수락합니다.
4. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
5. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
6. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 복사한 프로젝트 ID와 키를 붙여넣습니다. 그런 후 **Next** 를 클릭합니다.
7. 에이전트가 프로젝트에 연결할 수 있는지 확인합니다. 연결할 수 없으면 설정을 확인합니다. 에이전트는 연결할 수는 있지만 수집기는 연결할 수 없는 경우 지원에 문의합니다.


### <a name="error-802-date-and-time-synchronization-error"></a>오류 802: 날짜 및 시간 동기화 오류

서버 시계가 현재 시간과 동기화되지 않아 5분 이상 차이가 벌어질 수 있습니다. 다음과 같이 VM 수집기에서 시계 시간을 현재 시간과 일치하도록 변경합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인하려면 w32tm /tz 명령을 실행합니다.
3. 시간을 동기화하려면 w32tm /resync 명령을 실행합니다.

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI 설치 실패

Azure Migrate 수집기는 PowerCLI를 다운로드하여 어플라이언스에 설치합니다. PowerCLI 리포지토리의 엔드포인트에 연결할 수 없는 것이 PowerCLI 설치 실패의 원인일 수 있습니다. 문제를 해결하려면 다음 단계에 따라 수집기 VM에서 PowerCLI를 수동으로 설치해 보세요.

1. Windows PowerShell을 관리자 모드로 엽니다.
2. C:\ProgramFiles\ProfilerService\VMWare\Scripts\ 디렉터리로 이동합니다.
3. InstallPowerCLI.ps1 스크립트를 실행합니다.

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>오류 UnhandledException 내부 오류가 발생했습니다. System.IO.FileNotFoundException

이 문제는 VMware PowerCLI 설치 문제로 인해 발생할 수 있습니다. 문제를 해결하려면 아래 단계를 수행합니다.

1. 수집기 어플라이언스의 최신 버전을 사용하고 있지 않다면 [수집기를 최신 버전으로 업그레이드](https://aka.ms/migrate/col/checkforupdates)한 후에 문제가 해결되었는지 확인합니다.
2. 최신 수집기 버전이 이미 있는 경우 아래 단계를 따라 PowerCLI의 새로 설치를 수행합니다.

   a. 어플라이언스에서 웹 브라우저를 닫습니다.

   b. Windows 서비스 관리자('실행'을 열고 services.msc를 입력하여 Windows 서비스 관리자 열기)로 이동하여 'Azure Migrate Collector' 서비스를 중지합니다. Azure Migrate Collector 서비스를 마우스 오른쪽 단추로 클릭하고 중지를 클릭합니다.

   다. 다음 위치에서 'VMware'로 시작하는 모든 폴더를 삭제합니다. C:\Program Files\WindowsPowerShell\Modules  
        C:\Program Files (x86)\WindowsPowerShell\Modules

   d. Windows 서비스 관리자('실행'을 열고 services.msc를 입력하여 Windows 서비스 관리자 열기)에서 'Azure Migrate Collector' 서비스를 다시 시작합니다. Azure Migrate Collector 서비스를 마우스 오른쪽 단추로 클릭하고 시작을 클릭합니다.

   e. 바탕 화면 바로 가기 '수집기 실행'을 두 번 클릭하여 수집기 애플리케이션을 시작합니다. 자동으로 수집기 응용 프로그램을 다운로드 하 고 필요한 버전의 PowerCLI 설치 해야 합니다.

3. 위의 문제를 해결 되지 않으면 다음과 같이 위의 c 후 다음 단계를 사용 하 여 어플라이언스에서 PowerCLI를 수동으로 설치 합니다.

   a. 설치 파일에서 다음 단계를 완료 되지 않은 PowerCLI 정리 #a 위의 2 단계에서 #c 하 합니다.

   b. 시작으로 이동 > 실행 > 관리자 모드에서 Windows PowerShell(x86) 열기

   다. 명령 실행:  Install-module "VMWare.VimAutomation.Core"-RequiredVersion "6.5.2.6234650" (유형 확인을 위해 요청 하는 경우 ' A')

   d. Windows 서비스 관리자('실행'을 열고 services.msc를 입력하여 Windows 서비스 관리자 열기)에서 'Azure Migrate Collector' 서비스를 다시 시작합니다. Azure Migrate Collector 서비스를 마우스 오른쪽 단추로 클릭하고 시작을 클릭합니다.

   e. 바탕 화면 바로 가기 '수집기 실행'을 두 번 클릭하여 수집기 애플리케이션을 시작합니다. 자동으로 수집기 응용 프로그램을 다운로드 하 고 필요한 버전의 PowerCLI 설치 해야 합니다.

4. 방화벽 문제로 인해 기기 모듈을 다운로드할 수 없는 경우 다운로드 하 고 다음 단계를 사용 하 여 인터넷 액세스 권한이 있는 컴퓨터에서 모듈을 설치 합니다.

    a. 설치 파일에서 다음 단계를 완료 되지 않은 PowerCLI 정리 #a 위의 2 단계에서 #c 하 합니다.

    b. 시작으로 이동 > 실행 > 관리자 모드에서 Windows PowerShell(x86) 열기

    다. 명령 실행:  Install-module "VMWare.VimAutomation.Core"-RequiredVersion "6.5.2.6234650" (유형 확인을 위해 요청 하는 경우 ' A')

    d. 수집기 VM에서 동일한 위치에 "C:\Program Files (x86) \WindowsPowerShell\Modules"에서 "VMware"로 시작 하는 모든 모듈을 복사 합니다.

    e. Windows 서비스 관리자('실행'을 열고 services.msc를 입력하여 Windows 서비스 관리자 열기)에서 'Azure Migrate Collector' 서비스를 다시 시작합니다. Azure Migrate Collector 서비스를 마우스 오른쪽 단추로 클릭하고 시작을 클릭합니다.

    f. 바탕 화면 바로 가기 '수집기 실행'을 두 번 클릭하여 수집기 애플리케이션을 시작합니다. 자동으로 수집기 응용 프로그램을 다운로드 하 고 필요한 버전의 PowerCLI 설치 해야 합니다.

### <a name="error-unabletoconnecttoserver"></a>오류 UnableToConnectToServer

다음 오류로 인해 vCenter Server "Servername.com:9443"에 연결할 수 없습니다. https://Servername.com:9443/sdk를 수신 대기 중인 메시지를 수락할 수 있는 엔드포인트가 없습니다.

수집기 어플라이언스가 최신 버전인지 확인하고, 최신 버전이 아니면 어플라이언스를 [최신 버전](https://docs.microsoft.com/azure/migrate/concepts-collector)으로 업그레이드합니다.

최신 버전에서도 여전히 문제가 발생하면 수집기 컴퓨터가 지정된 vCenter Server 이름을 확인할 수 없거나 지정된 포트가 잘못되었을 수 있습니다. 기본적으로 포트가 지정되지 않은 경우 수집기는 포트 번호 443에 연결하려고 합니다.

1. 수집기 컴퓨터에서 Servername.com를 ping해 봅니다.
2. 1단계가 실패하는 경우 IP 주소를 통해 vCenter Server에 연결해보세요.
3. vCenter에 연결할 정확한 포트 번호를 식별합니다.
4. 마지막으로 vCenter Server가 실행 중인지 확인합니다.

### <a name="antivirus-exclusions"></a>바이러스 백신 제외

Azure Migrate 어플라이언스를 강화하려는 경우 어플라이언스의 다음 폴더를 바이러스 백신 검사에서 제외해야 합니다.

- Azure Migrate 서비스용 이진 파일이 있는 폴더. 모든 하위 폴더를 제외합니다.
  %ProgramFiles%\ProfilerService  
- Azure Migrate 웹 애플리케이션. 모든 하위 폴더를 제외합니다.
  %SystemDrive%\inetpub\wwwroot
- 데이터베이스 및 로그 파일의 로컬 캐시. Azure Migrate Service에는 이 폴더에 대한 RW 권한이 필요합니다.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>종속성 시각화 문제

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government 프로젝트에 대한 종속성 시각화 기능을 찾을 수 없습니다.

Azure Migrate는 종속성 시각화 기능에 대한 서비스 맵에 의존하며, 현재 Azure Government에서는 서비스 맵을 사용할 수 없으므로 Azure Government에서는 이 기능을 사용할 수 없습니다.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>온-프레미스 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 설치했는데 종속성이 Azure Migrate 포털에 표시됩니다.

일반적으로는 에이전트를 설치한 후 15~30분이 지나면 Azure Migrate 포털에 종속성이 표시됩니다. 30분 이상 기다렸다면 아래 단계를 수행하여 MMA 에이전트가 OMS 작업 영역과 통신할 수 있는지 확인합니다.

Windows VM:
1. **제어판**으로 이동하여 **Microsoft Monitoring Agent**를 시작합니다.
2. MMA 속성 팝업에서 **Azure Log Analytics(OMS)** 탭으로 이동합니다.
3. 작업 영역의 **상태**가 녹색인지 확인합니다.
4. 상태가 녹색이 아니면 작업 영역을 제거했다가 MMA에 다시 추가해 봅니다.
        ![MMA 상태](./media/troubleshooting-general/mma-status.png)

Linux VM의 경우 MMA 및 종속 에이전트 설치 명령이 정상적으로 실행되었는지 확인합니다.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA에서는 어떤 운영 체제가 지원되나요?

MMA에서 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)에 나와 있습니다.
MMA에서 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)에 나와 있습니다.

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>종속성 에이전트는 어떤 운영 체제를 지원하나요?

종속성 에이전트가 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)에 나와 있습니다.
종속성 에이전트가 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)에 나와 있습니다.

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>1시간이 넘는 기간에 대해서는 Azure Migrate의 종속성을 시각화할 수 없나요?
Azure Migrate에서는 최대 1시간 동안의 종속성을 시각화할 수 있습니다. 하지만 Azure Migrate에서는 최대 1개월 전의 특정 날짜로 돌아가 종속성을 시각화할 수 있습니다. 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 종속성을 확인할 수 있는 시간은 1시간입니다. 그러나 Azure Monitor의 로그를 사용할 수 있습니다 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 더 긴 기간 동안.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>VM이 10개보다 많은 그룹의 종속성은 시각화할 수 없나요?
[그룹의 종속성 시각화](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 시에 허용되는 최대 VM 수는 10개입니다. VM이 10개보다 많은 그룹이 있다면 더 작은 그룹 여러 개로 분할한 다음 종속성을 시각화하는 것이 좋습니다.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>에이전트를 설치하고 종속성 시각화 그룹을 사용하여 그룹을 만들었습니다. 장애 조치(failover) 이후, 컴퓨터가 "종속성 보기" 대신 "에이전트 설치" 작업을 표시합니다.
* 계획된 또는 계획되지 않은 장애 조치(failover) 후에는 온-프레미스 컴퓨터가 꺼지고 동급 컴퓨터가 Azure에 생성됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다. 사용자가 온-프레미스 IP 주소를 유지하는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다. MAC 및 IP 주소가 모두 다르면 Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않으며, 종속성을 보는 대신 사용자에게 에이전트를 설치하라고 요청합니다.
* 테스트 장애 조치(failover) 후에도 온-프레미스 컴퓨터는 예상대로 계속 켜져 있습니다. Azure에서 생성된 동급 컴퓨터는 다른 MAC 주소를 획득하며 다른 IP 주소를 획득할 수도 있습니다. 사용자 차단 보내는 Azure Monitor 트래픽을 이러한 컴퓨터에서 로그를 하지 않는 한 Azure Migrate 서비스 맵 종속성 데이터를 사용 하 여 온-프레미스 컴퓨터를 연결 하지는 않습니다 및 종속성을 보는 대신 에이전트를 설치 하는 사용자에 게 요청 합니다.

## <a name="troubleshoot-azure-readiness-issues"></a>Azure 준비 상태 문제 해결

**문제** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 공유 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행하기 전에 부팅 유형을 BIOS로 변환하는 것이 좋습니다. <br/><br/>[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure)를 사용하여 마이그레이션하는 동안 VM의 부팅 유형이 BIOS로 변환될 때 이러한 VM의 마이그레이션을 수행할 수 있습니다.
조건부로 지원되는 Windows OS | OS의 지원 종료 날짜가 경과되어, [Azure에서 지원되기](https://aka.ms/WSosstatement) 위해 CSA(사용자 지정 지원 계약)가 필요한 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
지원되지 않는 Windows OS | Azure는 [선택된 Windows OS 버전](https://aka.ms/WSosstatement)만 지원하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure는 [선택된 Linux OS 버전](../virtual-machines/linux/endorsed-distros.md)만 보증하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
보증되지 않는 Linux OS | 컴퓨터를 Azure에서 부팅할 수 있지만 Azure에서 OS 지원을 제공하지 않습니다. Azure로 마이그레이션하기 전에 OS를 [보증된 Linux 버전](../virtual-machines/linux/endorsed-distros.md)으로 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | Azure Migrate가 VM의 Azure 준비 상태를 식별할 수 없으므로 VM의 운영 체제가 vCenter 서버에서 '기타'로 지정되었습니다. 컴퓨터를 마이그레이션하기 전에 컴퓨터 내에서 실행되는 OS가 Azure에서 [지원](https://aka.ms/azureoslist)되는지 확인합니다.
지원되지 않는 OS 비트 | 32비트 OS가 있는 VM은 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 VM의 OS를 32비트에서 64비트로 업그레이드하는 것이 좋습니다.
Visual Studio 구독이 필요합니다. | 컴퓨터에 Visual Studio 구독에서만 지원되는 Windows 클라이언트 OS가 실행되고 있습니다.
필요한 저장소 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 저장소 성능(IOPS/처리량)이 Azure VM 지원을 초과합니다. 마이그레이션을 시작하기 전에 컴퓨터의 저장소 요구 사항을 낮춰봅니다.
필요한 네트워크 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정된 위치에서 VM을 찾을 수 없습니다. | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
부적합한 디스크가 하나 이상 있습니다. | VM에 연결된 하나 이상의 디스크가 Azure 요구 사항을 충족하지 않습니다. VM에 연결된 각 디스크에 대해 디스크 크기가 4TB 미만인지 확인합니다. 4TB 미만이 아니면 Azure로 마이그레이션하기 전에 디스크 크기를 축소합니다. 각 디스크에 필요한 성능(IOPS/처리량)이 Azure [관리 가상 머신 디스크](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)에서 지원되는지 확인합니다.   
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure는 4TB 이하의 디스크를 지원합니다. 마이그레이션을 시작하기 전에 디스크를 4TB 미만으로 축소합니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 저장소 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure가 적합한 종류의 VM을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.


## <a name="collect-logs"></a>로그 수집

**수집기 VM에서 로그를 수집하는 방법**

기본적으로 로깅을 사용하도록 설정됩니다. 로그는 다음과 같은 위치에 있습니다.

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Windows용 이벤트 추적을 수집하려면 다음 단계를 수행합니다.

1. 수집기 VM에서 PowerShell 명령 창을 엽니다.
2. **Get-EventLog -LogName Application | export-csv eventlog.csv** 명령을 실행합니다.

**포털 네트워크 트래픽 로그는 어떻게 수집하나요?**

1. 브라우저를 열고 [포털](https://portal.azure.com)을 탐색하여 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작합니다. 필요한 경우 **탐색에 대한 항목 지우기** 설정을 선택 취소합니다.
3. **네트워크** 탭을 클릭하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 검은색 원을 클릭하여 시작합니다.
   - Microsoft Edge/IE에서는 자동으로 기록이 시작됩니다. 자동으로 시작되지 않으면 녹색 재생 단추를 클릭합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - 크롬에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content**를 클릭합니다. 그러면 로그가 .har 파일로 압축되어 내보내집니다.
   - Microsoft Edge/IE에서는 **캡처된 트래픽 내보내기** 아이콘을 클릭합니다. 그러면 로그가 압축되어 내보내집니다.
6. **콘솔** 탭으로 이동하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장**을 선택하여 로그를 내보내고 압축합니다.
   - Microsoft Edge/IE의 경우 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사**를 선택합니다.
7. 개발자 도구를 닫습니다.

## <a name="collector-error-codes-and-recommended-actions"></a>수집기 오류 코드 및 권장 작업

| 오류 코드 | 오류 이름   | Message   | 가능한 원인 | 권장 작업  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | 수집기가 만료되었습니다.                                                        | 수집기가 만료되었습니다.                                                                                    | 새 버전의 수집기를 다운로드하고 다시 시도하세요.                                                                                      |
| 751       | UnableToConnectToServer        | 오류로 인해 vCenter Server ‘%Name;’에 연결할 수 없습니다. %ErrorMessage;     | 자세한 내용은 오류 메시지를 확인하세요.                                                             | 문제를 해결하고 다시 시도하세요.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | 서버 ‘%Name;’이(가) vCenter Server가 아닙니다.                                  | vCenter Server 세부 정보를 제공하세요.                                                                       | 올바른 vCenter Server 세부 정보를 사용하여 작업을 다시 시도하세요.                                                                                   |
| 753       | InvalidLoginCredentials        | 오류로 인해 vCenter Server ‘%Name;’에 연결할 수 없습니다. %ErrorMessage; | 잘못된 로그인 자격 증명으로 인해 vCenter Server에 연결하지 못했습니다.                             | 제공된 로그인 자격 증명 정보가 올바른지 확인하세요.                                                                                    |
| 754       | NoPerfDataAvailable           | 성능 데이터를 사용할 수 없습니다.                                               | vCenter Server에서 통계 수준을 확인하세요. 성능 데이터를 사용하려면 3으로 설정해야 합니다. | 통계 수준을 3(5분, 30분, 2시간 동안)으로 변경하고 하루 이상 기다린 후 시도하세요.                   |
| 756       | NullInstanceUUID               | null InstanceUUID가 있는 컴퓨터를 발견했습니다.                                  | vCenter Server에 부적절한 개체가 있을 수 있습니다.                                                      | 문제를 해결하고 다시 시도하세요.                                                                                                           |
| 757       | VMNotFound                     | 가상 머신이 없습니다.                                                  | 가상 머신이 삭제되었을 수 있습니다. %VMID;                                                                | vCenter 인벤토리의 범위를 지정하는 동안 선택한 가상 머신이 검색 중에 있는지 확인하세요.                                      |
| 758       | GetPerfDataTimeout             | VCenter 요청 시간이 초과되었습니다. 메시지 %Message;                                  | vCenter Server 자격 증명이 잘못되었습니다.                                                              | vCenter Server 자격 증명을 검사하고 vCenter Server에 연결할 수 있는지 확인하세요. 작업을 다시 시도하세요. 문제가 지속되면 고객 지원 팀에 문의하세요. |
| 759       | VmwareDllNotFound              | VMWare.Vim DLL이 없습니다.                                                     | PowerCLI가 올바르게 설치되지 않았습니다.                                                                   | PowerCLI가 올바르게 설치되었는지 확인하세요. 작업을 다시 시도하세요. 문제가 지속되면 고객 지원 팀에 문의하세요.                               |
| 800       | ServiceError                   | Azure Migrate Collector 서비스가 실행되고 있지 않습니다.                               | Azure Migrate Collector 서비스가 실행되고 있지 않습니다.                                                       | services.msc를 사용하여 서비스를 시작하고 작업을 재시도하세요.                                                                             |
| 801       | PowerCLIError                  | VMware PowerCLI 설치에 실패했습니다.                                          | VMware PowerCLI 설치에 실패했습니다.                                                                  | 작업을 다시 시도하세요. 문제가 지속되면 수동으로 설치하고 작업을 다시 시도하세요.                                                   |
| 802       | TimeSyncError                  | 시간이 인터넷 시간 서버와 동기화되어 있지 않습니다.                            | 시간이 인터넷 시간 서버와 동기화되어 있지 않습니다.                                                    | 컴퓨터 시간이 컴퓨터의 표준 시간대에 맞게 정확하게 설정되었는지 확인하고 작업을 다시 시도하세요.                                 |
| 702       | OMSInvalidProjectKey           | 잘못된 프로젝트 키가 지정되었습니다.                                                | 잘못된 프로젝트 키가 지정되었습니다.                                                                        | 올바른 프로젝트 키로 작업을 다시 시도하세요.                                                                                              |
| 703       | OMSHttpRequestException        | 요청을 보내는 동안 오류가 발생했습니다. 메시지 %Message;                                | 프로젝트 ID 및 키를 검사하고 엔드포인트에 연결할 수 있는지 확인하세요.                                       | 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP 요청 시간이 초과되었습니다. 메시지 %Message;                                     | 프로젝트 ID 및 키를 검사하고 엔드포인트에 연결할 수 있는지 확인하세요.                                       | 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.                                                                     |
