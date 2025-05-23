+++
date = '2025-05-23T17:07:50+08:00'
draft = false
title = 'MiniMax API 接口文档'
summary= "This summary is independent of the content."
+++

# MiniMax T2A v2（语音生成）API接口文档

该API支持基于文本到语音的同步生成，单次文本传输最大10000字符。接口本身为无状态接口，即单次调用时，模型所接收到的信息量仅为接口传入内容，不涉及业务逻辑，同时模型也不存储您传入的数据。

该接口支持以下功能：

1. 支持100+系统音色、复刻音色自主选择；

2. 支持音量、语调、语速、输出格式调整；

3. 支持按比例混音功能；

4. 支持固定间隔时间控制；

5. 支持多种音频规格、格式，包括：mp3,pcm,flac,wav。注：wav仅在非流式输出下支持；

6. 支持流式输出。

该接口的适用场景：短句生成、语音聊天、在线社交等

## 模型列表

以下模型具备卓越的跨语言能力，全面支持24种全球广泛使用的语言。 我们致力于打破语言壁垒，构建真正意义上的全球通用人工智能模型。 包含中文 (Chinese)、粤语 (Cantonese)、英语 (English)、西班牙语 (Spanish)、法语 (French)、俄语 (Russian)、德语 (German)、葡萄牙语 (Portuguese)、阿拉伯语 (Arabic)、意大利语 (Italian）、日语 (Japanese)、韩语 (Korean)、印尼语 (Indonesian)、越南语 (Vietnamese)、土耳其语 (Turkish)、荷兰语 (Dutch)、乌克兰语 (Ukrainian)、泰语（Thai）、波兰语（Polish）、罗马尼亚语（Romanian）、希腊语（Greek）、捷克语（Czech）、芬兰语（Finnish）、印地语（Hindi）

| 模型                      | 备注                                                         |
| :------------------------ | :----------------------------------------------------------- |
| speech－02－hd            | 全新的HD模型，拥有更出色的韵律和稳定性，复刻相似度和音质表现突出 |
| speech－02－turbo         | 全新的Turbo模型，拥有更出色的韵律和稳定性，小语种能力加强，性能表现出色 |
| speech－01－hd            | 最新的模型架构，拥有最出色的音质和复刻相似度。               |
| speech－01－turbo         | 最新的模型，拥有出色的效果与时延表现。此模型将不定期迭代优化，以提供更优的体验。 |
| speech－01－240228        | 稳定版本的模型，效果出色。稳定版本适合对可能出现的细微音色变化敏感的场景。 |
| speech－01－turbo－240228 | 稳定版本的模型，时延更低。稳定版本适合对可能出现的细微音色变化敏感的场景。 |



## 官方MCP

github链接：https://github.com/MiniMax-AI/MiniMax-MCP

## HTTP接口

接口地址：https://api.minimax.chat/v1/t2a_v2

### 接口参数说明

#### **请求体（Request）参数**

##### **Authorization** 

string 必填

`HTTP：Bearer Auth`
\-  Security Scheme Type: http
\-  HTTP Authorization Scheme: Bearer
API_key，可在[账户管理>接口密钥](https://platform.minimaxi.com/user-center/basic-information/interface-key)中查看。

##### **Content-Type** 

application/json 必填

Content-Type。

##### **Groupid** 

使用发放的值 必填

用户所属的组。使用发放的值。该值应拼接在调用API的url末尾。

##### **model** 

string 必填

请求的模型版本：`speech-02-hd`、`speech-02-turbo`、`speech-01-hd`、`speech-01-turbo`、`speech-01-240228`、`speech-01-turbo-240228`

##### **text** 

string 必填

待合成的文本，长度限制<10000字符，段落切换用换行符替代。（如需要控制语音中间隔时间，在字间增加<#x#>,x单位为秒，支持0.01-99.99，最多两位小数）。支持自定义文本与文本之间的语音时间间隔，以实现自定义文本语音停顿时间的效果。需要注意的是文本间隔时间需设置在两个可以语音发音的文本之间，且不能设置多个连续的时间间隔。

##### **voice_setting**

参数

```
speed 范围[0.5,2]，默认值为1.0
生成声音的语速，可选，取值越大，语速越快。

vol 范围（0,10]，默认值为1.0
生成声音的音量，可选，取值越大，音量越高。

pitch 范围[-12,12]，默认值为0
生成声音的语调，可选，（0为原音色输出，取值需为整数）。

voice_id string
请求的音色编号。与 timber_weights 二选一“必填”。
支持系统音色(id)以及复刻音色（id）两种类型，其中系统音色（ID）如下：
青涩青年音色：male-qn-qingse
精英青年音色：male-qn-jingying
霸道青年音色：male-qn-badao
青年大学生音色：male-qn-daxuesheng
少女音色：female-shaonv
御姐音色：female-yujie
成熟女性音色：female-chengshu
甜美女性音色：female-tianmei
男性主持人：presenter_male
女性主持人：presenter_female
男性有声书1：audiobook_male_1
男性有声书2：audiobook_male_2
女性有声书1：audiobook_female_1
女性有声书2：audiobook_female_2
青涩青年音色-beta：male-qn-qingse-jingpin
精英青年音色-beta：male-qn-jingying-jingpin
霸道青年音色-beta：male-qn-badao-jingpin
青年大学生音色-beta：male-qn-daxuesheng-jingpin
少女音色-beta：female-shaonv-jingpin
御姐音色-beta：female-yujie-jingpin
成熟女性音色-beta：female-chengshu-jingpin
甜美女性音色-beta：female-tianmei-jingpin
聪明男童：clever_boy
可爱男童：cute_boy
萌萌女童：lovely_girl
卡通猪小琪：cartoon_pig
病娇弟弟：bingjiao_didi
俊朗男友：junlang_nanyou
纯真学弟：chunzhen_xuedi
冷淡学长：lengdan_xiongzhang
霸道少爷：badao_shaoye
甜心小玲：tianxin_xiaoling
俏皮萌妹：qiaopi_mengmei
妩媚御姐：wumei_yujie
嗲嗲学妹：diadia_xuemei
淡雅学姐：danya_xuejie
Santa Claus：Santa_Claus
Grinch：Grinch
Rudolph：Rudolph
Arnold：Arnold
Charming Santa：Charming_Santa
Charming Lady：Charming_Lady
Sweet Girl：Sweet_Girl
Cute Elf：Cute_Elf
Attractive Girl：Attractive_Girl
Serene Woman：Serene_Woman

emotion string
控制合成语音的情绪；
当前支持7种情绪：高兴，悲伤，愤怒，害怕，厌恶，惊讶，中性；
参数范围["happy", "sad", "angry", "fearful", "disgusted", "surprised", "neutral"]
该参数仅对 speech-02-hd，speech-02-turbo，speech-01-turbo，speech-01-hd生效

latex_read bool
控制是否支持朗读latex公式，默认为false。
需注意：
1. 请求中的公式需要在公式的首尾加上$$；
2. 请求中公式若有"\"，需转义成"\\"。
示例：导数的基本公式是$$\\frac{d}{dx}(x^n) = nx^{n-1}$$

english_normalization bool
该参数支持英语文本规范化，可提升数字阅读场景的性能，但会略微增加延迟。如果未提供，则默认值为 false。
```

##### **audio_setting**
参数
```
sample_rate 范围【8000，16000，22050，24000，32000，44100】
生成声音的采样率。可选，默认为32000。

bitrate 范围【32000，64000，128000，256000】
生成声音的比特率。可选，默认值为128000。该参数仅对mp3格式的音频生效。

format string
生成的音频格式。默认mp3，范围[mp3,pcm,flac,wav]。wav仅在非流式输出下支持。

channel int
生成音频的声道数.默认1：单声道，可选：
1：单声道
2：双声道
```

##### **pronunciation_dict**

参数

```
tone list
替换需要特殊标注的文字、符号及对应的注音。
替换发音（调整声调/替换其他字符发音），格式如下：
["燕少飞/(yan4)(shao3)(fei1)","达菲/(da2)(fei1)"，"omg/oh my god"]
声调用数字代替，一声（阴平）为1，二声（阳平）为2，三声（上声）为3，四声（去声）为4），轻声为5。
```

##### **timber_weights** 

与voice_id二选一 必填

参数
```
voice_id string
请求的音色id。须和weight参数同步填写。

weight 范围[1,100]
权重，须与voice_id同步填写。最多支持4种音色混合，取值为整数，单一音色取值占比越高，合成音色越像。
```

##### **stream** 

boolean

是否流式。默认false，即不开启流式。

##### **language_boost** 

string， 默认为null

增强对指定的小语种和方言的识别能力，设置后可以提升在指定小语种/方言场景下的语音表现。如果不明确小语种类型，则可以选择"auto"，模型将自主判断小语种类型。支持以下取值：
'Chinese', 'Chinese,Yue', 'English', 'Arabic', 'Russian', 'Spanish', 'French', 'Portuguese', 'German', 'Turkish', 'Dutch', 'Ukrainian', 'Vietnamese', 'Indonesian', 'Japanese', 'Italian', 'Korean', 'Thai', 'Polish', 'Romanian', 'Greek', 'Czech', 'Finnish', 'Hindi', 'auto'

##### **subtitle_enable** 

bool

控制是否开启字幕服务的开关。默认为false。此参数仅对`speech-01-turbo` `speech-01-hd`有效。

##### **output_format** 

string

控制输出结果形式的参数。可选值为`url` `hex`。默认值为`hex`。该参数仅在非流式场景生效，流式场景仅支持返回hex形式。

##### 请求示例：

请求成功（非流式）

```json
{
    "model":"speech-02-hd",
    "text":"真正的危险不是计算机开始像人一样思考，而是人开始像计算机一样思考。计算机只是可以帮我们处理一些简单事务。",
    "stream":False,
    "language_boost":"auto",
    "output_format":"hex",
    "voice_setting":{
        "voice_id":"male-qn-qingse",
        "speed":1,
        "vol":1,
        "pitch":0,
        "emotion":"happy"
    },
    "audio_setting":{
        "sample_rate":32000,
        "bitrate":128000,
        "format":"mp3"
    }
  }
```



请求成功（流式）

```json
{
    "model":"speech-02-turbo",
    "text":"真正的危险不是计算机开始像人一样思考，而是人开始像计算机一样思考。计算机只是可以帮我们处理一些简单事务。",
    "stream":True,
    "language_boost":"auto",
    "output_format":"hex",
    "voice_setting":{
        "voice_id":"male-qn-qingse",
        "speed":1,
        "vol":1,
        "pitch":0,
    },
    "audio_setting":{
        "sample_rate":32000,
        "bitrate":128000,
        "format":"mp3"
    }
  }
```





#### **返回(Response)参数**

**data** 
object

data可能返回为null，参考示例代码时，注意进行非空判断。

参数
```
audio string
合成后的音频片段，采用hex编码，按照输入定义的格式进行生成（mp3/pcm/flac）。

subtitle_file string
合成的字幕下载链接。音频文件对应的字幕，精确到句（不超过50字），单位为毫秒，格式为json。

status int
当前音频流状态，1表示合成中，2表示合成结束。
```

**trace_id** 
string

本次会话的id。用于在咨询/反馈时帮助定位问题。

extra_info 
object

相关额外信息。

展开参数

```
audio_length 
int64
音频时长，精确到毫秒。

audio_sample_rate 
int64
采样率。

audio_size 
int64
音频大小。单位为字节。

bitrate 
int64
比特率。

audio_format 
string
生成音频文件的格式。取值范围mp3/pcm/flac。

audio_channel 
int64
生成音频声道数。1：单声道，2：双声道。

invisible_character_ratio 
float64
非法字符占比。非法字符不超过10%（包含10%），音频会正常生成并返回非法字符占比；最大不超过0.1（10%），超过进行报错。

usage_characters 
int64
计费字符数。本次语音生成的计费字符数。
```

**base_resp** 
object

如果请求出错，对应的错误状态码和详情。

参数
```
status_code int64
状态码。1000，未知错误；1001，超时；1002，触发限流；1004，鉴权失败；1039，触发TPM限流；1042，非法字符超过10%；2013，输入格式信息不正常。

status_msg string
状态详情。
```

#### 返回示例:

返回示例（非流式）

```json
{
    "data":{
        "audio":"hex编码的audio",
        "status":2,
    },
     "extra_info":{
        "audio_length":5746,
        "audio_sample_rate":32000,
        "audio_size":100845,
        "audio_bitrate":128000,
        "word_count":300,
        "invisible_character_ratio":0,
        "audio_format":"mp3",
        "usage_characters":630
    },
    "trace_id":"01b8bf9bb7433cc75c18eee6cfa8fe21",
    "base_resp":{
        "status_code":0,
        "status_msg":""
    }
}
```



返回示例（流式）

```json
//结束
{
    "data":{
        "audio":"hex编码的audio_chunk1 + hex编码的audio_chunk2 + hex编码的audio_chunk3",
        "status":2
    },
     "extra_info":{
        "audio_length":5746,
        "audio_sample_rate":32000,
        "audio_size":302535,
        "audio_bitrate":128000,
        "word_count":300,
        "invisible_character_ratio":0,
        "audio_format":"mp3",
        "usage_characters":630
    },
    "trace_id":"01b8bf9bb7433cc75c18eee6cfa8fe21",
    "base_resp":{
        "status_code":0,
        "status_msg":""
    }
}
//返回的第三个chunk
{
    "data":{
        "audio":"hex编码的audio_chunk3",
        "status":1,
    },
    "trace_id":"01b8bf9bb7433cc75c18eee6cfa8fe21",
    "base_resp":{
        "status_code":0,
        "status_msg":""
    }
}
//返回的第二个chunk
{
    "data":{
        "audio":"hex编码的audio_chunk2",
        "status":1,
    },
    "trace_id":"01b8bf9bb7433cc75c18eee6cfa8fe21",
    "base_resp":{
        "status_code":0,
        "status_msg":""
    }
}
//返回的第一个chunk
{
    "data":{
        "audio":"hex编码的audio_chunk1",
        "status":1,
    },
    "trace_id":"01b8bf9bb7433cc75c18eee6cfa8fe21",
    "base_resp":{
        "status_code":0,
        "status_msg":""
    }
}
```



### 接口调用示例(非流式)

curl:

```bash
curl --location 'https://api.minimax.chat/v1/t2a_v2?GroupId=${group_id}' \
--header 'Authorization: Bearer $MiniMax_API_KEY' \
--header 'Content-Type: application/json' \
--data '{
    "model":"speech-02-hd",
    "text":"真正的危险不是计算机开始像人一样思考，而是人开始像计算机一样思考。计算机只是可以帮我们处理一些简单事务。",
    "stream":false,
    "voice_setting":{
        "voice_id":"male-qn-qingse",
        "speed":1,
        "vol":1,
        "pitch":0,
        "emotion":"happy"
    },
    "pronunciation_dict":{
        "tone":["处理/(chu3)(li3)", "危险/dangerous"]
    },
    "audio_setting":{
        "sample_rate":32000,
        "bitrate":128000,
        "format":"mp3",
        "channel":1
    }
  }'
```

Python

```python
import requests
import json

group_id = "请填写您的group_id"
api_key = "请填写您的api_key"

url = f"https://api.minimax.chat/v1/t2a_v2?GroupId={group_id}"

payload = json.dumps({
  "model":"speech-02-hd",
  "text":"真正的危险不是计算机开始像人一样思考，而是人开始像计算机一样思考。计算机只是可以帮我们处理一些简单事务。",
  "stream":False,
  "voice_setting":{
    "voice_id":"male-qn-qingse",
    "speed":1,
    "vol":1,
    "pitch":0,
    "emotion":"happy"
  },
  "pronunciation_dict":{
    "tone":[
        "处理/(chu3)(li3)", "危险/dangerous"
    ]
  },
  "audio_setting":{
    "sample_rate":32000,
    "bitrate":128000,
    "format":"mp3",
    "channel":1
  }
})
headers = {
  'Authorization': f'Bearer {api_key}',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, stream=True, headers=headers, data=payload)
parsed_json = json.loads(response.text)

# 获取audio字段的值
audio_value = bytes.fromhex(parsed_json['data']['audio'])
with open('output.mp3', 'wb') as f:
    f.write(audio_value)

```



### 完整示例（流式）

本示例以流式形式调用本接口，并流式播放；最终将保存完整音频文件。

完整示例

python

```python
# This Python file uses the following encoding: utf-8

import json
import subprocess
import time
from typing import Iterator

import requests

group_id = ''    #请输入您的group_id
api_key = ''    #请输入您的api_key

file_format = 'mp3'  # 支持 mp3/pcm/flac

url = "https://api.minimax.chat/v1/t2a_v2?GroupId=" + group_id
headers = {"Content-Type":"application/json", "Authorization":"Bearer " + api_key}


def build_tts_stream_headers() -> dict:
    headers = {
        'accept': 'application/json, text/plain, */*',
        'content-type': 'application/json',
        'authorization': "Bearer " + api_key,
    }
    return headers


def build_tts_stream_body(text: str) -> dict:
    body = json.dumps({
        "model":"speech-02-turbo",
        "text":"真正的危险不是计算机开始像人一样思考，而是人开始像计算机一样思考。计算机只是可以帮我们处理一些简单事务。",
        "stream":True,
        "voice_setting":{
            "voice_id":"male-qn-qingse",
            "speed":1.0,
            "vol":1.0,
            "pitch":0
        },
        "pronunciation_dict":{
            "tone":[
                "处理/(chu3)(li3)", "危险/dangerous"
            ]
        },
        "audio_setting":{
            "sample_rate":32000,
            "bitrate":128000,
            "format":"mp3",
            "channel":1
        }
    })
    return body


mpv_command = ["mpv", "--no-cache", "--no-terminal", "--", "fd://0"]
mpv_process = subprocess.Popen(
    mpv_command,
    stdin=subprocess.PIPE,
    stdout=subprocess.DEVNULL,
    stderr=subprocess.DEVNULL,
)


def call_tts_stream(text: str) -> Iterator[bytes]:
    tts_url = url
    tts_headers = build_tts_stream_headers()
    tts_body = build_tts_stream_body(text)

    response = requests.request("POST", tts_url, stream=True, headers=tts_headers, data=tts_body)
    for chunk in (response.raw):
        if chunk:
            if chunk[:5] == b'data:':
                data = json.loads(chunk[5:])
                if "data" in data and "extra_info" not in data:
                    if "audio" in data["data"]:
                        audio = data["data"]['audio']
                        yield audio


def audio_play(audio_stream: Iterator[bytes]) -> bytes:
    audio = b""
    for chunk in audio_stream:
        if chunk is not None and chunk != '\n':
            decoded_hex = bytes.fromhex(chunk)
            mpv_process.stdin.write(decoded_hex)  # type: ignore
            mpv_process.stdin.flush()
            audio += decoded_hex

    return audio


audio_chunk_iterator = call_tts_stream('')
audio = audio_play(audio_chunk_iterator)

# 结果保存至文件
timestamp = int(time.time())
file_name = f'output_total_{timestamp}.{file_format}'
with open(file_name, 'wb') as file:
    file.write(audio)
```



## WebSocket接口

接口地址：wss://api.minimax.chat/ws/v1/t2a_v2

### 建立连接

调用WebSocket库函数（具体实现方式因编程语言或库函数而异），将请求头和URL传入以建立WebSocket连接。

#### 请求体（Request）参数

##### **Authorization** 

string必填
API_key，可在[账户管理>接口密钥](https://platform.minimaxi.com/user-center/basic-information/interface-key)中查看。

##### **返回(Response)参数**

**session_id** 
string
表示整个会话的id

##### **event** 

string
表示会话类型，建连成功后会返回"connected_success"

##### **trace_id** 

string
表示会话中单次请求的id

##### **base_resp**

该请求对应的状态码和详情

参数
```
status_codeint64
状态码。"0"代表建连成功。
status_msgstring
状态详情。
```

#### **建立连接示例**

```
{
    "Authorization": "your_api_key", // 鉴权信息
}
```

#### **返回示例（建连成功）**

```
{
    "session_id":"xxxx",
    "event":"connected_success"
    "trace_id":"0303a2882bf18235ae7a809ae0f3cca7",
    "base_resp":{
        "status_code": 0,
        "status_msg":"success"
    }
}
```



### 发送“任务开始”事件

发送“任务开始”事件则正式开始合成任务，当服务端返回的task_started事件时，标志着任务已成功开始。只有在接收到该事件后，才能向服务器发送task_continue事件或task_finish事件

#### 请求体（Request）参数

##### **event** 

string必填

控制发送的指令。当前环节可选值："task_start"

##### **model** 

string必填

请求的模型版本：speech-02-hd、speech-02-turbo、speech-01-hd、speech-01-turbo、speech-01-240228、speech-01-turbo-240228

##### **voice_setting**

参数
```
speed 范围[0.5,2]，默认值为1.0
生成声音的语速，可选，取值越大，语速越快。

vol 范围（0,10]，默认值为1.0
生成声音的音量，可选，取值越大，音量越高。

pitch 范围[-12,12]，默认值为0
生成声音的语调，可选，（0为原音色输出，取值需为整数）。

voice_id string
请求的音色编号。与timber_weights二选一“必填”。
支持系统音色(id)以及复刻音色（id）两种类型，其中系统音色（ID）如下：
青涩青年音色：male-qn-qingse
精英青年音色：male-qn-jingying
霸道青年音色：male-qn-badao
青年大学生音色：male-qn-daxuesheng
少女音色：female-shaonv
御姐音色：female-yujie
成熟女性音色：female-chengshu
甜美女性音色：female-tianmei
男性主持人：presenter_male
女性主持人：presenter_female
男性有声书1：audiobook_male_1
男性有声书2：audiobook_male_2
女性有声书1：audiobook_female_1
女性有声书2：audiobook_female_2
青涩青年音色-beta：male-qn-qingse-jingpin
精英青年音色-beta：male-qn-jingying-jingpin
霸道青年音色-beta：male-qn-badao-jingpin
青年大学生音色-beta：male-qn-daxuesheng-jingpin
少女音色-beta：female-shaonv-jingpin
御姐音色-beta：female-yujie-jingpin
成熟女性音色-beta：female-chengshu-jingpin
甜美女性音色-beta：female-tianmei-jingpin
聪明男童：clever_boy
可爱男童：cute_boy
萌萌女童：lovely_girl
卡通猪小琪：cartoon_pig
病娇弟弟：bingjiao_didi
俊朗男友：junlang_nanyou
纯真学弟：chunzhen_xuedi
冷淡学长：lengdan_xiongzhang
霸道少爷：badao_shaoye
甜心小玲：tianxin_xiaoling
俏皮萌妹：qiaopi_mengmei
妩媚御姐：wumei_yujie
嗲嗲学妹：diadia_xuemei
淡雅学姐：danya_xuejie
Santa Claus：Santa_Claus
Grinch：Grinch
Rudolph：Rudolph
Arnold：Arnold
Charming Santa：Charming_Santa
Charming Lady：Charming_Lady
Sweet Girl：Sweet_Girl
Cute Elf：Cute_Elf
Attractive Girl：Attractive_Girl
Serene Woman：Serene_Woman

emotion string
控制合成语音的情绪；
当前支持7种情绪：高兴，悲伤，愤怒，害怕，厌恶，惊讶，中性；
参数范围["happy", "sad", "angry", "fearful", "disgusted", "surprised", "neutral"]
该参数仅对speech-02-hd，speech-02-turbo，speech-01-turbo，speech-01-hd生效

latex_read bool
控制是否支持朗读latex公式，默认为false。
需注意：
1. 请求中的公式需要在公式的首尾加上$$；
2. 请求中公式若有"\"，需转义成"\\"。
示例：导数的基本公式是$$\\frac{d}{dx}(x^n) = nx^{n-1}$$

english_normalization bool
该参数支持英语文本规范化，可提升数字阅读场景的性能，但会略微增加延迟。如果未提供，则默认值为 false。
```

##### **audio_setting**

参数
```
sample_rate范围【8000，16000，22050，24000，32000，44100】
生成声音的采样率。可选，默认为32000。
bitrate范围【32000，64000，128000，256000】
生成声音的比特率。可选，默认值为128000。该参数仅对mp3格式的音频生效。
formatstring
生成的音频格式。默认mp3，范围[mp3,pcm,flac,wav]。wav仅在非流式输出下支持。
channelint
生成音频的声道数.默认1：单声道，可选：
1：单声道
2：双声道
```



##### **pronunciation_dict**

参数
```
tone list
替换需要特殊标注的文字、符号及对应的注音。
替换发音（调整声调/替换其他字符发音），格式如下：
["燕少飞/(yan4)(shao3)(fei1)","达菲/(da2)(fei1)"，"omg/oh my god"]
声调用数字代替，一声（阴平）为1，二声（阳平）为2，三声（上声）为3，四声（去声）为4），轻声为5。
```



##### **timber_weights** 

与voice_id二选一必填必填

参数

```
voice_id string
请求的音色id。须和weight参数同步填写。

weight 范围[1,100]
权重，须与voice_id同步填写。最多支持4种音色混合，取值为整数，单一音色取值占比越高，合成音色越像。
```



##### **language_boost** 

string，默认为null

增强对指定的小语种和方言的识别能力，设置后可以提升在指定小语种/方言场景下的语音表现。如果不明确小语种类型，则可以选择"auto"，模型将自主判断小语种类型。支持以下取值：
'Chinese', 'Chinese,Yue', 'English', 'Arabic', 'Russian', 'Spanish', 'French', 'Portuguese', 'German', 'Turkish', 'Dutch', 'Ukrainian', 'Vietnamese', 'Indonesian', 'Japanese', 'Italian', 'Korean', 'Thai', 'Polish', 'Romanian', 'Greek', 'Czech', 'Finnish', 'Hindi', 'auto'

##### task-start事件举例 

发送“任务开始”事件

```
{
    "event":"task_start",
    "model":"speech-02-turbo",
    "language_boost":"Chinese",
    "voice_setting":{
        "voice_id":"male-qn-qingse",
        "speed":1,
        "vol":1,
        "pitch":0,
        "emotion":"happy"
    },
    "pronunciation_dict":{
        "tone":["处理/(chu3)(li3)", "危险/dangerous"]
    },
    "audio_setting":{
        "sample_rate":32000,
        "bitrate":128000,
        "format":"mp3",
        "channel":1
    }    
}
```



#### 返回(Response)参数

##### **session_id** 

string
表示整个会话的id

##### **event** 

string
表示会话类型，当前环节成功后会返回"task_started"

##### **trace_id** 

string
本次会话的id。用于在咨询/反馈时帮助定位问题。

##### **base_resp** 

object
如果请求出错，对应的错误状态码和详情。

参数
```
status_code
int64
状态码。0，发送成功；2202，非法事件。

status_msg
string
状态详情。
```



**task-start** 事件
发送“任务开始”事件

#### 返回示例（任务开始）

返回示例（非流式）

```
{
    "session_id":"xxxx",
    "event":"task_started",
    "trace_id":"0303a2882bf18235ae7a809ae0f3cca7",
    "base_resp":{
        "status_code": 0,
        "status_msg":"success"
    }
}
```







### 发送“任务继续”事件

当收到服务端返回的“task_started”事件后，任务正式开始，可通过发送"task_continue"事件发送要合成的文本，支持顺序发送多个“task_continue”事件，当最后一次收到服务端返回超过120s没有发送事件时，websocket连接自动断开。

#### 请求体（Request）参数

##### event

string

控制发送的指令。当前环节可选值："task_continue"

##### text 

string 必填

待合成的文本，长度限制<10000字符，段落切换用换行符替代。（如需要控制语音中间隔时间，在字间增加<#x#>,x单位为秒，支持0.01-99.99，最多两位小数）。支持自定义文本与文本之间的语音时间间隔，以实现自定义文本语音停顿时间的效果。需要注意的是文本间隔时间需设置在两个可以语音发音的文本之间，且不能设置多个连续的时间间隔。

#### 发送“任务继续”事件示例

```
{
       "event": "task_continue",
       "text": "你好！"
}
```



#### 返回(Response)参数

data 

object

data可能返回为null，参考示例代码时，注意进行非空判断。

参数

##### trace_idstring

本次会话的id。用于在咨询/反馈时帮助定位问题。

##### session_id 

string

本次session的id。

##### event

string

表示会话类型，当前环节成功后会返回"task_continued"

##### is_final

bool

该请求返回是否完结。

##### extra_info

object

相关额外信息。

展开参数

##### base_resp

object

如果请求出错，对应的错误状态码和详情。

参数

```
status_code
int64
状态码。1000，未知错误；1001，超时；1002，触发限流；1004，鉴权失败；1039，触发TPM限流；1042，非法字符超过10%；2013，输入格式信息不正常；2201，超时断开连接；2202，非法事件；2203，空文本，跳过；2204，超过字符限制，跳过；2205，请求超限。

status_msg
string
状态详情。
```



#### 返回示例

```
{
    "data": {
        "audio": "xxx",
    },
    "extra_info": {
        "audio_length": 935,
        "audio_sample_rate": 32000,
        "audio_size": 15597,
        "bitrate": 128000,
        "word_count": 1,
        "invisible_character_ratio": 0,
        "usage_characters": 4,
        "audio_format": "mp3",
        "audio_channel": 1
    },
    "session_id": "xxxx",
    "event": "task_continued",
    "is_final":false,
    "trace_id": "0303a2882bf18235ae7a809ae0f3cca7",
    "base_resp": {
        "status_code": 0,
        "status_msg": "success"
    }
}
```







### 发送“任务结束”事件

当发送"task_finish"事件，服务端收到该事件后，会等待当前队列中所有合成任务完成后，关闭WebSocket连接并结束任务。

#### 请求体（Request）参数

##### event 

string

控制发送的指令。当前环节可选值："task_finish"

#### 发送“任务结束”事件示例

```
{
    "event": "task_finish"
 }
```



#### 返回(Response)参数

##### trace_id 

string

本次会话的id。用于在咨询/反馈时帮助定位问题。

##### session_id

string

本次session的id。

##### event

string

表示会话类型，当前环节成功后会返回"task_finished"

##### base_resp

object

如果请求出错，对应的错误状态码和详情。

参数

```
status_code
int64
状态码。0，发送成功；2202，非法事件。

status_msg
string
状态详情。
```



#### 返回示例

```
{
    "session_id": "xxxx",
    "event": "task_finished",
    "trace_id": "0303a2882bf18235ae7a809ae0f3cca7",
    "base_resp": {
        "status_code": 0,
        "status_msg": "success"
    }
}
```





### “任务失败”事件

如果接收到"task_failed"事件，表示任务失败。此时需要关闭WebSocket连接并处理错误。

#### 返回(Response)参数

##### trace_id

string

本次会话的id。用于在咨询/反馈时帮助定位问题。

##### session_id

string

本次session的id。

##### event

string

表示会话类型，任务失败会返回"task_failed"

##### base_resp

object

如果请求出错，对应的错误状态码和详情。

参数

```
status_code
int64
状态码。1000，未知错误；1001，超时；1002，触发限流；1004，鉴权失败；1039，触发TPM限流；1042，非法字符超过10%；2013，输入格式信息不正常； 2201，超时断连。

status_msg
string
状态详情。
```

#### 任务失败返回示例

```
{
    "session_id": "xxxx",
    "event": "task_failed",
    "trace_id": "0303a2882bf18235ae7a809ae0f3cca7",
    "base_resp": {
        "status_code": 1004,
        "status_msg": "XXXXXXX"
    }
}
```





### 完整示例（Websocket）

#### Websocket调用示例

```python
import asyncio
import websockets
import json
import ssl
from pydub import AudioSegment  # 新增音频处理库
from pydub.playback import play
from io import BytesIO

MODULE = "speech-02-hd"
EMOTION = "happy"


async def establish_connection(api_key):
    """建立WebSocket连接"""
    url = "wss://api.minimax.chat/ws/v1/t2a_v2"
    headers = {"Authorization": f"Bearer {api_key}"}

    ssl_context = ssl.create_default_context()
    ssl_context.check_hostname = False
    ssl_context.verify_mode = ssl.CERT_NONE

    try:
        ws = await websockets.connect(url, additional_headers=headers, ssl=ssl_context)
        connected = json.loads(await ws.recv())
        if connected.get("event") == "connected_success":
            print("连接成功")
            return ws
        return None
    except Exception as e:
        print(f"连接失败: {e}")
        return None


async def start_task(websocket, text):
    """发送任务开始请求"""
    start_msg = {
        "event": "task_start",
        "model": MODULE,
        "voice_setting": {
            "voice_id": "male-qn-qingse",
            "speed": 1,
            "vol": 1,
            "pitch": 0,
            "emotion": EMOTION
        },
        "audio_setting": {
            "sample_rate": 32000,
            "bitrate": 128000,
            "format": "mp3",
            "channel": 1
        }
    }
    await websocket.send(json.dumps(start_msg))
    response = json.loads(await websocket.recv())
    return response.get("event") == "task_started"


async def continue_task(websocket, text):
    """发送继续请求并收集音频数据"""
    await websocket.send(json.dumps({
        "event": "task_continue",
        "text": text
    }))

    audio_chunks = []
    chunk_counter = 1  # 新增分块计数器
    while True:
        response = json.loads(await websocket.recv())
        if "data" in response and "audio" in response["data"]:
            audio = response["data"]["audio"]
            # 打印编码信息（前20字符 + 总长度）
            print(f"音频块 #{chunk_counter}")
            print(f"编码长度: {len(audio)} 字节")
            print(f"前20字符: {audio[:20]}...")
            print("-" * 40)

            audio_chunks.append(audio)
            chunk_counter += 1
        if response.get("is_final"):
            break
    return "".join(audio_chunks)


async def close_connection(websocket):
    """关闭连接"""
    if websocket:
        await websocket.send(json.dumps({"event": "task_finish"}))
        await websocket.close()
        print("连接已关闭")

async def main():
    API_KEY = "your_api_key_here"
    TEXT = "这是一个简化版的语音合成示例"

    ws = await establish_connection(API_KEY)
    if not ws:
        return

    try:
        if not await start_task(ws, TEXT[:10]):
            print("任务启动失败")
            return

        hex_audio = await continue_task(ws, TEXT)

        # Hex解码音频数据
        audio_bytes = bytes.fromhex(hex_audio)

        # 保存为MP3文件
        with open("output.mp3", "wb") as f:
            f.write(audio_bytes)
        print("音频已保存为output.mp3")

        # 直接播放音频（需要安装pydub和简单音频依赖）
        audio = AudioSegment.from_file(BytesIO(audio_bytes), format="mp3")
        print("正在播放音频...")
        play(audio)

    finally:
        await close_connection(ws)


if __name__ == "__main__":
    asyncio.run(main())
```



