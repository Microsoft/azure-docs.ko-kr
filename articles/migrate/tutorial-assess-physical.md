---
title: Azure Migrate 서버 평가를 사용하여 Azure로 마이그레이션할 물리적 서버 평가
description: Azure Migrate 서버 평가를 사용하여 Azure로 마이그레이션할 온-프레미스 물리적 서버를 평가하는 방법에 대해 설명합니다.
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: c89c731712a625e5f3b7a1a7e9306f6a7480b96b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990303"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Azure Migrate를 사용하여 물리적 서버 평가: Server Assessment

이 문서에서는 Azure Migrate:  서버 평가 도구를 사용하여 온-프레미스 VMware VM을 평가하는 방법을 보여 줍니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다.

이 자습서는 물리적 서버를 평가하고 Azure로 마이그레이션하는 방법을 보여 주는 시리즈의 두 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Migrate 프로젝트를 설정합니다.
> * 온-프레미스에서 실행되는 Azure Migrate 어플라이언스에서 물리적 서버를 평가하도록 설정합니다.
> * 온-프레미스 물리적 서버의 연속 검색을 시작합니다. 어플라이언스는 검색된 서버의 구성 및 성능 데이터를 Azure로 보냅니다.
> * 검색된 서버를 그룹화하고 서버 그룹을 평가합니다.
> * 평가를 검토합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 방법 문서를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>사전 요구 사항

- 이 시리즈의 첫 번째 자습서를 [완료](tutorial-prepare-physical.md)합니다. 그렇지 않으면 이 자습서의 지침이 작동하지 않습니다.
- 첫 번째 자습서에서 수행해야 하는 작업은 다음과 같습니다.
    - Azure Migrate에 대한 [Azure 권한을 설정](tutorial-prepare-physical.md#prepare-azure)합니다.
    - 평가할 [물리적 서버를 준비](tutorial-prepare-physical.md#prepare-for-physical-server-assessment)합니다. 어플라이언스 요구 사항을 확인해야 합니다. 또한 물리적 서버 검색에 사용할 계정을 설정해야 합니다. 필요한 포트를 사용할 수 있어야 하고, Azure에 액세스하는 데 필요한 URL을 알고 있어야 합니다.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate 프로젝트 설정

새 Azure Migrate 프로젝트를 설정하는 방법은 다음과 같습니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**의 **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 클릭합니다.

    ![서버 검색 및 평가](./media/tutorial-assess-physical/assess-migrate.png)

4. **시작**에서**도구 추가**를 클릭합니다.
5. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.     
6. **프로젝트 세부 정보**에서 프로젝트 이름과 프로젝트를 만들려는 지역을 지정합니다. 아시아, 유럽, 영국 및 미국 지역이 지원됩니다.

    - 프로젝트 지역은 온-프레미스 서버에서 수집된 메타데이터를 저장하는 데만 사용됩니다.
    - 대상 지역은 마이그레이션을 실행할 때 선택할 수 있습니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-assess-physical/migrate-project.png)


7. **다음**을 클릭합니다.
8. **평가 도구 선택**에서 **Azure Migrate: 서버 평가** > **다음**을 차례로 선택합니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-assess-physical/assessment-tool.png)

9. **마이그레이션 도구 선택**에서 **마이그레이션 도구 추가 건너뛰기** > **다음**을 차례로 선택합니다.
10. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 클릭합니다.
11. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. 프로젝트 페이지로 이동합니다. 프로젝트가 표시되지 않으면 Azure Migrate 대시보드의 **서버**에서 액세스할 수 있습니다.


## <a name="set-up-the-appliance"></a>어플라이언스 설정

Azure Migrate: 서버 평가는 경량 어플라이언스를 실행합니다.

