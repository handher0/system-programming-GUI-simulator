# SIC/XE Control Section Simulator 

Control Section 방식의 SIC/XE Object Program을 가상 메모리에 적재하고, 한 명령어씩 실행하면서 레지스터/메모리/명령어 흐름을 시각적으로 확인할 수 있는 Java 기반 GUI 시뮬레이터입니다.

## 주요 기능
<p align="center">
<img width="1024" height="718" alt="image" src="https://github.com/user-attachments/assets/9972a3ff-445f-48d8-aa09-5644efd21a65" />
</p>

- Object Program(H/D/T/M/E 레코드) 파싱 및 메모리 적재
- Modification Record 기반 재배치/심볼 처리
- Instruction 단위 실행(one step) 및 전체 실행(all)
- 레지스터/현재 명령어/로그/타깃 주소 실시간 표시
- 파일 기반 디바이스 I/O(TD/RD/WD) 시뮬레이션

## 실행 방법
```bash
# 프로젝트 루트에서 컴파일
javac -d out src/*.java

# 실행
java -cp out SP25_simulator.src.VisualSimulator
```
## 실행 예시
<p align="center">1. 초기 화면</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/3a656b03-c84d-4bef-967b-08d97f007ae0" width="500" alt="스크린샷 2"/>
</p>

<p align="center">2. 파일 오픈 시 다이얼로그 창</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/eaa7767d-ee14-48d0-99e0-bf7599c35e36" width="500" alt="스크린샷 3"/>
</p>

<p align="center">3. 로드 결과</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/d6d0cf9f-530d-4d5a-badf-22da84e4755d" width="500" alt="스크린샷 4"/>
</p>

<p align="center">4. 실행 중</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/e5f179e4-2f1e-40c0-af29-54c5bee1459f" width="500" alt="스크린샷 5"/>
</p>

<p align="center">5. 실행 완료</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/6aaf4bc7-78a0-4ef5-ae29-ffc7043c4451" width="500" alt="스크린샷 6"/>
</p>

## 기술 스택
- Java (JDK 8+ 권장)
- Swing GUI
- File I/O (Object program, device 파일, instruction table)

## 적용한 지식/기술
- SIC/XE Object Program 형식 이해(H/D/R/T/M/E)
- Control Section 기반 로더/링커 동작 구현
- Modification Record를 통한 재배치/심볼 해석
- opcode/format(1~4) 판별 및 nixbpe 주소지정 방식
- PC-relative / Base-relative / Indexed addressing
- 가상 메모리/레지스터 모델링
- 이벤트 기반 GUI(Swing) 상태 동기화

## 디렉터리 구조
```
src/
  VisualSimulator.java
  SicSimulator.java
  SicLoader.java
  ResourceManager.java
  InstLuncher.java
  SymbolTable.java
input/
  inst_table.txt
  output_objectcode.txt
device/
  05.device
  F1.device
```

## 클래스 역할
- `src/VisualSimulator.java`
  Swing 기반 GUI. 파일 선택, 버튼 이벤트 처리, 레지스터/로그/명령어 표시 업데이트.

- `src/SicSimulator.java`
  실행 엔진. 메모리에서 명령어 fetch → decode → execute 수행. 로그 기록 및 실행 상태 관리.

- `src/SicLoader.java`
  Object Program 파서/로더. H/D/T/M/E 레코드 파싱, 메모리 적재, M 레코드 재배치, instruction list 구성.

- `src/ResourceManager.java`
  메모리(64KB), 레지스터, 디바이스 입출력 스트림, 심볼 테이블 관리.

- `src/InstLuncher.java`
  각 명령어 수행 로직. nixbpe 및 유효 주소 계산, 레지스터/메모리 갱신, 디바이스 I/O 처리.

- `src/SymbolTable.java`
  심볼 저장/검색/수정.

- `Instruction` (SicSimulator 내부 클래스)
  명령어 테이블 한 줄을 파싱하여 mnemonic/format/opcode/operand 정보를 보유.

- `ModificationRecord` (SicLoader 내부 클래스)
  M 레코드 재배치 정보를 저장.



## 테스트 방법 (수동)
1. 프로그램 실행 후 `open` 버튼으로 `input/output_objectcode.txt` 선택
2. 로딩 후 아래 항목을 확인
   - H/E 레코드 정보 표시
   - 명령어 목록 리스트 출력
   - 레지스터 초기화 상태
3. `실행(1step)`으로 단계별 실행 확인
   - PC 이동, 레지스터 변화, 로그 추가 확인
   - 타깃 주소(Target Address) 표시 확인
4. `실행(all)`로 전체 실행 확인
5. I/O 명령어 테스트
   - `device/05.device`, `device/F1.device` 파일로 TD/RD/WD 동작 확인
   - GUI의 "사용중인 장치" 필드 업데이트 확인

## 참고
- Instruction Table: `input/inst_table.txt`
- Object Program 예시: `input/output_objectcode.txt`
- 디바이스 파일은 `device/*.device`로 사용
