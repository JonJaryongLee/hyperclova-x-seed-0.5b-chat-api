# 로컬 HyperCLOVA X Chat API 서버 (GPU 없는 임베디드 환경)

이 저장소는 Naver의 HyperCLOVA X SEED 0.5B 모델을 Hugging Face Transformers 를 사용해 라즈베리 파이 등의 임베디드 환경에서 실행하고, OpenAI ChatCompletion API와 유사한 형태로 제공하는 예제 프로젝트입니다.

## 주요 기능

* HyperCLOVA X SEED 0.5B 모델 로드 및 추론
* FastAPI 기반 RESTful 서버로 `POST /v1/chat/completions` 엔드포인트 제공

## 시스템 요구사항

* Raspberry Pi 5+
* Ubuntu 22.04+ (다른 Linux 배포판도 가능)
* WSL
* Python 3.11+

## 모델 사용 전 준비사항

1. 먼저, Hugging Face 에 접속해 사용자 토큰을 발급합니다.
2. HyperCLOVA X SEED 0.5B 리포지토리에 접속해 사용 허가를 받습니다.
3. huggingface-cli 를 사용해 로그인합니다.

## 설치 및 실행

1. 저장소 클론

   ```bash
   git clone https://github.com/JonJaryongLee/hyperclova-x-seed-0.5b-chat-api.git
   cd hyperclova-x-seed-0.5b-chat-api
   ```

2. 가상환경 생성 및 활성화

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. 패키지 설치

   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. 서버 실행

   ```bash
   gunicorn -w 1 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 main:app
   ```

## API 사용법

### ChatCompletion

* **엔드포인트**: `POST /api/v1/chat/completions`

#### 요청 예시

```json
{
  "messages": [
    { "role": "system",  "content": "너는 naver 에서 만든 HyperCLOVA X SEED 0.5B 모델이야." },
    { "role": "user",    "content": "안녕, 너의 이름이 뭐야?" }
  ]
}
```

#### 응답 예시

```json
{
  "reply": "제 이름은 CLOVA X입니다."
}
```

## JavaScript(axios) 예제

```py
# pip install requests
import requests

def chat_with_local_model():
    url = "http://localhost:8000/api/v1/chat/completions"
    payload = {
        "messages": [
            { "role": "system", "content": "너는 naver 에서 만든 HyperCLOVA X SEED 0.5B 모델이야." },
            { "role": "user",   "content": "안녕, 너의 이름이 뭐야?" }
        ]
    }

    try:
        res = requests.post(url, json=payload)
        res.raise_for_status()  # HTTP 에러 시 예외 발생
        data = res.json()
        print("Assistant:", data["reply"])
    except requests.HTTPError as http_err:
        print("API 호출 중 HTTP 에러:", http_err, res.text)
    except Exception as err:
        print("에러 발생:", err)

if __name__ == "__main__":
    chat_with_local_model()

```

## 만든사람
- 제작자: 이자룡  
- 소속: 주식회사 민코딩 웹개발 2팀
- 연락처: jaryong.lee@mincoding.co.kr