- 이 어플라이언스는 물리적 서버 검색을 수행하고, 서버 메타데이터 및 성능 데이터를 Azure Migrate 서버 평가에 보냅니다.
- 어플라이언스를 설정하려면 다음을 수행합니다.
    - Azure Portal에서 Azure Migrate 설치 프로그램 스크립트가 포함된 압축 파일을 다운로드합니다.
    - 압축 파일의 콘텐츠를 추출합니다. 관리자 권한으로 PowerShell 콘솔을 시작합니다.
    - PowerShell 스크립트를 실행하여 어플라이언스 웹 애플리케이션을 시작합니다.
    - 어플라이언스를 처음으로 구성하고, Azure Migrate 프로젝트에 등록합니다.
- 단일 Azure Migrate 프로젝트에 대해 여러 어플라이언스를 설정할 수 있습니다. 모든 어플라이언스에서 원하는 수의 물리적 서버를 검색할 수 있습니다. 어플라이언스당 최대 250개의 서버를 검색할 수 있습니다.

### <a name="download-the-installer-script"></a>설치 프로그램 스크립트 다운로드

어플라이언스에 대한 압축 파일을 다운로드합니다.

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **가상화되지 않음/기타**를 클릭합니다.
3. **다운로드**를 클릭하여 압축 파일을 다운로드합니다.

    ![설치 프로그램 다운로드](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3.  최신 어플라이언스 버전의 경우 생성된 해시는 다음 설정과 일치해야 합니다.

  **알고리즘** | **해시 값**
  --- | ---
  MD5 | 1e92ede3e87c03bd148e56a708cdd33f
  SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate 설치 프로그램 스크립트 실행

설치 프로그램 스크립트는 다음을 수행합니다.

- 물리적 서버 검색 및 평가를 위한 에이전트와 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 지정된 경로에 다음 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 다음과 같이 실행합니다.

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1**이라는 스크립트를 실행합니다.
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
스크립트가 성공적으로 완료되면 어플라이언스 웹 애플리케이션이 시작됩니다.

문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 스크립트 로그에 액세스할 수 있습니다.

> [!NOTE]
> 기존 Azure Migrate 어플라이언스에서 Azure Migrate 설치 프로그램 스크립트를 실행하지 마세요.

### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스에서 [Azure URL](migrate-appliance.md#url-access)에 연결할 수 있는지 확인합니다.


### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

1. 어플라이언스에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*): 44368**)을 엽니다.

   또는 바탕 화면에서 앱 바로 가기를 클릭하여 앱을 열 수 있습니다.
2. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **라이선스**: 사용 조건에 동의하고 타사 정보를 읽습니다.
    - **연결**: 앱에서 서버가 인터넷에 액세스할 수 있는지 확인합니다. 서버에서 프록시를 사용하는 경우:
        - **프록시 설정**을 클릭하고, 프록시 주소와 수신 포트를 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 지정합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
    - **시간 동기화**: 시간이 확인됩니다. 서버 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate 서버 평가에서 어플라이언스에 최신 업데이트가 설치되어 있는지 확인합니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. **로그인**을 클릭합니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
2. 새로 만들기 탭에서 Azure 자격 증명을 사용하여 로그인합니다.
    - 사용자 이름과 암호를 사용하여 로그인합니다.
    - PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다.
4. Azure Migrate 프로젝트가 만들어진 구독을 선택합니다. 그런 다음, 해당 프로젝트를 선택합니다.
5. 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
6. **등록**을 클릭합니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

이제 어플라이언스에서 검색할 물리적 서버에 연결하여 검색을 시작합니다.

1. **자격 증명 추가**를 클릭하여 어플라이언스가 서버를 검색하는 데 사용할 계정 자격 증명을 지정합니다.  
2. **운영 체제**, 자격 증명의 식별 이름, **사용자 이름** 및 **암호**를 지정하고 **추가**를 클릭합니다.
Windows 및 Linux 서버에 대해 각각 자격 증명 집합 하나를 추가할 수 있습니다.
4. **서버 추가**를 클릭하고 서버에 연결하기 위한 서버 세부 정보(FQDN/IP 주소 및 자격 증명의 식별 이름)를 지정합니다(한 행에 한 항목).
3. **유효성 검사**를 클릭합니다. 유효성 검사 후 검색 가능한 서버 목록이 표시됩니다.
    - 서버에 대한 유효성 검사가 실패하면 마우스로 **상태** 열의 아이콘 위를 가리켜서 오류를 검토합니다. 문제를 해결하고, 유효성을 다시 검사합니다.
    - 서버를 제거하려면 > **삭제**를 선택합니다.
