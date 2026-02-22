# CFD Convergence Watch 사용자 매뉴얼

**버전**: v0.3.2

---

## 1. 소개

CFD Convergence Watch는 CFD(전산유체역학) 해석 시 모니터링 포인트에 대한 수렴 이력(convergence history)을 시각화하고 분석하는 도구입니다.

다양한 CFD solver의 출력 파일을 로드하여 수렴 추이를 차트로 확인하고, 이동평균(SMA) 비교를 통해 수렴 여부를 판단할 수 있습니다.

### 지원 solver

| Solver | 유형 | 파일 형식 |
|--------|------|-----------|
| Generic CSV | - | `.csv` |
| Ansys CFX | Steady | `.csv` |
| FlexCompute Flow360 | Steady / Transient | `.csv` |
| Ansys Fluent | Steady / Transient | `.out` |
| SU2 | Steady | `.csv` |

### 파일 형식 요약

**Generic CSV** — 쉼표 구분. 첫 컬럼이 X축.
```
timestep, var1, var2
1, 0.001, 0.002
2, 0.001, 0.002
```

**Ansys CFX** — 쉼표 구분. 따옴표 헤더 (내부 쉼표 포함 가능). `cfx5mondata`로 추출.
```
"ACCUMULATED TIMESTEP","USER POINT,Mass Flow Monitor","USER POINT,Pressure Ratio"
1,-4.53497200e+01,2.13941860e+00
```

**Flow360 Steady** — 쉼표 구분. `physical_step`(첫 컬럼)은 무시, `pseudo_step`(둘째 컬럼)이 X축.
```
physical_step, pseudo_step, CL, CD, ...
0, 0, -1.480, 12.750, ...
0, 10, -4.620, 42.375, ...
```

**Flow360 Transient** — 쉼표 구분. `physical_step`(첫 컬럼)이 X축. 동일 step의 마지막 행만 사용.
```
physical_step, pseudo_step, CL, CD, ...
0, 0, -2.418, 2.240, ...
0, 24, -2.418, 2.240, ...
1, 0, -2.418, 2.240, ...
```

**Ansys Fluent** — 공백 구분 `.out` 파일. `(`로 시작하는 행이 헤더 (그 위는 주석).
```
"force-and-mom-file-0"
"Iteration" "fx-1-stabaxis etc.."
("Iteration" "fx-1-stabaxis" "fz-1-stabaxis" "my-1")
1 175342.7 220725.8 18475.2
```

**SU2** — 쉼표 구분. `Time_Iter`, `Outer_Iter`(앞 2컬럼)는 무시, `Inner_Iter`(셋째 컬럼)가 X축.
```
"Time_Iter","Outer_Iter","Inner_Iter","rms[P]","rms[U]"
0,0,0,-4.953,-16.084
0,0,1,-5.073,-4.883
```

---

## 2. 화면 구성

![CFD Convergence Watch 메인 화면](screenshot_v030.jpg)

프로그램은 3개 패널과 상태바로 구성됩니다.

```
+----------+----------+-------------------------------+
| File     | Variable | Chart                         |
| Panel    | Panel    |                               |
|          |          |  SMA 컨트롤                    |
| 파일목록  | 변수체크  |  ImPlot 차트                   |
|          |          |  (수렴 오버레이 포함)            |
|          |          +-------------------------------+
|          |          | Statistics Table              |
|          |          | (통계 + 클립보드 복사)           |
+----------+----------+-------------------------------+
| Status Bar: Solver: Flow360 Steady | Path: ...      |
+-----------------------------------------------------+
```

- **File Panel** (왼쪽): 로드된 파일 목록. 클릭으로 파일 선택.
- **Variable Panel** (중앙): 선택된 파일의 변수 체크박스. All/None 버튼으로 일괄 선택/해제.
- **Chart Panel** (오른쪽): 차트, 수렴 오버레이, SMA 컨트롤, 통계 테이블.
- **Status Bar** (하단): 현재 선택된 solver와 폴더 경로 표시.

