# STM32 Mini Pac-Man Game (2025.08.05 ~ 2025.08.07) [2인]

## 프로젝트 내용

**STM32(Nucleo-F411RE)에서 조이스틱(ADC)·타이머(IRQ)·I2C LCD를 이용해 팩맨(이동/충돌/레벨업/클리어)을 구현한 미니 게임 프로젝트.**

📌**입력**: 조이스틱 X/Y를 ADC1 IN0/IN1로 읽어서 방향 결정

📌**출력(UI)**: I2C LCD 1602에 게임 화면(캐릭터/적/아이템/상태)을 표시 (I2C_lcd.c/.h 사용)

📌**게임 로직**

-Move_Pacman() : 조이스틱 방향에 따라 팩맨 이동 + 벽/맵 체크 + 위치 업데이트

-LCD_Display_Charactor(), LCD_Display_Enemy() : LCD에 팩맨/적을 좌표 기반으로 그림

-GameStatus() : 먹은 위치(past_position) 카운트가 목표에 도달하면 클리어/레벨업 처리

-LevelupInit() : 레벨 전환 시 맵/좌표/상태 초기화

📌**주기 처리(타이머)**: HAL_TIM_PeriodElapsedCallback()에서 게임 진행에 필요한 주기 작업을 처리하는 형태


## SOFTWARE 

1️⃣**초기화 단계 (부팅 직후)**
-HAL/Clock 초기화

-GPIO / ADC / I2C / UART / TIM 설정

-.ioc 기준: ADC1(IN0, IN1) / I2C1(PB8, PB9) / USART2 / TIM2 / TIM3(PWM: PA6)

-LCD 초기화 (I2C_lcd), 커스텀 캐릭터(팩맨/적 등) 등록

-게임 변수 초기화 (레벨, 현재 좌표, past_position 등)

2️⃣**메인루프(While(1))**
-ADC로 조이스틱 읽기 → 방향 결정

-Move_Pacman() 호출로 좌표 업데이트

-LCD에 현재 상태 렌더링

-LCD_Display_Charactor() (팩맨 표시)

-LCD_Display_Enemy() (적 표시)

-GameStatus()로 충돌/클리어/레벨업 조건 검사
