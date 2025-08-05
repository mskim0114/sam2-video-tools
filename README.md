# SAM2 Video Processing Tools

SAM2(Segment Anything Model 2)를 사용한 비디오 처리 도구 모음입니다.

## 📁 프로젝트 구조

```
sam2/my_own/
├── main_pipeline.py              # 메인 파이프라인 (프레임 추출 → 세그멘테이션 → 렌더링)
├── extract_frame.py              # 비디오에서 프레임 추출
├── segmentation.py               # SAM2 세그멘테이션 처리
├── render_video.py               # 마스크를 비디오에 렌더링
├── interactive_pipeline.py       # 기본 인터랙티브 세그멘테이션
├── advanced_interactive.py       # 고급 인터랙티브 세그멘테이션
├── sam2_video_compose.py        # 비디오 합성 도구
├── README_interactive.md         # 인터랙티브 도구 사용법
├── README_compose.md            # 합성 도구 사용법
└── test_small.py                # 테스트 스크립트
```

## 🚀 주요 기능

### 1. 자동 세그멘테이션 파이프라인
- 비디오 프레임 추출
- SAM2 기반 자동 세그멘테이션
- 마스크 비디오 렌더링

### 2. 인터랙티브 세그멘테이션
- 마우스 클릭으로 세그멘테이션 영역 지정
- 프레임 간 이동 및 멀티 오브젝트 지원
- 상태 저장/불러오기 기능

### 3. 비디오 합성
- SAM2 마스크 기반 객체 추출
- 다양한 블렌딩 모드
- 자동 밝기 맞춤
- 실시간 미리보기

## 📋 사용법

### 1. 기본 파이프라인 실행
```bash
cd /home/keti/sam2
python my_own/main_pipeline.py
```

### 2. 인터랙티브 세그멘테이션
```bash
# 기본 버전
python my_own/interactive_pipeline.py

# 고급 버전
python my_own/advanced_interactive.py --video 도로주행2.mp4
```

### 3. 비디오 합성
```bash
python my_own/sam2_video_compose.py \
    --bg 배경영상.mp4 \
    --obj 원본영상.mp4 \
    --mask output_masks/ \
    --out 합성결과.mp4
```

## 🎮 컨트롤

### 인터랙티브 세그멘테이션
- **좌클릭**: 포그라운드 포인트 (녹색)
- **우클릭**: 백그라운드 포인트 (빨간색)
- **ENTER**: 세그멘테이션 실행
- **←/→**: 프레임 이동 (고급 버전)
- **n/N**: 새 오브젝트 시작
- **s/S**: 상태 저장
- **l/L**: 상태 불러오기
- **q/ESC**: 종료

### 비디오 합성
- **Q**: 종료
- **S**: 현재 프레임 저장

## 📁 출력 파일

- `segmentation_result.mp4`: 세그멘테이션 결과 비디오
- `output_masks/`: PNG 마스크 파일들
- `tmp_frames/`: 추출된 프레임들
- `interactive_state.pkl`: 상태 저장 파일

## ⚙️ 요구사항

- Python 3.8+
- PyTorch
- OpenCV
- SAM2 설치
- CUDA (선택사항, GPU 가속)

## 🔧 설치

```bash
# SAM2 설치
pip install -e sam2

# 의존성 설치
pip install opencv-python pillow numpy torch
```

## 📖 상세 문서

- [인터랙티브 도구 사용법](README_interactive.md)
- [비디오 합성 도구 사용법](README_compose.md)

## 🐛 문제 해결

### 메모리 부족
- `--batch-size`를 줄여보세요
- 더 작은 해상도의 영상을 사용하세요

### 세그멘테이션 품질 개선
- 더 많은 포인트를 입력하세요
- 객체 경계선 근처에 포인트를 찍으세요
- 포그라운드/백그라운드 포인트를 균형있게 입력하세요

## 📝 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.

## 🤝 기여

버그 리포트나 기능 제안은 이슈를 통해 해주세요. 