패널 사이의 구분선을 드래그하여 패널 너비를 조절할 수 있습니다.

---

## 3. 파일 로드

### 폴더 열기

**File > Open Folder** 메뉴를 선택하면 폴더 선택 대화상자가 열립니다. 선택된 폴더 내의 모든 지원 파일(`.csv`, `.out`)을 자동으로 로드합니다.

> 동일한 확장자를 가진 동일한 파일이 있는 경우 오류가 발생할 수 있습니다.

### 파일 열기

**File > Open Files** 또는 File Panel의 **Add Files** 버튼을 클릭하면 파일 선택 대화상자가 열립니다. 여러 파일을 동시에 선택할 수 있습니다.

- 지원 필터: All Supported (`.csv`, `.out`), CSV Files (`.csv`), Fluent Output (`.out`)
- 동일 파일은 중복 로드되지 않습니다.
- 파일은 이름순으로 자동 정렬됩니다.

### 파일 전체 삭제

**File > Clear All** 메뉴를 선택하면 로드된 모든 파일을 제거합니다.

---

## 4. CFD Solver 선택

**Settings > CFD Solver** 메뉴에서 데이터에 맞는 solver를 선택합니다.

| Solver | 설명 |
|--------|------|
| Generic CSV | 범용 CSV. 첫 컬럼이 X축 |
| CFX Steady | Ansys CFX 수렴 이력. 따옴표 헤더 지원 |
| Flow360 Steady | physical_step 제거, pseudo_step이 X축 |
| Flow360 Transient | physical_step별 그룹의 마지막 행만 보존 |
| Fluent Steady | `.out` 파일, Iteration이 X축 |
| Fluent Transient | `.out` 파일, Time Step이 X축 |
| SU2 Steady | Time_Iter, Outer_Iter 제거, Inner_Iter가 X축 |

Solver를 변경하면 이미 로드된 모든 파일이 새 solver 설정으로 자동 재로드됩니다.

### 범례 위치

**Settings > Legend Position** 메뉴에서 차트 범례의 위치를 변경할 수 있습니다.

| 옵션 | 설명 |
|------|------|
| Inside (Top-Left) | 차트 안쪽 좌측 상단 (기본값) |
| Outside (Right) | 차트 바깥 오른쪽 |
| Outside (Top) | 차트 바깥 상단 |
| Outside (Bottom) | 차트 바깥 하단 |

### CFX Install Path

**Settings > CFX Install Path** 메뉴를 선택하면 CFX bin 폴더 경로를 설정하는 모달이 표시됩니다.

- 텍스트 입력란에 현재 경로가 표시되며, 직접 수정하거나 **Browse** 버튼으로 폴더를 선택할 수 있습니다.
- 경로는 `cfx5mondata.exe`가 있는 폴더를 지정해야 합니다 (예: `C:\Program Files\ANSYS Inc\v242\CFX\bin`).
- Tools > Extract from CFX 기능 사용 시 필요합니다.

### X축 경고

데이터 로드 시 X축 값이 일정 간격(sequential)이 아닌 경우 경고 모달이 표시됩니다. 이는 데이터에 맞지 않는 solver를 선택했을 가능성을 나타냅니다. **Settings > CFD Solver**에서 올바른 solver를 선택해 주세요.

---

## 5. 차트

### 변수 선택

Variable Panel에서 표시할 변수를 체크합니다.

- **All**: 모든 변수 선택
- **None**: 모든 변수 해제

체크된 변수만 차트와 통계 테이블에 표시됩니다.

### SMA (Simple Moving Average)

차트 상단의 SMA 컨트롤로 이동평균을 설정합니다.

- **SMA-1 / SMA-2**: 체크박스로 각각 활성화/비활성화
- 옆의 숫자 입력으로 윈도우 크기 설정 (최소 2, 최대 = 데이터 행 수)
- SMA 라인은 원본 데이터 위에 굵은 선으로 표시됩니다

