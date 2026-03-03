# STM32G474 (CubeIDE) FW Template

## 내용 설명
1) **Internal ADC 온도(TempSensor) 모니터링 → 일정값 이상 셧다운**
   - ADC DMA 시퀀스에 `TEMP` + `VREFINT` 포함(권장)
   - `service/temp_monitor.*`에서 온도 계산 및 Protection 연동

2) **모터 2중 PID**
   - Outer(Position PID) → speed_setpoint
   - Inner(Speed PID) → current_setpoint
   - (추가) Current PI → PWM duty (전류제어 = current source)

3) **가감속/위치보정/인코더 피드백**
   - `app/motion_profile.*` : accel/decel 제한 setpoint 생성
   - `service/encoder.*` + `driver/encoder_drv.*` : TIM Encoder mode 기반 위치/속도 추정

4) **카메라 One Pulse 모드 정확한 snapshot**
   - `driver/camera_trigger_drv.*` : TIM One-Pulse로 펄스 발생 (delay + width)
   - `service/camera.*` : 명령으로 snapshot 트리거

5) **current source 전류제어 코드**
   - ADC current sense → Current PI → PWM
   - Over-current/over-temp/timeout 등 Protection과 연동

## 폴더 구조
- include/: 공통 헤더
- src/driver/: STM32 HAL/LL 호출부(ADC DMA, UART, PWM, Encoder, OnePulse)
- src/hal/: HAL Interface(추상화)
- src/service/: Parser/Protection/Sensor/Temp/Encoder/Camera/Telemetry
- src/app/: FSM/Scheduler/PID(dual)/MotionProfile/Control
- src/platform/: STM32G4 CubeIDE 연결 스켈레톤 + PC Sim

## PC 테스트
- `tools/pc_send_cmd.py` : 프레임(AA55/LEN/CMD/PAYLOAD/CRC16) 생성 및 (옵션) pyserial로 송신
