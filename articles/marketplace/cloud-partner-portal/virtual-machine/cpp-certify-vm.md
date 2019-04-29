---
title: Azure Marketplace에 대한 VM 이미지 인증 | Microsoft Docs
description: Azure Marketplace 인증을 위해 VM 이미지를 테스트하고 제출하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638348"
---
# <a name="certify-your-vm-image"></a>VM 이미지 인증

VM(가상 머신)을 만들고 배포한 후에 Azure Marketplace 인증을 위해 VM 이미지를 테스트하고 제출해야 합니다. 이 문서에서는 *Azure Certified용 인증 테스트 도구*를 얻을 수 있는 위치, 이 도구를 사용하여 VM 이미지를 인증하는 방법 및 VHD가 있는 Azure 컨테이너에 확인 결과를 업로드하는 방법에 대해 설명합니다. 


## <a name="download-and-run-the-certification-test-tool"></a>인증 테스트 도구 다운로드 및 실행

Azure 인증 테스트 도구는 로컬 Windows 머신에서 실행되지만 Azure 기반 Windows 또는 Linux VM을 테스트합니다.  사용자 VM 이미지가 Microsoft Azure와 호환되는지, 즉 VHD 준비와 관련된 지침과 요구 사항이 충족되었는지를 확인합니다. 이 도구의 출력은 호환성 보고서이며, VM 인증을 요청하기 위해 [Cloud 파트너 포털](https://cloudpartner.azure.com)에 업로드됩니다.

1. 가장 최근의 [Azure Certified용 인증 테스트 도구](https://www.microsoft.com/download/details.aspx?id=44299)를 다운로드하여 설치합니다. 
2. 인증 도구를 연 다음, **새 테스트 시작**을 클릭합니다.
3. **테스트 정보** 화면에서 테스트 실행에 대한 **테스트 이름**이름을 입력합니다.
4. VM에 대한 **플랫폼**(`Windows Server` 또는 `Linux`)을 선택합니다. 플랫폼 선택은 나머지 옵션에 영향을 줍니다.
5. VM에서 이 데이터베이스 서비스를 사용하는 경우 **Azure SQL Database 테스트** 확인란을 선택합니다.

   ![인증 테스트 도구 초기 페이지](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>VM 이미지에 인증 도구 연결

  도구에서 [PowerShell](https://docs.microsoft.com/powershell/)을 사용하여 Windows 기반 VM에 연결하고, [SSH.Net](https://www.ssh.com/ssh/protocol/)을 통해 Linux VM에 연결합니다.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Linux VM 이미지에 인증 도구 연결

1. **SSH 인증** 모드(`Password Authentication` 또는 `key File Authentication`)를 선택합니다.
2. 암호 기반 인증을 사용하는 경우 **VM DNS 이름**, **사용자 이름** 및 **암호**에 대한 값을 입력합니다.  필요에 따라 기본 **SSH 포트** 번호를 변경할 수 있습니다.

     ![Linux VM 이미지의 암호 인증](./media/publishvm_026.png)

3. 키 파일 기반 인증을 사용하는 경우 **VM DNS Name**, **사용자 이름** 및 **개인 키** 위치에 대한 값을 입력합니다.  필요에 따라 **전달 구**를 제공하거나 기본 **SSH 포트** 번호를 변경할 수 있습니다.

     ![Linux VM 이미지의 파일 인증](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Windows 기반 VM 이미지에 인증 도구 연결**
1. 정규화된 **VM DNS 이름**(예: `MyVMName.Cloudapp.net`)을 입력합니다.
2. **사용자 이름** 및 **암호**에 대한 값을 입력합니다.

   ![Windows VM 이미지의 암호 인증](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>인증 테스트 실행

인증 도구에서 VM 이미지에 대한 매개 변수 값을 제공한 후 **연결 테스트**를 선택하여 VM에 올바르게 연결되도록 합니다. 연결이 확인되면 **다음**을 선택하여 테스트를 시작합니다.  테스트가 완료되면 테스트 결과(통과/실패/경고)가 있는 테이블이 표시됩니다.  다음 예제에서는 Linux VM 테스트에 대한 테스트 결과를 보여 줍니다. 

![Linux VM 이미지에 대한 인증 테스트 결과](./media/publishvm_029.png)

테스트 중 하나라도 실패하면 이미지가 인증되지 *않습니다*. 이 경우 요구 사항과 오류 메시지를 검토하고, 표시된 변경을 수행한 다음, 테스트를 다시 실행합니다. 

자동화된 테스트가 완료되면 **질문서** 화면에서 VM 이미지에 대한 추가 정보를 제공해야 합니다.  여기에는 완성해야 하는 두 개의 탭이 있습니다.  **일반 평가** 탭에는 **True/False** 질문이 포함되어 있는 반면, **커널 사용자 지정** 탭에는 여러 선택 및 자유형 질문이 포함되어 있습니다.  두 탭 모두에서 질문에 대해 대답하고, **다음**을 선택합니다.

![인증 도구 질문서](./media/publishvm_030.png)

마지막 화면에서는 Linux VM 이미지에 대한 SSH 액세스 정보 및 예외를 찾는 경우 실패한 평가에 대한 설명과 같은 추가 정보를 제공할 수 있습니다. 

마지막으로, **보고서 생성**을 클릭하여 실행된 테스트 사례에 대한 테스트 결과 및 로그 파일과 질문서에 대한 답변을 다운로드합니다. 결과를 VHD와 동일한 컨테이너에 저장합니다.

![인증 테스트 결과 저장](./media/publishvm_031.png)


## <a name="next-steps"></a>다음 단계

다음으로, 마켓플레이스에 제출하는 [각 VHD에 대한 URI(Uniform Resource Identifier)를 생성](./cpp-get-sas-uri.md)합니다. 
