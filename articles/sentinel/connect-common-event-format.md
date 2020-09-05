---
title: Azure 센티널 Preview에 CEF 데이터 연결 | Microsoft Docs
description: Linux 컴퓨터를 프록시로 사용 하 여 CEF (Common Event Format) 메시지를 Azure 센티널로 보내는 외부 솔루션을 연결 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 51e6c74a8b80b94ca552645cfbb76bd4e162a62b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650063"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>일반적인 이벤트 형식을 사용 하 여 외부 솔루션 연결


CEF 메시지를 전송 하는 외부 솔루션을 연결 하는 경우 Azure 센티널에 연결 하는 세 가지 단계가 있습니다.

1 단계: [CEF 연결 에이전트를 배포 하 여](connect-cef-agent.md) 2 단계: [솔루션 특정 단계 수행](connect-cef-solution-config.md) : 3 단계: [연결 확인](connect-cef-verify.md)

이 문서에서는 연결이 작동 하는 방식을 설명 하 고, 필수 구성 요소를 제공 하며, Syslog 위에 CEF (일반 이벤트 형식) 메시지를 보내는 보안 솔루션에 에이전트를 배포 하는 단계를 제공 합니다. 

> [!NOTE] 
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

이 연결을 만들려면 어플라이언스와 Azure 센티널 간의 통신을 지원 하기 위해 전용 Linux 컴퓨터 (VM 또는 온-프레미스)에 에이전트를 배포 해야 합니다. 다음 다이어그램은 Azure에서 Linux VM의 경우의 설정에 대해 설명 합니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

또는 다른 클라우드 또는 온-프레미스 컴퓨터에서 VM을 사용 하는 경우이 설정이 존재 합니다. 

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>보안 고려 사항

조직의 보안 정책에 따라 컴퓨터의 보안을 구성 해야 합니다. 예를 들어 회사 네트워크 보안 정책에 맞게 네트워크를 구성 하 고, 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다. 다음 지침을 사용 하 여 컴퓨터 보안 구성을 향상 시킬 수 있습니다.  [Azure에서 VM 보안](../virtual-machines/security-policy.md), [네트워크 보안에 대 한 모범 사례](../security/fundamentals/network-best-practices.md)를 참조 하세요.

보안 솔루션과 Syslog 컴퓨터 간에 TLS 통신을 사용 하려면 tls: s a s [-rsyslog를 사용 하 여 Syslog 트래픽 암호화](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), tls- [syslog를 사용 하 여 로그 메시지 암호화](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)를 사용 하 여 syslog 데몬 (rsyslog 또는 syslog 기능)을 통신 하도록 구성 해야 합니다.

 
## <a name="prerequisites"></a>전제 조건
프록시로 사용 하는 Linux 컴퓨터가 다음 운영 체제 중 하나를 실행 하 고 있는지 확인 합니다.

- 64비트
  - CentOS 6 및 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 및 7
  - Red Hat Enterprise Linux Server 6 및 7
  - Debian GNU/Linux 8 및 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS 및 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32비트
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 및 9
   - Ubuntu Linux 14.04 LTS 및 16.04 LTS
 
 - 디먼 버전
   - Syslog-기능: 2.1-3.22.1
   - Rsyslog: v8
  
 - Syslog Rfc 지원
   - Syslog RFC 3164
   - Syslog RFC 5424
 
컴퓨터가 다음 요구 사항도 충족 하는지 확인 합니다. 
- 사용 권한
    - 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 
- 소프트웨어 요구 사항
    - 컴퓨터에서 Python을 실행 하 고 있는지 확인 합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