### 수렴 오버레이

차트 우측 상단에 각 변수의 수렴 지표가 표시됩니다.

```
R-S1:0.05%  R-S2:0.10%  S1-S2:0.03% OK
```

| 지표 | 의미 |
|------|------|
| R-S1 | Raw 최종값과 SMA-1 최종값의 상대 차이(%) |
| R-S2 | Raw 최종값과 SMA-2 최종값의 상대 차이(%) |
| S1-S2 | SMA-1 최종값과 SMA-2 최종값의 상대 차이(%) |

수렴 판정 기호:

| 기호 | 조건 | 색상 |
|------|------|------|
| OK | S1-S2 < 0.1% | 녹색 |
| ~ | S1-S2 < 1.0% | 노란색 |
| X | S1-S2 >= 1.0% | 빨간색 |

### 차트 조작

ImPlot 차트는 마우스로 조작할 수 있습니다.

- **드래그**: 차트 영역 이동 (pan)
- **스크롤**: 줌 인/아웃
- **더블 클릭**: 차트 범위 초기화 (fit)
- **우클릭**: 차트 옵션 메뉴

---

## 6. 통계 테이블

차트 아래에 선택된 변수의 통계가 표 형태로 표시됩니다.

| 컬럼 | 설명 |
|------|------|
| Variable | 변수명 |
| Min | 최솟값 |
| Max | 최댓값 |
| Final | 마지막 값 |
| SMA-1 (N) | SMA-1 최종값 (윈도우 크기 N) |
| SMA-2 (N) | SMA-2 최종값 (윈도우 크기 N) |
| S1-S2(%) | SMA-1과 SMA-2의 상대 차이(%) |

### 클립보드 복사

**Copy** 버튼을 클릭하면 통계 데이터가 탭 구분 형식으로 클립보드에 복사됩니다. Excel 등 스프레드시트에 바로 붙여넣기할 수 있습니다.

Copy 버튼 옆의 체크박스로 복사할 컬럼을 선택할 수 있습니다:

- **Min** / **Max** / **Final** / **S1** / **S2** / **Diff**

---

## 7. Tools

### Extract from CFX

**Tools > Extract from CFX** 메뉴를 선택하면 CFX 결과 파일(`.res`)에서 USER POINT 모니터링 데이터를 CSV로 추출합니다.

1. Settings에서 CFX Install Path가 설정되어 있어야 합니다.
2. `.res` 파일을 선택합니다.
3. 추출이 백그라운드에서 실행되며, 진행 중에는 스피너가 표시됩니다.
4. 완료 시 결과가 팝업으로 표시됩니다.
   - 성공: `{파일명}_userpoints.csv`가 `.res` 파일과 같은 폴더에 생성되고, 자동으로 앱에 로드됩니다.
   - 실패: 에러 메시지가 표시됩니다.

내부적으로 ANSYS의 `cfx5mondata.exe`를 호출하여 추출합니다.

---

## 8. 테마

**View** 메뉴에서 UI 테마를 변경할 수 있습니다.

- **Dark** (기본값)
- **Light**
- **Classic**

---

## 9. 설정 저장

프로그램 종료 시 아래 설정이 자동 저장되며, 다음 실행 시 복원됩니다.

- CFD Solver 선택
- 테마
- 마지막 열었던 폴더 경로
- SMA 윈도우 크기 및 활성화 상태
- 범례 위치
- CFX Install Path

설정 파일: 실행 파일과 같은 폴더의 `cfdconvwatch.ini`


## Trademarks

ANSYS, CFX, Fluent는 ANSYS, Inc.의 등록 상표입니다. Flow360은 FlexCompute, Inc.의 상표입니다. SU2는 Stanford University에서 개발되었습니다. 본 소프트웨어는 이들 회사와 아무런 관련이 없으며, 보증 또는 승인을 받지 않았습니다.

## License
Freeware - free for personal and commercial use. No warranty.

Copyright 2025 CLEW, Inc.
https://www.clew.tech
