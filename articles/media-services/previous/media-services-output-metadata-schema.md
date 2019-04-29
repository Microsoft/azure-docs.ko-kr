---
title: Azure Media Services 출력 메타데이터 스키마 | Microsoft 문서
description: 이 항목에서는 Azure Media Services 출력 메타데이터 스키마에 대한 개요를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 42227095c69924cd2922673d020b349aa29f2daa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61129749"
---
# <a name="output-metadata"></a>출력 메타데이터
## <a name="overview"></a>개요
인코딩 작업은 일부 인코딩 태스크를 수행할 입력 자산(또는 자산)과 연결됩니다. 예를 들어 MP4 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩하며, 미리 보기 이미지와 오버레이를 만듭니다. 태스크가 완료되는 즉시 출력 자산이 생성됩니다.  출력 자산에는 비디오, 오디오, 미리 보기 등이 포함됩니다. 출력 자산에는 출력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타데이터 XML 파일의 이름은 &lt;source_file_name&gt;_manifest.xml 형식입니다(예: BigBuckBunny_manifest.xml).  

Media Services는 메타데이터를 생성하기 위해 선제적으로 입력 자산을 검사하지 않습니다. 입력 자산이 작업에서 처리되는 경우 입력 메타데이터는 아티팩트로만 생성됩니다. 따라서 이 아티팩트는 출력 자산에 기록됩니다. 다른 도구는 입력 자산 및 출력 자산에 대한 메타데이터를 생성하는 데 사용됩니다. 따라서 입력 메타데이터는 출력 메타데이터와 스키마가 약간 다를 수 있습니다.

메타데이터 파일을 검사하려는 경우 **SAS** 로케이터를 만들어 로컬 컴퓨터에 파일을 다운로드할 수 있습니다.  

이 문서에서는 출력 메타데이터(&lt;source_file_name&gt;_manifest.xml)의 기초가 되는 XML 스키마의 요소 및 형식에 대해 설명합니다. 입력 자산에 대한 메타데이터가 포함된 파일에 대한 자세한 내용은 입력 메타데이터를 참조하세요.  

스키마 코드 및 XML 예제는 이 문서의 끝에 있습니다.  

## <a name="AssetFiles"></a> AssetFiles 루트 요소
인코딩 작업에 대한 AssetFile 항목의 컬렉션입니다.  

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |AssetFiles 컬렉션의 일부인 AssetFile 요소입니다. |

