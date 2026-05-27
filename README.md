# Raspberry Pi Web Server using Flask to Control GPIOs

> 작업 일시: 2026년 5월 24일 14:00 ~ 21:00

---


## 1. 조립하기

홈페이지를 참고하여 회로를 조립하였습니다.

---

## 2. OS 업데이트

```bash
sudo apt update
sudo apt upgrade -y
```

![OS 업데이트 하는 모습](KakaoTalk_20260527_045829194.jpg)

---

## 3. 가상환경 생성 및 Flask 설치

바로 Flask 설치하려 하였으나 에러가 발생하여, 가상환경을 생성한 후 그 안에 Flask를 설치하였습니다.

```bash
sudo apt install python3-venv -y
sudo apt install python3-rpi-lgpio -y

mkdir web-server
cd web-server

python3 -m venv venv
source venv/bin/activate

pip install flask
```

![가상환경 생성 후 Flask 설치하는 모습](KakaoTalk_20260527_052112334.jpg)

---

## 4. app.py 만들기

```bash
nano app.py
```

```python
from flask import Flask
import RPi.GPIO as GPIO

app = Flask(__name__)

GPIO.setmode(GPIO.BCM)

LED = 23

GPIO.setup(LED, GPIO.OUT)

@app.route('/')
def home():
    return '''
    <h1>Raspberry Pi GPIO Control</h1>

    <a href="/on">
        <button style="width:100px;height:50px;">ON</button>
    </a>

    <a href="/off">
        <button style="width:100px;height:50px;">OFF</button>
    </a>
    '''

@app.route('/on')
def on():
    GPIO.output(LED, GPIO.HIGH)
    return '<h1>LED ON</h1><a href="/">Back</a>'

@app.route('/off')
def off():
    GPIO.output(LED, GPIO.LOW)
    return '<h1>LED OFF</h1><a href="/">Back</a>'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

![나노 편집기에서 코드를 저장한 모습](KakaoTalk_20260527_060231152.jpg)

---

## 5. templates 폴더 및 HTML 파일 만들기

```bash
mkdir templates
cd templates
nano main.html
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>Raspberry Pi GPIO Control</title>
</head>

<body>

<h1>GPIO Control</h1>

{% for pin in pins %}

<h2>{{ pins[pin].name }}

{% if pins[pin].state == true %}
    is currently ON
</h2>

<a href="/{{pin}}/off">
    <button>Turn OFF</button>
</a>

{% else %}
    is currently OFF
</h2>

<a href="/{{pin}}/on">
    <button>Turn ON</button>
</a>

{% endif %}

{% endfor %}

</body>
</html>
```

![나노 편집기에서 코드를 저장한 모습](KakaoTalk_20260527_060244962.jpg)

---

## 6. 서버 실행 및 브라우저 접속

```bash
cd ~/web-server
source venv/bin/activate
python app.py
```

- `hostname -I` 명령어로 IP 주소 확인: `172.30.1.16`
- 브라우저에서 접속: `http://172.30.1.16:5000`

![브라우저 접속 화면](KakaoTalk_20260527_060314789.jpg)

---

## 7. 실행 결과

브라우저에서 버튼을 클릭하여 LED를 켜고 끄는 동작을 확인하였습니다.

> 📹 시연 영상

![브라우저로 LED 키고 끄는 모습](KakaoTalk_20260527_061336861.gif)
