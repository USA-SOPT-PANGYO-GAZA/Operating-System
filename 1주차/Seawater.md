# 운영체제

- 컴퓨터 **하드웨어 바로 위에 설치됨**
- 컴퓨터 하드웨어와 각종 소프트웨어, 사용자를 연결하는 소프트웨어 계층

<img width="369" alt="image" src="https://user-images.githubusercontent.com/68391767/153821202-6ae0b32a-11aa-41d2-a425-0a20cad576d4.png">

- 좁은 의미의 운영체제
    - **(커널)** 운영체제의 핵심 부분, 메모리에 상주하는 부분
- 넓은 의미의 운영체제
    - 커널 뿐 아니라, 각종 주변 시스템 유틸리티를 포함 (ex. 부가적인 프로그램, 유틸리티 등)
    - 메모리에 상주하지는 않지만 나름 포함시키는 부분

# 운영체제의 목적 2가지

1. 하드웨어 자원을 **효율적으로 관리**함
    - 주어진 자원(CPU, 메모리, 각종 입출력장치 등)으로 최대한의 성능을 내도록 하고,
    - 어느 정도는 형평성 있게 자원이 분배되도록 함
2. **사용자가 컴퓨터를 편리하게 사용할 수 있도록** 함

    <img width="671" alt="image" src="https://user-images.githubusercontent.com/68391767/153821246-fc0e8412-854a-472b-80f1-0015ffba9065.png">
    

# 운영체제의 분류

**`분류기준`** 1. 동시작업 가능여부, 2. 사용자의 수, 3. 처리방식

## 1. 동시작업 가능 여부

### 1) 단일 작업 single tasking

*ex) MS-DOS 프롬프트 상에서는 한 명령의 수행을 끝내기 전에 다른 명령을 수행시킬 수 없는 예시*

- 한 번에 하나의 작업만 처리 → 프로그램 하나 돌리면 다른 프로그램 사용 불가능
- 옛날의 운영체제

### 2) 다중 작업 multi tasking

*ex) UNIX, MS Windows 등에서는 한 명령의 수행이 끝나기 전에 다른 명령이나 프로그램을 수행할 수 있는 예시*

- 동시에 두 개 이상의 작업 처리함
- 현재의 운영체제

## 2. 사용자의 수

컴퓨터 한대가 여러 사용자에 접속할 수 있는지

### 1) 단일 사용자 single user

*ex) MS-DOS, MS Windows*

### 2) 다중 사용자 multi user

*ex) UNIX, NT server*

## 3. 처리방식에 따른 분류

### 1) 일괄처리 batch processing

- 어떤 작업이 주어졌을 때, 바로 처리하는게 아니라 작업을 일정량 모아서 한꺼번에 처리하는 방식
- 작업이 완전 종료될때까지 기다려야 한다.
- 현대의 운영체제에서 찾아보기 힘들다
- interactive 하지 않다. (ex. 키보드에 입력하면 다 모아서 1분뒤에 뜨는)

### 2) 시분할방식 time sharing

- 여러 작업을 수행할 때, 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용하는 방식
- 짧은 응답시간을 가진다. (ex. 내가 키보드에 두드리면 바로 화면에 나오는)
- interactive하다
- 정확한 시간을 지켜준다고는 말할 수 없다.

### 3) 실시간 Realtime OS

- deadline이 있고, 정해진 시간안에 반드시 결과가 나오도록 보장되는 방식
- 특수한 목적을 가진 시스템에서 반드시 실행되도록 하는 운영체제 (ex. 미사일) - Hard realtime system
- 최근에는 더 완화된 개념으로도 사용되고 있다. - Soft realtime System
    
    ex) 영화 보다 끊기는..
    

# 운영체제의 예시

## 유닉스 Unix

- C언어로 작성 (Unix를 만들기 위해 C언어를 새로 만듬)
- 높은 이식성
- 최소한의 커널 구조
- 소스코드가 공개되어있다
- 프로그램 개발에 용이
- 다양한 버전
    - System V, FreeBSD, SunOS, Solaris
    - Linux

## DOS Disk Operating System

- MS사에서 1981년 IBM-PC를 위해 개발
- 단일 사용자용 운영체제
- 640KB의 메모리

## MS Windows

- MS사의 다중 작업용 GUI 기반의 운영 체제
- 초창기 Window는 불안정했는데 이젠 불안정하지 않은듯함
- 풍부한 지원 소프트웨어

## Handheld device를 위한 OS

PalmOS, Pocker PC, Tiny OS

# 운영체제

<img width="366" alt="image" src="https://user-images.githubusercontent.com/68391767/153821283-5c48ddd5-574f-463c-8f47-d426b2e6f4f0.png">

### CPU

- CPU 스케줄링
- 어떤 프로그램에게 CPU를 할당할지 결정
- 굉장히 빠른 장치이다.

### Memory

- 어떤 프로그램에게 메모리를 얼마나 할당할지, 한정된 메모리를 어떻게 쪼개 쓸지 결정

### Disk

- 디스크에 파일을 어떻게 보관할지
- 파일을 쪼개서 저장할지, 한꺼번에 저장할지

→ 디스크 스케줄링

### IO 디바이스

- 속도 느림
- 각기 다른 입출력장치와 컴퓨터 간 어떻게 정보를 주고받게 할지 결정 (interrupt 기반)
