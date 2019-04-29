---
title: Azure에서 아웃바운드 SMTP 연결 문제 해결 | Microsoft Docs
description: Azure에서 아웃바운드 SMTP 연결 문제를 해결하는 방법을 알아봅니다.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 385163d791bff0c02a05ee1b27afd82c3afd0ac3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401922"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Azure에서 아웃바운드 SMTP 연결 문제 해결

2017년 11월 15일부터 VM(가상 머신)에서 외부 도메인(예: outlook.com 및 gmail.com)에 직접 전송되는 아웃바운드 이메일 메시지는 Microsoft Azure에서 특정 구독 형식에만 사용할 수 있습니다. TCP 포트 25를 사용하는 아웃바운드 SMTP 연결이 차단되었습니다. (포트 25는 주로 인증되지 않은 이메일 배달을 위해 사용됩니다.)

이 동작의 변경 내용은 2017년 11월 15일 이후 새 구독 및 새 배포에만 적용됩니다.

## <a name="recommended-method-of-sending-email"></a>이메일을 전송하는 권장 방법
인증된 SMTP 릴레이 서비스(일반적으로 TCP 포트 587 또는 443을 통해 연결되지만 다른 포트도 지원함)를 사용하여 Azure VM 또는 Azure App Services에서 이메일을 보내는 것이 좋습니다. 이러한 서비스는 타사 이메일 공급자가 메시지를 거부할 가능성을 최소화하도록 IP 또는 도메인 평판을 유지하기 위해 사용됩니다. 이러한 SMTP 릴레이 서비스는 [SendGrid](https://sendgrid.com/partners/azure/)를 비롯한 다양한 기능을 포함합니다. 실행 중인 온-프레미스에서 사용할 수 있는 보안 SMTP 릴레이 서비스가 설치되었을 수 있습니다.

이러한 이메일 배달 서비스를 사용하는 작업은 구독 형식에 상관없이 Azure에서 제한되지 않습니다.

## <a name="enterprise-agreement"></a>기업 계약
기업계약 Azure 사용자의 경우 인증된 릴레이를 사용하지 않고 이메일을 보내는 기술 기능이 변경되지 않았습니다. 신규 및 기존 기업계약 사용자는 모두 Azure 플랫폼에서 아무런 제한 없이 Azure VM에서 외부 이메일 공급자에게 직접 아웃바운드 이메일 배달을 시도할 수 있습니다. 이메일 공급자가 지정된 사용자에게서 들어오는 이메일을 허용하도록 보장하지 않지만 기업계약 구독 내의 Azure 플랫폼을 통해 배달 시도가 차단되지 않습니다. 특정 공급자를 포함하는 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 이메일 공급자와 직접 작업해야 합니다.

## <a name="pay-as-you-go"></a>Pay-As-You-Go
종량제 또는 Microsoft 파트너 네트워크 구독 제품에 2017년 11월 15일 이전에 등록한 경우 아웃바운드 이메일 배달을 시도하는 기술 기능이 변경되지 않았습니다. 모두 Azure 플랫폼에서 아무런 제한 없이 이러한 구독 내의 Azure VM에서 외부 이메일 공급자에게 직접 아웃바운드 이메일 배달을 계속 시도할 수 있습니다. 마찬가지로 이메일 공급자가 지정된 사용자에게서 들어오는 이메일을 수락하고, 사용자가 특정 공급자를 포함하는 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 이메일 공급자와 직접 작업하도록 보장되지 않습니다.

2017년 11월 15일 이후에 생성된 종량제 또는 Microsoft 파트너 네트워크 구독의 경우 이러한 구독 내의 VM에서 직접 보낸 이메일을 차단하는 기술적 제한이 있습니다. 이메일을 Azure VM에서 외부 이메일 공급자로 직접 보내려는 경우(인증된 SMTP 릴레이를 사용하지 않음) 제한을 제거하도록 요청할 수 있습니다. 요청은 Microsoft의 재량에 따라 검토되고 승인되어 추가 부패 방지 검사를 수행한 후에만 허가됩니다. 요청을 하려면 다음 문제 유형을 사용 하 여 지원 사례를 엽니다. **기술** > **Virtual Network** > **연결** > **이메일 (P/포트 25)** 합니다. 배포에서 인증된 릴레이 사용하는 대신 메일 공급자에게 직접 메일을 보내야 하는 이유에 대한 세부 정보를 추가했는지 확인합니다.

종량제 또는 Microsoft 파트너 네트워크 구독이 제외되는 경우 앞으로 해당 구독 내의 VM만 제외됩니다.

> [!NOTE]
> Microsoft에서 서비스 약관이 위반되었음을 판단하게 되면 이 예외를 취소할 수 있는 권한을 가집니다.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark 및 평가판
2017년 11월 15일 이후에 MSDN, Azure Pass, Azure는 Open, Education, BizSpark, Azure 스폰서쉽, Azure 학생, 평가판 또는 Visual Studio 구독을 생성한 경우 이러한 구독 내의 VM에서 이메일 공급자에 직접 전송되는 이메일을 차단하는 기술적 제한이 포함됩니다. 제한 사항은 남용을 방지하기 위해 적용됩니다. 이 제한 사항을 제거하는 요청은 허가되지 않습니다.

이러한 구독 형식을 사용하는 경우 이 문서의 앞부분에 설명된 대로 SMTP 릴레이 서비스를 사용하는 것이 좋습니다.

## <a name="cloud-service-provider-csp"></a>CSP(클라우드 서비스 공급자)

CSP를 통해 Azure 리소스를 사용하는 경우 CSP를 선택하여 지원 사례를 만들 수 있습니다. 또한 보안 SMTP 릴레이를 사용할 수 없는 경우 사용자 대신 차단 해제 사례를 만들도록 CSP에 요청할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
