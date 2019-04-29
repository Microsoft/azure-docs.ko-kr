---
title: Widevine 라이선스 템플릿을 사용하는 Azure Media Services 개요 | Microsoft Docs
description: 이 항목에서는 Widevine 라이선스를 구성하는 데 사용되는 Widevine 라이선스 템플릿의 개요를 제공합니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466326"
---
# <a name="widevine-license-template-overview"></a>Widevine 라이선스 템플릿 개요 

Azure Media Services를 사용하면 **Google Widevine**을 사용하여 콘텐츠를 암호화할 수 있습니다. 또한 Media Services는 Widevine 라이선스를 제공하는 서비스를 제공합니다. Azure Media Services API를 사용하여 Widevine 라이선스를 구성할 수 있습니다. 플레이어가 Widevine으로 보호된 콘텐츠를 재생하려고 하면 라이선스 배달 서비스로 요청이 전송되어 라이선스를 얻습니다. 라이선스 서비스가 요청을 승인하면 서비스에서 라이선스를 발급합니다. 클라이언트로 전송되며 지정된 콘텐츠를 암호 해독하고 재생하는 데 사용됩니다.

Widevine 라이선스 요청 형식은 JSON 메시지입니다.  

>[!NOTE]
> 값 없이 "{}"만 포함하여 빈 메시지를 만들 수 있습니다. 그러면 라이선스 템플릿이 기본값으로 생성됩니다. 기본값은 대부분의 경우 적합합니다. Microsoft 기반 라이선스 배달 시나리오는 항상 기본값을 사용해야 합니다. “provider“ 및 “content_id“ 값을 설정해야 하는 경우 공급자가 Widevine 자격 증명과 일치해야 합니다.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON 메시지

| 이름 | 값 | 설명 |
| --- | --- | --- |
| payload |Base64로 인코드된 문자열 |클라이언트에서 보낸 라이선스 요청입니다. |
| content_id |Base64로 인코드된 문자열 |각 content_key_specs.track_type의 키 ID와 콘텐츠 키를 파생하는 데 사용되는 식별자입니다. |
| provider |문자열 |콘텐츠 키 및 정책을 조회하는 데 사용합니다. Widevine 라이선스 배달에 Microsoft 키 배달을 사용하는 경우 이 매개 변수는 무시됩니다. |
| policy_name |문자열 |이전에 등록된 정책의 이름입니다. 선택 사항입니다. |
| allowed_track_types |enum |SD_ONLY 또는 SD_HD. 라이선스에 포함되는 콘텐츠 키를 제어합니다. |
| content_key_specs |JSON 구조 배열(“콘텐츠 키 사양” 섹션 참조).  |반환할 콘텐츠 키에 대한 보다 세분화된 제어. 자세한 내용은 “콘텐츠 키 사양” 섹션을 참조하세요. Allowed_track_types 및 content_key_specs 값 중 하나만 지정할 수 있습니다. |
| use_policy_overrides_exclusively |Boolean, true 또는 false |policy_overrides로 지정된 정책 특성을 사용하고 이전에 저장된 모든 정책은 생략합니다. |
| policy_overrides |JSON 구조(“정책 재정의” 섹션 참조). |이 라이선스에 대한 정책 설정입니다.  이 자산에 미리 정의된 정책이 있는 경우 이러한 지정된 값이 사용됩니다. |
| session_init |JSON 구조(“세션 초기화” 섹션 참조). |선택적 데이터가 라이선스에 전달됩니다. |
| parse_only |Boolean, true 또는 false |라이선스 요청이 구문 분석되지만 라이선스는 발급되지 않습니다. 그러나 라이선스 요청의 값이 응답에 반환됩니다. |

## <a name="content-key-specs"></a>콘텐츠 키 사양
기존 정책이 있는 경우, 콘텐츠 키 사양에 값을 지정할 필요가 없습니다. 이 콘텐츠와 연관된 기존 정책은 HDCP(High-bandwidth Digital Content Protection), CGMS(Copy General Management System) 등의 출력 보호를 확인하는 데 사용됩니다. 기존 정책이 Widevine 라이선스 서버에 등록되지 않은 경우, 콘텐츠 공급자가 라이선스 요청에 값을 삽입할 수 있습니다.   

use_policy_overrides_exclusively 옵션에 관계없이 모든 트랙에 대해 각 content_key_specs 값을 지정해야 합니다. 

| 이름 | 값 | 설명 |
| --- | --- | --- |
| content_key_specs. track_type |문자열 |트랙 유형 이름입니다. 라이선스 요청에 Content_key_specs를 지정한 경우 모든 트랙 유형을 명시적으로 지정해야 합니다. 이렇게 하지 않으면 이전 10초 재생에 실패합니다. |
| content_key_specs  <br/> security_level |uint32 |재생에 대한 클라이언트 견고성 요구 사항을 정의합니다. <br/> - 소프트웨어 기반 화이트 박스 암호화가 필요합니다. <br/> - 소프트웨어 암호화 및 난독 처리된 디코더가 필요합니다. <br/> - 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 키 자료 및 암호화 작업을 수행해야 합니다. <br/> - 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.  <br/> - 하드웨어 기반의 신뢰할 수 있는 실행 환경에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다. |
| content_key_specs <br/> required_output_protection.hdc |string, HDCP_NONE, HDCP_V1, HDCP_V2 중 하나 |HDCP가 필요한지 여부를 나타냅니다. |
| content_key_specs <br/>key |Base64로<br/>인코딩된 문자열 |이 트랙에 사용할 콘텐츠 키입니다. 지정된 경우 track_type 또는 key_id가 필요합니다. 콘텐츠 공급자는 Widevine 라이선스 서버에서 키를 생성하거나 조회하도록 하지 않고 이 옵션을 사용하여 이 트랙에 대한 콘텐츠 키를 삽입할 수 있습니다. |
| content_key_specs.key_id |Base64로 인코드된 문자열 이진, 16바이트 |키의 고유 식별자. |

