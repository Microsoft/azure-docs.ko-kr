---
title: Azure Media Services 패키징 및 원본 오류 | Microsoft Docs
description: 이 항목에서는 Orgin (Azure Media Services Streaming Endpoint) 서비스에서 받을 수 있는 오류에 대해 설명 합니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8f4e0d9a6781e35c1ed6197dd32181511506d145
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256483"
---
# <a name="streaming-endpoint-origin-errors"></a>스트리밍 엔드포인트(원본) 오류 

이 항목에서는 Azure Media Services [스트리밍 끝점 서비스](streaming-endpoint-concept.md)에서 받을 수 있는 오류에 대해 설명 합니다.

## <a name="400-bad-request"></a>400 잘못된 요청

요청에 잘못 된 정보가 포함 되어 있으며 다음 이유 중 하나로 인해 이러한 오류 코드와 함께 거부 됩니다.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 구문 또는 형식 오류입니다. 예를 들어 잘못 된 유형, 잘못 된 조각 또는 잘못 된 추적에 대 한 요청이 있습니다. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|요청에 URL에 암호화 태그가 없습니다. CMAF 요청은 URL에 암호화 태그가 필요 합니다. 암호화 유형을 두 개 이상 사용 하 여 구성 된 다른 프로토콜에는 명확성을 위해 암호화 태그도 필요 합니다. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|요청을 처리 하는 저장소에 대 한 요청이 잘못 된 요청 오류로 인해 실패 했습니다. |

## <a name="403-forbidden"></a>403 사용할 수 없음