4. 유효성 검사가 완료되면 **저장 및 검색 시작**을 클릭하여 검색 프로세스를 시작합니다.

그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 서버 당 약 1.5분이 걸립니다.

### <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색되면 Azure Portal에서 해당 서버이 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버**의 수를 표시하는 아이콘을 클릭합니다.

## <a name="set-up-an-assessment"></a>평가 설정

Azure Migrate: 서버 평가를 사용하여 만들 수 있는 평가에는 두 가지 유형이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | **추천 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **추천 디스크 유형(표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **추천 VM 크기**: 온-프레미스 서버 크기를 기반으로 합니다.<br/><br> **추천 디스크 유형**: 평가를 위해 선택한 스토리지 유형 설정을 기반으로 합니다.


### <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **평가**를 클릭합니다.

    ![평가](./media/tutorial-assess-physical/assess.png)

2. **서버 평가**에서 평가 이름을 지정합니다.
3. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![평가 속성](./media/tutorial-assess-physical/view-all.png)

3. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 서버를 수집합니다.
4. **그룹에 머신 추가**에서 그룹에 추가할 서버를 선택합니다.
5. **평가 만들기**를 클릭하여 그룹을 만들고, 평가를 실행합니다.

    ![평가 만들기](./media/tutorial-assess-physical/assessment-create.png)

6. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가** > **평가**에서 해당 평가를 확인합니다.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.



## <a name="review-an-assessment"></a>평가 검토

평가에서 설명하는 항목은 다음과 같습니다.

- **Azure 준비 상태**: 서버가 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 서버를 실행하는 데 들어가는 월간 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.

### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** >  **서버**의 **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.
2. **평가**에서 해당 평가를 클릭하여 엽니다.

    ![평가 요약](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 서버가 Azure로 마이그레이션할 준비가 되었는지 확인합니다.
2. 상태를 검토합니다.
    - **Azure 준비 완료**: Azure Migrate는 평가에서 VM의 크기 및 예상 비용을 추천합니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없는 경우에 사용됩니다.

2. **Azure 준비 상태** 상태를 클릭합니다. 서버 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 서버 세부 정보를 확인할 수 있습니다.



### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기에서는 Azure에서 VM을 실행하는 데 들어가는 예상 컴퓨팅 및 스토리지 비용을 표시합니다.

1. 월간 컴퓨팅 및 스토리지 비용을 검토합니다. 비용은 평가된 그룹의 모든 서버에 대해 집계됩니다.

    - 예상 비용은 머신의 크기 추천 사항과 해당 디스크 및 속성을 기반으로 합니다.
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다.
    - 예상 비용은 온-프레미스 서버를 IaaS VM으로 실행하는 데 들어가는 비용입니다. Azure Migrate 서버 평가에서는 PaaS 또는 SaaS 비용을 고려하지 않습니다.

2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.
3. 드릴다운하여 특정 서버에 대한 세부 정보를 확인할 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행하면 신뢰 등급이 평가에 할당됩니다.

![신뢰 등급](./media/tutorial-assess-physical/confidence-rating.png)

- 별 1개(최저)에서 별 5개(최고)까지의 등급입니다.
- 신뢰 등급을 통해 평가에서 제공하는 크기 추천 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.

평가에 대한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

신뢰 등급의 모범 사례에 대해 [자세히 알아보세요](best-practices-assessment.md#best-practices-for-confidence-ratings).


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Migrate 어플라이언스 설정
> * 평가 만들기 및 검토

Azure Migrate:  Server Migration에 연결합니다.

> [!div class="nextstepaction"]
> [물리적 서버 마이그레이션](./tutorial-migrate-physical-virtual-machines.md)