## <a name="policy-overrides"></a>정책 재정의
| 이름 | 값 | 설명 |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, true 또는 false |콘텐츠의 재생이 허용됨을 나타냅니다. 기본값은 false입니다. |
| policy_overrides&#46;can_persist |Boolean, true 또는 false |라이선스를 오프라인 용도로 비휘발성 저장소에 저장할 수 있음을 나타냅니다. 기본값은 false입니다. |
| policy_overrides&#46;can_renew |Boolean, true 또는 false |이 라이선스의 갱신이 허용됨을 나타냅니다. true이면 라이선스의 기간을 하트비트로 확장할 수 있습니다. 기본값은 false입니다. |
| policy_overrides&#46;license_duration_seconds |int64 |이 특정 라이선스에 대한 기간을 나타냅니다. 값 0은 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides&#46;rental_duration_seconds |int64 |재생이 허용되는 동안 기간을 나타냅니다. 값 0은 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides&#46;playback_duration_seconds |int64 |라이선스 기간 내에서 재생이 시작된 후 표시 기간입니다. 값 0은 기간에 제한이 없음을 나타냅니다. 기본값은 0입니다. |
| policy_overrides&#46;renewal_server_url |문자열 |이 라이선스에 대한 모든 하트비트(갱신) 요청이 지정된 URL로 리디렉션됩니다. 이 필드는 can_renew가 true인 경우에만 사용됩니다. |
| policy_overrides&#46;renewal_delay_seconds |int64 |license_start_time 이후, 처음으로 갱신을 시도할 때까지의 시간(초)입니다. 이 필드는 can_renew가 true인 경우에만 사용됩니다. 기본값은 0입니다. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |실패 시 후속 라이선스 갱신 요청 간 지연 시간(초)을 지정합니다. 이 필드는 can_renew가 true인 경우에만 사용됩니다. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |갱신을 시도했지만 라이선스 서버와 백 엔드 문제로 인해 실패한 동안 재생을 계속할 수 있는 기간입니다. 값 0은 기간에 제한이 없음을 나타냅니다. 이 필드는 can_renew가 true인 경우에만 사용됩니다. |
| policy_overrides&#46;renew_with_usage |Boolean, true 또는 false |사용이 시작될 때 갱신을 위해 라이선스가 전송됨을 나타냅니다. 이 필드는 can_renew가 true인 경우에만 사용됩니다. |

## <a name="session-initialization"></a>세션 초기화
| 이름 | 값 | 설명 |
| --- | --- | --- |
| provider_session_token |Base64로 인코드된 문자열 |이 세션 토큰은 라이선스에 다시 전달되고 후속 갱신에 존재합니다. 세션 토큰은 세션 후에 지속되지 않습니다. |
| provider_client_token |Base64로 인코드된 문자열 |라이선스 응답으로 다시 보낼 클라이언트 토큰입니다. 클라이언트 요청에 클라이언트 토큰이 포함된 경우 이 값은 무시됩니다. 클라이언트 토큰은 라이선스 세션 후에도 지속됩니다. |
| override_provider_client_token |Boolean, true 또는 false |false이고 라이선스 요청에 클라이언트 토큰이 포함된 경우 이 구조에 클라이언트 토큰이 지정된 경우에도 요청의 토큰을 사용합니다. true이면 항상 이 구조에 지정된 토큰을 사용합니다. |

## <a name="configure-your-widevine-license-with-net"></a>.NET을 사용하여 Widevine 라이선스 구성 

Media Services는 Widevine 라이선스를 구성할 수 있는 클래스를 제공합니다. 라이선스를 생성하려면 JSON을 [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)에 전달합니다.

템플릿을 구성하려면 다음을 수행할 수 있습니다.

### <a name="directly-construct-a-json-string"></a>JSON 문자열을 직접 구성

이 방법은 오류가 발생할 수 있습니다. [필요한 클래스 정의 및 JSON으로 직렬화](#classes)에 설명된 다른 방법을 사용하는 것이 좋습니다.

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> 필요한 클래스 정의 및 JSON으로 직렬화

#### <a name="define-classes"></a>클래스 정의

다음 예제는 Widevine JSON 스키마에 매핑되는 클래스 정의의 예를 보여 줍니다. 클래스를 JSON 문자열로 직렬화하기 전에 인스턴스화할 수 있습니다.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>라이선스 구성

이전 섹션에 정의된 클래스를 사용하여 [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)을 구성하는 데 사용되는 JSON을 만듭니다.

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>다음 단계

[DRM으로 보호](protect-with-drm.md)하는 방법 확인
