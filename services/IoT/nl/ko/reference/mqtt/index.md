---

copyright:
  years: 2015, 2016

---

{:new_window: target="\_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}


# MQTT 메시징
{: #ref-mqtt}
마지막 업데이트 날짜: 2016년 9월 13일
{: .last-updated}

MQTT는 디바이스와 애플리케이션이 {{site.data.keyword.iot_full}}과 통신하는 데 사용하는 기본 프로토콜입니다. MQTT는 센서와 모바일 디바이스 간에 효율적으로 실시간 데이터를 교환하도록 설계된 공개 및 구독 메시징 전송 프로토콜입니다.
{:shortdesc}

MQTT는 TCP/IP를 통해 실행되며 TCP/IP에 직접 코딩할 수 있지만, MQTT 프로토콜의 세부사항을 처리하는 라이브러리를 사용하도록 선택할 수도 있습니다. 사용할 수 있는 MQTT 클라이언트 라이브러리는 다양합니다. IBM은 다음 사이트에서 사용 가능한 라이브러리를 포함하여 여러 클라이언트 라이브러리의 개발 및 지원에 기여합니다.

- [MQTT 커뮤니티 위키](https://github.com/mqtt/mqtt.github.io/wiki)
- [Eclipse Paho 프로젝트](http://eclipse.org/paho/)

## 버전 지원
{: #version-support}

{{site.data.keyword.iot_short_notm}}에서는 다음 버전의 MQTT 메시징 프로토콜을 지원합니다.

MQTT 버전 | 편차 | 참고
--- | --- | ---
[3.1.1](https://www.oasis-open.org/standards#mqttv3.1.1)(권장됨) | 보유된 메시지는 지원되지 않습니다(예: 공유 구독). | <ul><li>OASIS Standard.<li>ISO 표준(ISO/IEC PRF 20922) <li>V3.1과 비교하여 정의가 더 정확하므로 여러 클라이언트와 서버 간 상호 운용성이 향상됩니다. <li>MQTT 클라이언트 ID(ClientId)의 최대 길이는 V3.1에서 부여한 23자 한계에서 256자로 증가됩니다. </br>{{site.data.keyword.iot_short_notm}} 서비스에는 종종 더 긴 클라이언트 ID(ClientID)가 필요합니다. </br>긴 클라이언트 ID는 MQTT 프로토콜 버전과 상관없이 지원되지만, 일부 V3.1 클라이언트 라이브러리에서는 ClientId 값의 길이를 확인하고 23자 한계를 적용합니다.</ul>
3.1 | - | MQTT V3.1은 현재 가장 광범위하게 사용되는 프로토콜 버전입니다.

{{site.data.keyword.iot_short_notm}}에서는 MQTT 표준에서 허용하는 모든 컨텐츠를 지원합니다. MQTT에서는 모든 데이터를 사용할 수 있으므로 이미지, 임의 인코딩 체계로 된 텍스트, 암호화된 데이터 및 2진 형식의 거의 모든 데이터 유형을 보낼 수 있습니다. MQTT 표준에 대한 자세한 정보는 다음 자원을 참조하십시오.
- [MQTT.org](http://mqtt.org/)
- [HiveMQ: MQTT 소개](http://www.hivemq.com/blog/mqtt-essentials-part-1-introducing-mqtt)

## 애플리케이션, 디바이스 및 게이트웨이 클라이언트
{: #device-app-clients}

{{site.data.keyword.iot_short_notm}}에서 기본 클래스는 디바이스와 애플리케이션입니다. 게이트웨이는 디바이스의 서브클래스입니다.

MQTT 클라이언트에서 자신의 클래스로 서비스에 표시하는 클래스에 따라 클라이언트 연결 시 해당 기능이 결정됩니다. 이 클래스에 따라 클라이언트 인증 메커니즘도 결정됩니다.

애플리케이션과 디바이스는 다양한 MQTT 주제 공간에 대해서도 작업합니다. 디바이스는 디바이스 범위 주제 공간 내에서 작업하지만 애플리케이션은 전체 조직에 대한 주제 공간에 대한 전체 액세스를 가집니다. 자세한 정보는 다음 주제를 참조하십시오.

- [디바이스](../../devices/mqtt.html)
- [애플리케이션](../../applications/mqtt.html)
- [게이트웨이](../../gateways/mqtt.html)

## 서비스 품질(QoS) 레벨
{: #qos-levels}

MQTT 프로토콜에서는 클라이언트와 서버 간에 메시지를 전달하기 위해 세 개의 서비스 품질(QoS)을 제공합니다. 즉 "한 번 이하", "한 번 이상" 및 "정확하게 한 번"입니다.
서비스 품질 레벨을 사용하여 이벤트와 명령을 보낼 수 있지만 사용자의 요구사항에 맞는 서비스 레벨을 신중하게 고려해야 합니다. 서비스 품질(QoS) 레벨 2를 선택하는 것이 레벨 0보다 항상 좋은 것은 아닙니다.

### 한 번 이하(QoS0)

"한 번 이하" 서비스 품질 레벨(QoS0)은 가장 빠른 전송 모드이며 경우에 따라 "실행 후 잊기(fire and forget)"라고 부릅니다. 메시지가 최대 한 번 전달되거나 전혀 전달되지 않습니다. 네트워크를 통한 전달은 수신확인 되지 않으므로 메시지가 저장되지 않습니다. 클라이언트의 연결이 끊어지거나 서버 장애가 발생하는 경우 메시지가 손실될 수 있습니다. 

MQTT 프로토콜을 사용하면 서버가 서비스 품질(QoS) 레벨 0에서 클라이언트에 공개를 전달하지 않아도 됩니다. 서버가 공개를 수신할 때 클라이언트의 연결이 끊기면 서버 구현에 따라 공개가 삭제될 수 있습니다. 

**팁:** 간격에 따라 실시간 데이터를 보낼 때 서비스 품질(QoS) 레벨 0을 사용하십시오. 한 메시지가 사라져도 새로운 데이터를 포함하는 다른 메시지가 잠시 후에 전송되므로 문제가 되지 않습니다. 이 시나리오에서는 더 높은 서비스 품질(QoS)을 사용하여 추가 비용을 들여도 가시적인 이점이 없습니다.

### 한 번 이상(QoS1)

서비스 품질 레벨 1(QoS1)을 사용하면 메시지가 항상 한 번 이상 전달됩니다. 발신인이 수신확인을 받기 전에 장애가 발생하면 메시지를 여러 번 전달할 수 있습니다. 수신인이 메시지를 공개했음을 나타내는 확인을 발신인이 받을 때까지 메시지는 발신인이 로컬로 저장해야 합니다. 메시지를 다시 전송해야 하는 경우 메시지가 저장됩니다. 

### 정확하게 한 번(QoS2)

"정확하게 한 번" 서비스 품질 레벨(QoS2)은 가장 안전하지만 가장 느린 전송 모드입니다. 수신인이 메시지를 공개했음을 나타내는 확인을 발신인이 받을 때까지 메시지는 정확히 한 번만 전송되어야 하며 발신인이 로컬로 저장해야 합니다. 메시지를 다시 전송해야 하는 경우 메시지가 저장됩니다. 서비스 품질 레벨 2를 사용하면 메시지가 중복되지 않도록 레벨 1에 비해 더욱 정교한 응답 확인 방식과 수신확인 시퀀스가 사용됩니다. 

**팁:** 명령을 보낼 때 지정된 명령만 딱 한 번 수행되게 하려면 서비스 품질 레벨 2를 사용하십시오. 이 경우 레벨 2의 추가 오버헤드가 있어도 다른 레벨에 비해 유리합니다.

## 구독 버퍼 및 정리 세션
{: #subscription-buffers-and-clean-session}

디바이스 또는 애플리케이션으로부터의 각각의 구독에는 5000개의 메시지라는 버퍼가 할당됩니다. 버퍼를 사용하면 모든 애플리케이션 또는 디바이스가 처리 중인 라이브 데이터의 속도보다 느려지며 각 구독에 대해 보류 중인 메시지가 최대 5000개인 백로그가 누적됩니다. 버퍼가 가득찬 경우 새 메시지를 수신하면 가장 오래된 메시지를 지웁니다.

MQTT 정리 세션 옵션을 사용하여 구독 버퍼에 액세스하십시오. 정리 세션을 false로 설정하면 구독자가 버퍼에서 메시지를 받습니다. 정리 세션을 true로 설정하면 버퍼를 재설정합니다.

**참고:** 사용된 서비스 품질 설정과 상관없이 구독 버퍼 한계가 적용됩니다. 레벨 1 또는 2에서 전송된 메시지가 구독 메시지 비율을 맞출 수 없는 애플리케이션에는 전달되지 않을 수 있습니다. 

## 메시지 페이로드 제한사항
{: #message-payload}

{{site.data.keyword.iot_short_notm}}에서는 MQTT 표준에서 허용하는 모든 형식으로 메시지 보내기와 받기를 지원합니다. MQTT에서는 모든 데이터를 사용할 수 있으므로 이미지, 임의 인코딩 체계로 된 텍스트, 암호화된 데이터 및 2진 형식의 거의 모든 데이터 유형을 보낼 수 있습니다. 그러나 특정 유스 케이스에는 몇 가지 제한사항이 있습니다.   

{{site.data.keyword.iot_short_notm}}에는 메시지 페이로드의 크기 제한사항도 있습니다.

### 메시지 페이로드 형식 제한사항

메시지 페이로드에는 임의의 유요한 문자열이 포함될 수 있지만, JSON("json"), 텍스트("text") 및 2진("bin") 형식이 다른 형식 유형보다 많이 사용됩니다.

다음 테이블에서는 다른 형식 유형의 메시지 페이로드 제한사항을 간략하게 설명합니다.

페이로드 형식  | 특정 유스 케이스용 가이드라인
--------- | ----------  
JSON | JSON은 {{site.data.keyword.iot_short_notm}}의 표준 형식입니다. 기본 제공 {{site.data.keyword.iot_short_notm}} 대시보드, 보드, 카드 및 분석을 사용하려는 경우 메시지 페이로드 형식이 올바르게 구성된 JSON 텍스트를 준수하는지 확인하십시오.
텍스트 | 유효한 UTF-8 문자 인코딩을 사용합니다.
2진 | 제한사항이 없습니다.


### 최대 메시지 페이로드 크기

**중요:** {{site.data.keyword.iot_short_notm}}에서 최대 페이로드 크기는 131072바이트입니다. 한계보다 페이로드가 큰 메시지는 거부됩니다. 다음 디바이스 메시지 예에 간략하게 설명된 대로 연결 클라이언트도 연결이 끊기며 진단 로그에 메시지가 표시됩니다.

`Closed connection from x.x.x.x. The message size is too large for this endpoint.`