## <a name="AssetFile"></a> AssetFile 요소
[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Name**<br/><br/> 필수 |**xs:string** |미디어 자산 파일 이름입니다. |
| **크기**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:long** |자산 파일의 크기(바이트)입니다. |
| **Duration**<br/><br/> 필수 |**xs:duration** |콘텐츠 재생 시간입니다. |

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **Sources** |이 AssetFile을 생성하기 위해 처리된 입력/원본 미디어 파일의 컬렉션입니다. 자세한 내용은 Source 요소를 참조하세요. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. 자세한 내용은 VideoTracks 요소를 참조하세요. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. 이 요소는 이러한 모든 오디오 트랙의 컬렉션입니다. 자세한 내용은 AudioTracks 요소를 참조하세요. |

## <a name="Sources"></a> Sources 요소
이 AssetFile을 생성하기 위해 처리된 입력/원본 미디어 파일의 컬렉션입니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **원본**<br/><br/> minOccurs="1" maxOccurs="unbounded" |이 자산을 생성할 때 사용되는 입력/원본 파일입니다. 자세한 내용은 Source 요소를 참조하세요. |

## <a name="Source"></a> Source 요소
이 자산을 생성할 때 사용되는 입력/원본 파일입니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Name**<br/><br/> 필수 |**xs:string** |입력 원본 파일 이름입니다. |

## <a name="VideoTracks"></a> VideoTracks 요소
각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. **VideoTracks** 요소는 모든 비디오 트랙의 컬렉션을 나타냅니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |부모 AssetFile의 특정 비디오 트랙입니다. 자세한 내용은 VideoTrack 요소를 참조하세요. |

## <a name="VideoTrack"></a> VideoTrack 요소
부모 AssetFile의 특정 비디오 트랙입니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |이 비디오 트랙의 0 기준 인덱스입니다. **참고:**  이 **ID**가 반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. |
| **FourCC**<br/><br/> 필수 |**xs:string** |비디오 코덱 FourCC 코드입니다. |
| **프로필** |**xs:string** |H264 프로파일입니다(H264 코덱에만 적용). |
| **Level** |**xs:string** |H264 수준입니다(H264 코덱에만 적용). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |인코딩된 비디오 너비(픽셀)입니다. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |인코딩된 비디오 높이(픽셀)입니다. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:double** |비디오 디스플레이 가로 세로 비율의 분자입니다. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:double** |비디오 디스플레이 가로 세로 비율의 분모입니다. |
| **Framerate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs: decimal** |.3f 형식으로 측정된 비디오 프레임 속도입니다. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs: decimal** |.3f 형식으로 기본 설정된 대상 비디오 프레임 속도입니다. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |AssetFile에서 계산되는 평균 비디오 비트 전송률(Kb/초)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |인코딩 기본 설정을 통해 요청된 대로 이 비디오 트랙의 대상 평균 비트 전송률(Kbps)입니다. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |이 비디오 트랙의 최대 GOP 평균 비트 전송률(Kb/초)입니다. |

## <a name="AudioTracks"></a> AudioTracks 요소
각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. **AudioTracks** 요소는 모든 오디오 트랙의 컬렉션을 나타냅니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |부모 AssetFile의 특정 오디오 트랙입니다. 자세한 내용은 AudioTrack 요소를 참조하세요. |

## <a name="AudioTrack"></a> AudioTrack 요소
부모 AssetFile의 특정 오디오 트랙입니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |이 오디오 트랙의 0 기준 인덱스입니다. **참고:**  반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. |
| **Codec** |**xs:string** |오디오 트랙 코덱 문자열입니다. |
| **EncoderVersion** |**xs:string** |EAC3에 필요한 선택적 인코더 버전 문자열입니다. |
| **Channels**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |오디오 채널 수입니다. |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |오디오 샘플링 속도(샘플/초 또는 Hz)입니다. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |AssetFile에서 계산되는 평균 오디오 비트 전송률(bps)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> 필수 |**xs:int** |wFormatTag 형식 샘플당 비트입니다. |

### <a name="child-elements"></a>자식 요소
| 이름 | 설명 |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |음량 측정 결과 매개 변수입니다. 자세한 내용은 LoudnessMeteringResultParameters 요소를 참조하세요. |

## <a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters 요소
음량 측정 결과 매개 변수입니다.  

[XML 예제](#xml)에서 XML 예제를 찾을 수 있습니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |**DPLM**(Dolby Professional Loudness Metering) 개발 키트 버전입니다. |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> 필수 |**xs:int** |DPLM을 통해 생성되며, LoudnessMetering을 설정할 때 필요합니다. |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> 필수 |**xs:float** |통합된 음량입니다. |
| **IntegratedLoudnessUnit**<br/><br/> 필수 |**xs:string** |통합된 음량 단위입니다. |
| **IntegratedLoudnessGatingMethod**<br/><br/> 필수 |**xs:string** |게이팅 식별자입니다. |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |프로그램을 통과하는 음성 콘텐츠(%)입니다. |
| **SamplePeak**<br/><br/> 필수 |**xs:float** |다시 설정되거나 마지막으로 지워진 이후 채널당 샘플 최대 사용 절대값입니다.  단위는 dBFS입니다. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> 필수 |**xs:anySimpleType** |샘플 최대 사용 단위입니다. |
| **TruePeak**<br/><br/> 필수 |**xs:float** |다시 설정되거나 마지막으로 지워진 이후 채널당 실제 최대 사용 값입니다(ITU-R BS.1770-2 규격). 단위는 dBTP입니다. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> 필수 |**xs:anySimpleType** |실제 최대 사용 단위입니다. |

## <a name="schema-code"></a>스키마 코드
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Width" use="required">  
                              <xs:annotation>  
                                <xs:documentation>encoded video width in pixels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Height" use="required">  
                              <xs:annotation>  
                                <xs:documentation>encoded video height in pixels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:double">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:double">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Framerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="MaxGOPBitrate">  
                              <xs:annotation>  
                                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Channels" use="required">  
                              <xs:annotation>  
                                <xs:documentation>number of audio channels</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="SamplingRate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml"></a> XML 예제

다음 XML은 출력 메타데이터 파일의 예제입니다.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