요청은 다음과 같은 이유로 허용되지않습니다.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|요청을 처리 하는 저장소에 대 한 요청이 인증 실패로 인해 실패 했습니다. 저장소 키를 회전 하 고 서비스에서 저장소 키를 동기화 할 수 없는 경우이 문제가 발생할 수 있습니다. <br/><br/>Azure Portal에서 [도움말 + 지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 으로 이동 하 여 Azure 지원에 문의 하세요.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |저장소 작업 오류, 계정 권한 부족으로 인해 액세스 하지 못했습니다. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |저장소 계정이 사용 하지 않도록 설정 되어 있으므로 요청을 처리 하는 저장소에 대 한 요청이 실패 했습니다. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |저장소 작업 오류, 일반 오류로 인해 액세스 하지 못했습니다. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |StreamingPolicy의 구성으로 인해 출력 형식이 차단 됩니다. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |콘텐츠에 대 한 암호화가 필요 합니다. 출력 형식에는 배달 정책이 필요 합니다. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |배달 정책 설정에 암호화가 설정 되지 않았습니다. |

## <a name="404-not-found"></a>404 찾을 수 없음

더 이상 없는 리소스에 대해 작업을 수행했습니다. 리소스가 이미 삭제된 경우를 예로 들 수 있습니다.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |요청 된 추적을 찾을 수 없습니다. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |요청 된 리소스를 찾을 수 없습니다. |
|MPE_UNAUTHORIZED |0x80890244 |액세스 권한이 없습니다. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |요청 된 타임 스탬프를 찾을 수 없습니다. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |요청 된 동적 매니페스트 필터를 찾을 수 없습니다. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |요청한 조각 인덱스가 올바른 범위를 벗어났습니다. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Moov 버퍼를 가져오기 위해 라이브 미디어 항목을 찾을 수 없습니다. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |특정 트랙에 대해 요청 된 시간에 조각을 찾을 수 없습니다.<br/><br/>조각이 저장소에 있지 않을 수 있습니다. 조각이 있을 수 있는 프레젠테이션의 다른 계층을 사용해 보세요. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |매니페스트에서 요청한 비트 전송률에 대 한 미디어 항목을 찾을 수 없습니다. <br/><br/>플레이어에서 매니페스트에 없는 특정 비트 전송률의 비디오 트랙을 요청 했을 수 있습니다.|
|MPE_METADATA_NOT_FOUND |0x80890257 |매니페스트에서 특정 메타 데이터를 찾을 수 없거나 저장소에서 주소를 다시 지정 하는 것을 찾을 수 없습니다. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |저장소 작업 오류가 발생 했습니다. 리소스를 찾을 수 없습니다. |

## <a name="409-conflict"></a>409 충돌

또는 작업에서 리소스에 대해 제공 된 ID가 `PUT` `POST` 기존 리소스에서 사용 되었습니다. 리소스에 다른 ID를 사용 하 여이 문제를 해결 하십시오.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |저장소 작업 오류, 충돌 오류입니다.  |

## <a name="410"></a>410

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|라이브 스트리밍의 경우 forceEndTimestamp가 true로 설정 된 필터는 시작 또는 종료 타임 스탬프가 현재 DVR 창 외부에 있습니다.|

## <a name="412-precondition-failure"></a>412 사전 조건 실패

작업에서 서버에서 사용할 수 있는 버전과 다른 eTag (낙관적 동시성 오류)를 지정 했습니다. 리소스의 최신 버전을 확인하고 요청의 eTag를 업데이트한 후에 요청을 다시 시도해야 합니다.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |요청 된 조각이 준비 되지 않았습니다.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|저장소 작업 오류, 사전 조건 오류입니다.|

## <a name="415-unsupported-media-type"></a>415 지원 되지 않는 미디어 유형

클라이언트에서 보낸 페이로드 형식이 지원 되지 않는 형식입니다.

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 이미 암호화 된 콘텐츠에는 암호화를 적용 하면 안 됩니다.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |입력 형식에 대 한 암호화가 잘못 되었습니다.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 배달 정책 형식이 잘못 되었습니다.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |원래 설정은 여러 출력 형식으로 공유할 수 있습니다.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|미디어 형식이 나 형식이 지원 되지 않습니다. 예를 들어 Media Services은 64 이상의 품질 수준 수를 지원 하지 않습니다. FLV video 태그에서 Media Services는 여러 SPS 및 여러 PPS가 있는 비디오 프레임을 지원 하지 않습니다.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| 요청한 자산의 입력 형식은 지원 되지 않습니다. Media Services은 부드러운 (라이브), MP4 (VoD) 및 프로그레시브 다운로드 형식을 지원 합니다.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|요청 된 출력 형식은 지원 되지 않습니다. Media Services은 부드러운, 대시 (CSF, CMAF), HLS (v3, v4, CMAF) 및 프로그레시브 다운로드 형식을 지원 합니다.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|지원 되지 않는 암호화 형식이 발견 되었습니다.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|요청 된 미디어 유형은 출력 형식에서 지원 되지 않습니다. 지원 되는 형식은 video, audio 또는 "SUBT" 부제목입니다.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|원본 자산 미디어가 출력 형식과 호환 되지 않는 미디어 형식으로 인코딩되어 있습니다.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|원본 자산이 출력 형식과 호환 되지 않는 비디오 형식으로 인코딩되어 있습니다. H.264, AVC, 265 (HEVC, hev1 또는 hvc1)가 지원 됩니다.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|원본 자산이 출력 형식과 호환 되지 않는 오디오 형식으로 인코딩되어 있습니다. 지원 되는 오디오 형식은 AAC, E-E-AC3 (DD +), 돌비 DTS입니다.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|원본으로 보호 된 자산을 출력 형식으로 변환할 수 없습니다.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|출력 형식에서 보호 형식을 지원 하지 않습니다.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|보호 형식이 입력 형식에서 지원 되지 않습니다.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|비디오 NAL 단위가 잘못 되었습니다. 예를 들어 샘플의 첫 번째 NAL만 AUD 수 있습니다.|
|MPE_INVALID_NALU_SIZE|0x80890260|NAL 단위 크기가 잘못 되었습니다.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|NAL 단위 길이 값이 잘못 되었습니다.|
|MPE_FILTER_INVALID|0x80890236|잘못 된 동적 매니페스트 필터입니다.|
|MPE_FILTER_VERSION_INVALID|0x80890237|잘못 되었거나 지원 되지 않는 필터 버전입니다.|
|MPE_FILTER_TYPE_INVALID|0x80890238|필터 형식이 잘못 되었습니다.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|필터에서 잘못 된 범위를 지정 했습니다.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|필터에서 잘못 된 추적 특성을 지정 했습니다.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|필터에서 잘못 된 프레젠테이션 창 길이를 지정 했습니다.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|잘못 된 라이브 백오프는 필터에 의해 지정 됩니다.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|레거시 필터에서는 하나의 absTimeInHNS 요소만 지원 됩니다.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|필터를 적용 한 후에는 스트림이 더 이상 없습니다.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023a|라이브 백오프는 DVR 창을 초과 합니다.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|라이브 백오프는 프레젠테이션 창 보다 큽니다.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|최대 허용 기본 필터 10 개를 초과 했습니다.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|결합 된 요청 필터에서는 첫 번째 비디오 품질 연산자를 여러 개 사용할 수 없습니다.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|첫 번째 품질 비트 전송률 특성의 수는 1 이어야 합니다.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS 세그먼트 기간은 DVR 창의 1/3 보다 작아야 하 고 HLS 백오프를 해제 해야 합니다.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|조각 기간은 약 20 초 보다 작거나 같아야 합니다. 그렇지 않으면 입력 품질 수준에 시간이 정렬 되지 않습니다.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Dts 관련 오류는 DTS 상자를 구문 분석 하는 동안 DTSSpecficBox에 ReservedBox을 찾을 수 없습니다.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Dts 관련 오류입니다. DTS 상자를 구문 분석 하는 동안 DTSSpecficBox에서 채널을 찾을 수 없습니다.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTSSpecficBox의 DTS 관련 오류입니다. 샘플 유형이 일치 하지 않습니다.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 특정 오류, 다중 자산이 설정 되었지만 DTSH 샘플 유형이 일치 하지 않습니다.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 관련 오류입니다. 코어 스트림 크기가 잘못 되었습니다.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 관련 오류입니다. 샘플 확인이 잘못 되었습니다.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 특정 오류, 하위 스트림 확장 인덱스가 잘못 되었습니다.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 특정 오류, 하위 스트림 블록 번호가 잘못 되었습니다.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 특정 오류, 샘플링 빈도가 잘못 되었습니다.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 관련 오류입니다. 하위 스트림 확장의 참조 클록 코드가 잘못 되었습니다.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 특정 오류, 스피커 다시 매핑 집합 수가 잘못 되었습니다.|

암호화 문서 및 예제는 다음을 참조 하세요.

- [개념: 콘텐츠 보호](content-protection-overview.md)
- [개념: 콘텐츠 키 정책](content-key-policy-concept.md)
- [개념: 스트리밍 정책](streaming-policy-concept.md)
- [샘플: AES 암호화를 사용 하 여 보호](protect-with-aes128.md)
- [샘플: DRM으로 보호](protect-with-drm.md)

필터 지침은 다음을 참조 하세요.

- [개념: 동적 매니페스트](filters-dynamic-manifest-overview.md)
- [개념: 필터](filters-concept.md)
- [샘플: REST Api를 사용 하 여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [샘플: .NET을 사용 하 여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [샘플: CLI를 사용 하 여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

라이브 문서 및 샘플은 다음을 참조 하세요.

- [개념: 라이브 스트리밍 개요](live-streaming-overview.md)
- [개념: 라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)
- [샘플: 라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 범위가 충분하지 않음

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|저장소 작업 오류가 발생 했습니다. http 416 오류가 반환 되었습니다. 잘못 된 범위입니다.|

## <a name="500-internal-server-error"></a>500 내부 서버 오류

요청을 처리하는 동안 Media Services가 처리를 계속 실행하는 것을 막는 오류가 발생합니다.  

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|ERROR_WINHTTP_TIMEOUT의 Winhttp 오류 코드 (0x00002ee2)에서 수신 및 변환 합니다.|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|ERROR_WINHTTP_CONNECTION_ERROR의 Winhttp 오류 코드 (0x00002efe)에서 수신 및 변환 합니다.|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|ERROR_WINHTTP_NAME_NOT_RESOLVED의 Winhttp 오류 코드 (0x00002ee7)에서 수신 및 변환 됩니다.|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|저장소 작업 오류입니다. HTTP 500 오류 중 하나의 일반 InternalError입니다.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|HTTP 500 오류 중 하나에 대 한 일반 OperationTimedOut 저장소 작업 오류입니다.|
|MPE_STORAGE_FAILURE|0x808900F2|저장소 작업 오류, 기타 HTTP 500 오류 (InternalError 또는 OperationTimedOut)|

## <a name="503-service-unavailable"></a>503 서비스를 사용할 수 없음

서버는 현재 요청을 받을 수 없습니다. 이 오류는 서비스에 요청을 과도하게 해서 발생할 수 있습니다. Media Services 제한 메커니즘은 서비스에 과도한 요청을 보내는 애플리케이션의 리소스 사용을 제한합니다.

> [!NOTE]
> 503 오류가 발생하는 이유에 관한 자세한 정보를 얻기 위해 오류 메시지 및 오류 코드 문자열을 확인합니다. 이 오류가 항상 제한을 의미하지는 않습니다.
> 

|오류 코드|16 진수 값 |오류 설명|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|저장소 작업 오류, 수신 HTTP 서버 사용 중 오류 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

- [인코딩 오류 코드](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services 개념](concepts-overview.md)
- [할당량 및 제한](limits-quotas-constraints.md)

## <a name="next-steps"></a>다음 단계

[예: .NET을 사용 하 여 ApiException에서 ErrorCode 및 메시지 액세스](configure-connect-dotnet-howto.md#connect-to-the-net-client)
