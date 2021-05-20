---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99213747"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid 계정을 등록하려면
1. [Azure Portal][Azure portal]에 로그인합니다.
2. Azure Portal 메뉴 또는 홈페이지에서 **리소스 만들기** 를 선택합니다.

    ![리소스 만들기 옵션이 선택된 Azure Portal 메뉴의 스크린샷.][command-bar-new]
3. **SendGrid** 를 검색하여 선택합니다.

    ![검색 상자에 "SendGr"을 표시하고 검색 결과에서 선택된 SendGrid를 보여주는 Azure Portal Marketplace 화면의 스크린샷.][sendgrid-store]
4. 등록 양식을 작성하고 **만들기** 를 선택합니다.

    ![이름, 암호, 구독 및 리소스 그룹 필드가 채워진 새 SendGrid 계정 만들기 대화 상자의 스크린샷.][sendgrid-create]
5. Azure 설정에서 사용자의 SendGrid 서비스를 식별하기 위한 **이름** 을 입력합니다. 이름은 1자에서 100자 사이의 문자여야 하며, 영숫자, 대시, 점, 밑줄만 포함됩니다. 이 이름은 가입한 Azure 저장소 항목 목록에서 고유해야 합니다.
6. **암호** 를 입력하고 확인합니다.
7. **구독** 을 선택합니다.
8. 새 **리소스 그룹** 을 만들거나 기존 그룹을 선택합니다.
9. **가격 책정 계층** 섹션에서 등록할 SendGrid 플랜을 선택합니다.

    ![가격 책정 계층 선택 섹션이 열린 새 SendGrid 계정 만들기 대화 상자의 스크린샷][sendgrid-pricing]
10. 프로모션 코드를 갖고 있는 경우 **프로모션 코드** 를 입력합니다.
11. **연락처 정보** 를 입력합니다.
12. **약관** 을 검토하고 동의합니다.
13. 구매를 확인하면 **배포 성공** 팝업이 나타나고 계정이 나열됩니다.

    ![나열된 새 계정 ContosoSendGrid를 보여주는 SendGrid 계정 페이지의 스크린샷.][all-resources]

    구매를 완료하고 **관리** 단추를 클릭하여 전자 메일 확인 프로세스를 시작하면 SendGrid로부터 계정을 확인하라는 전자 메일을 받게 됩니다. 이 이메일을 받지 못했거나 계정 확인에 문제가 있는 경우 FAQ를 참조하세요.

    ![관리 단추가 강조 표시된 ContosoSendGrid 계정 페이지의 스크린샷.][manage]

    **계정을 확인하기 전에는 하루에 최대 100개의 전자 메일을 보낼 수 있습니다.**

    구독 계획을 수정하거나 SendGrid 연락처 설정을 보려면 SendGrid 서비스 이름을 클릭하여 SendGrid Marketplace 대시보드를 여세요.

    ![ContosoSendGrid 계정 페이지에서 모든 설정을 선택하여 ContosoSendGrid 계정에 대한 설정 페이지를 열었는지 보여주는 스크린샷.][settings]

    SendGrid를 사용하여 전자 메일을 보내려면 API 키를 입력해야 합니다.

### <a name="to-find-your-sendgrid-api-key"></a>SendGrid API 키를 찾으려면
1. **관리** 를 클릭합니다.

    ![관리 단추가 강조 표시된 ContosoSendGrid 계정 페이지의 스크린샷.][manage]
2. SendGrid 대시보드의 왼쪽 메뉴에서 **설정** 을 선택한 다음 **API 키** 를 선택합니다.

    ![설정 드롭다운이 열리고 API 키가 선택된 SendGrid 대시보드의 스크린샷.][api-keys]

3. **API 키 만들기** 를 클릭합니다.

    ![API 키 만들기 단추가 선택된 API 키 화면의 스크린샷.][general-api-key]
4. 적어도 **이 키의 이름** 을 입력하고 **메일 보내기** 에 대한 전체 액세스를 부여한 후 **저장** 을 선택합니다.

    ![메일 보내기가 전체 액세스로 설정되고, 예약된 보내기가 액세스 권한 없음으로 설정되고, 저장 단추가 강조 표시된 새 일반 API 키 추가 화면의 스크린샷.][access]
5. 이 시점에서 사용자의 API가 한 번 표시됩니다. 안전하게 저장해 놓으세요.

### <a name="to-find-your-sendgrid-credentials"></a>SendGrid 자격 증명을 찾으려면
1. 열쇠 아이콘을 클릭하여 **사용자 이름** 을 찾습니다.

    ![키 아이콘이 강조 표시된 ContosoSendGrid 계정 페이지의 스크린샷.][key]
2. 암호는 설치 시 선택한 암호입니다. **암호 변경** 또는 **암호 재설정** 을 선택하여 암호를 변경할 수 있습니다.

전자 메일 배달 설정을 관리하려면 **관리** 단추를 클릭합니다. 그러면 SendGrid 대시보드로 리디렉션됩니다.

![관리 단추가 강조 표시된 ContosoSendGrid 계정 페이지의 스크린샷.][manage]

SendGrid 통해 전자 메일을 보내는 방법에 대한 자세한 내용은 [전자 메일 API 개요][Email API Overview]를 참조하세요.

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
