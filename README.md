# 👾 STM32 Mini Pac-Man Game (2025.08.05 ~ 2025.08.07) [2인]

> **STM32 Nucleo-F411RE**에서 **조이스틱(ADC+DMA)**, **타이머(IRQ)**, **I2C LCD 1602**를 이용해  
> **팩맨 이동/충돌/레벨업/클리어**를 구현한 미니 게임 프로젝트

---

## ✅ Key Features
- 🎮 **Input**: 조이스틱 X/Y를 **ADC1 IN0/IN1(Scan + DMA)** 로 읽어 방향 결정
- 🖥️ **UI(Output)**: **I2C LCD 1602**에 캐릭터/적/아이템/상태 표시
- 🧠 **Game Logic**
  - `Move_Pacman()` : 조이스틱 방향 기반 이동 + 벽/맵 체크 + 위치 갱신
  - `LCD_Display_Charactor()`, `LCD_Display_Enemy()` : 좌표 기반 화면 출력
  - `GameStatus()` : Dot(또는 past_position) 카운트 조건 만족 시 **Clear/LevelUp**
  - `LevelupInit()` : 레벨 전환 시 맵/좌표/상태 초기화
- ⏱️ **Periodic Task**: `HAL_TIM_PeriodElapsedCallback()`에서 게임 주기 작업 처리

---

## 🔌 Hardware

<p align="center">
  <img src="팩맨프로젝트 이미지/capture_260115_070755.png" width="700" alt="hardware">
</p>

### 📍 Pin Map (Arduino 배선 기준)
| 모듈 | 핀(모듈) | Arduino UNO 핀 | 설명 |
|---|---|---|---|
| Joystick | VCC | 5V | 전원 |
| Joystick | GND | GND | 접지 |
| Joystick | VRx (X) | A0 | X축 아날로그 입력 |
| Joystick | VRy (Y) | A1 | Y축 아날로그 입력 |
| Joystick | SW | D6 | 버튼 입력 |
| I2C LCD 1602 | VCC | 5V | 전원 |
| I2C LCD 1602 | GND | GND | 접지 |
| I2C LCD 1602 | SDA | A4 (D14) | I2C 데이터 |
| I2C LCD 1602 | SCL | A5 (D15) | I2C 클럭 |

---

## 🧩 Software Architecture

<p align="center">
  <img src="팩맨프로젝트 이미지/capture_260115_070945.png" width="900" alt="software">
</p>

### 📍 Flowchart
<p align="center">
  <img src="팩맨프로젝트 이미지/Mermaid_Chart_-_Create_complex_visual_diagrams_with_text._A_smarter_way_of_creating_diagrams.-2025-08-07-055350.svg" width="320" alt="flowchart">
</p>

---

## 🧠 ADC + DMA 구조 (Block Diagram)

<p align="center">
  <img src="팩맨프로젝트 이미지/image.webp" width="800" alt="ADC DMA Block Diagram">
</p>

**요약:** ADC가 변환한 샘플 데이터를 **DMA가 CPU 개입 없이 Memory로 자동 전송**하고,  
전송 완료(Half/Full) 시점에만 **Interrupt**로 CPU에 알려 **부하를 크게 줄이는 구조**입니다.

### 동작 흐름
1) **ADC**가 주기적으로 아날로그 값을 변환  
2) **DMA**가 ADC 데이터 레지스터 → **Memory(Buffer)** 로 전송  
3) Buffer가 **Half/Full** 채워지면 DMA가 **Interrupt** 발생  
4) **CPU**는 Interrupt에서 필요한 처리(필터링/평균/방향결정/UI 등)만 수행

### 장점
- ✅ CPU 폴링 제거 → **낮은 CPU 사용률**
- ✅ 일정 샘플링 유지 → **안정적인 데이터 수집**
- ✅ 필요한 순간만 인터럽트 → **실시간성 향상**

---

## ⚙️ STM32CubeMX 설정 요약 (ADC / DMA / GPIO / TIM2)

> 아래 내용은 길어지기 쉬우니 **접기(Details)** 로 넣으면 README가 훨씬 깔끔해져요.

<details>
<summary><b>📌 ADC 설정 (Joystick: IN0/IN1)</b></summary>

- **Scan Conversion Mode = Enabled**  
  → 여러 채널을 순차 자동 측정 (**IN0(x축), IN1(y축)** 두 채널을 순서대로 변환)

- **Continuous Conversion Mode = Enabled**  
  → Start 후 계속 반복 측정 (팩맨 방향/이동을 위해 지속적으로 값 필요)

- **Discontinuous Requests = Disabled**  
  → 채널을 끊어서 1개씩 트리거하는 방식 비활성  
  (Enabled이면 IN0 변환 후 다시 트리거 줘야 IN1 변환 → 반응성 떨어짐)

- **DMA Continuous Requests = Enabled**  
  → ADC 변환마다 DMA 요청 자동 발생  
  (ADC → DMA → Memory로 X/Y 값이 자동 저장)

📷 설정 화면
<p align="center">
  <img src="팩맨프로젝트 이미지/capture_250806_115859.webp" width="500" alt="adc setting">
</p>

</details>

<details>
<summary><b>📌 DMA 설정 (ADC1 → DMA2 Stream0)</b></summary>

- DMA를 사용하지 않으면  
  → ADC 변환 완료 때마다 **Interrupt + CPU가 직접 값 읽기** 필요

- DMA를 사용하면  
  → ADC가 변환한 값이 **자동으로 메모리에 저장**되어 CPU 부하 감소

📷 설정 화면
<p align="center">
  <img src="팩맨프로젝트 이미지/capture_250806_112524.webp" width="500" alt="dma setting">
</p>

</details>

<details>
<summary><b>📌 GPIO 설정</b></summary>

- LCD / Joystick / Button 핀을 기능에 맞게 설정

📷 설정 화면
<p align="center">
  <img src="팩맨프로젝트 이미지/GPIO_Setting.webp" width="500" alt="gpio setting">
</p>

</details>

<details>
<summary><b>📌 TIM2 설정 (게임 주기/IRQ)</b></summary>

- 타이머 인터럽트 기반으로 게임 주기 로직 실행  
  → `HAL_TIM_PeriodElapsedCallback()`에서 이동/충돌/상태 업데이트 등 처리

📷 설정 화면
<p align="center">
  <img src="팩맨프로젝트 이미지/capture_250806_121320.webp" width="500" alt="tim2 setting">
</p>

</details>
