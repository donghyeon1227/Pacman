# STM32 Mini Pac-Man Game (2025.08.05 ~ 2025.08.07) [2인]

 **[프로젝트 설명]**

  STM32 마이크로컨트롤러(F429ZI)를 활용해 LCD에 출력되는 미니 팩맨 게임을 구현한 임베디드 프로젝트입니다.
게임 로직, 조이스틱 입력, 타이머 이벤트, 부저 사운드 등을 포함한 작은 규모의 게임 시스템을 STM32로 구성했습니다. 

**[팀원 소개]**

| 이름 | 담당 |
|------|------|
|김다현|게임 로직 구현, 하드웨어 제어 & 디바이스 인터페이싱|
|김동현|게임 로직 구현, ADC 기반 조이스틱 입력 처리, PPT제작 및 발표|
 
 **[사용한 기술]**

   -`STM32CubleIDE`

 **[시스템 회로도]**
<img width="640" height="460" alt="{8633471F-6860-4878-95A1-ABBB25E3D4B5}" src="https://github.com/user-attachments/assets/9521ccba-b00e-4dee-8502-3fb79e61444b" />

 **[게임 로직 구조]**  
  1️⃣ 게임 상태: ING(게임 진행 중), WIN(승리), OVER(실패)

  2️⃣ LCD 출력 처리: I2C 기반의 16*2 문자 LCD 사용, 팩만 & 적 캐릭터 커스텀 문자를 등록하여 그래픽처럼 표시
(LCD Custom Character Generator 사이트 활용)

  3️⃣ 타이머 & PWM: TIM2 적 움직임 제어용, TIM3 부저 PWM 사운드 제어
   
 **[시연 영상]**  
  <a href="https://youtube.com/shorts/oa5NfzWY2-4?si=lXIWHLyZj8PD6Caf">
  <img src="https://img.shields.io/badge/YouTube-Video-FF0000?style=flat-square&logo=youtube&logoColor=white">
  </a>
