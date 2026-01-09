### **구현 과정**

1. **입력 이미지 전처리**:
    - 스도쿠 퍼즐 이미지를 입력받아 OpenCV로 그레이스케일로 변환, 블러링 및 적응형 이진화(adaptive thresholding) 수행.
    - 퍼즐 보드를 탐지하기 위한 준비 작업 수행.
2. **스도쿠 보드 탐지**:
    - 가장 큰 사각형 외곽선을 탐지하여 퍼즐 보드를 식별.
    - 원근 변환(perspective transform)을 적용해 보드를 상단에서 내려다보는 뷰로 변환.
3. **숫자 추출**:
    - 9x9 그리드로 보드를 나누어 각 셀을 개별적으로 처리.
    - 각 셀에서 숫자를 감지하고, 숫자가 존재하면 이를 추출.
4. **OCR로 숫자 인식**:
    - MNIST 데이터셋을 사용해 커스텀 CNN 모델(**SudokuNet**)을 학습시켜 손글씨 숫자를 인식.
    - 추출된 숫자를 전처리해 학습된 모델 형식에 맞춰 분류.
5. **퍼즐 풀이**:
    - **py-sudoku** 라이브러리를 사용해 퍼즐을 자동으로 해결.
    - 풀이된 숫자를 보드에 업데이트.
6. **결과 출력**:
    - 원본 이미지 위에 풀이된 결과를 시각적으로 표시하고 출력.

### **핵심 구성 요소**

- **SudokuNet 모델**:
    - Keras와 TensorFlow로 설계된 커스텀 CNN 모델.
    - MNIST 데이터셋으로 학습, 테스트 데이터에서 99% 정확도 달성.
- **OpenCV 이미지 처리**:
    - 이진화, 외곽선 탐지, 원근 변환 등 기술로 보드와 셀을 탐지 및 추출.
- **OCR**:
    - 모델을 사용해 숫자를 분류하고 퍼즐 풀이에 활용.
- **스도쿠 풀이 알고리즘**:
    - py-sudoku 라이브러리를 활용해 퍼즐을 자동으로 해결.

### **사용된 도구와 라이브러리**

- **OpenCV**: 이미지 전처리 및 퍼즐 보드 탐지.
- **TensorFlow/Keras**: 숫자 인식을 위한 CNN 모델 학습.
- **scikit-image**: 셀 내 숫자 추출 보조 도구.
- **py-sudoku**: 퍼즐 풀이 알고리즘.
- **imutils**: 원근 변환 및 이미지 처리 보조.

---
---

# CameraPi 설정 (힘들었던 부분만)
- https://www.raspberrypi.com/documentation/accessories/camera.html#connect-the-camera

- https://www.raspberrypi.com/documentation/computers/camera_software.html#rpicam-apps

- https://libcamera.org/getting-started.html
	- git clone https://git.libcamera.org/libcamera/libcamera.git
	- cd libcamera
	- meson setup build
		- meson 설치, 환경변수 설정
			- 환경변수 설정 
				**`~/.local/bin`을 `PATH`에 추가하기**
				`~/.local/bin` 경로를 `PATH`에 추가하여 Python 패키지로 설치한 도구들을 실행할 수 있도록 해야 합니다.
				
				### **1. 임시로 `PATH`에 추가**
				
				터미널 세션 동안만 적용하려면 아래 명령을 실행합니다:
				
				bash
				
				코드 복사
				
				`export PATH=$PATH:~/.local/bin`
				
				- 이 명령은 현재 세션에만 유효하며, 터미널을 닫으면 원래 상태로 돌아갑니다.
				- 추가 후 확인:
				    
				    bash
				    
				    코드 복사
				    
				    `echo $PATH`
				    
				    - 출력 예시:
				        
				        plaintext
				        
				        코드 복사
				        
				        `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games:/home/your_username/.local/bin`
				        
				
				### **2. 영구적으로 추가**
				
				터미널을 새로 열 때마다 적용되도록 설정하려면, `.bashrc` 또는 `.zshrc` 파일에 추가해야 합니다.
				
				1. **파일 열기**
				    
				    - Bash를 사용하는 경우:
				        
				        bash
				        
				        코드 복사
				        
				        `nano ~/.bashrc`
				        
				    - Zsh를 사용하는 경우:
				        
				        bash
				        
				        코드 복사
				        
				        `nano ~/.zshrc`
				        
				2. **`PATH` 추가** 아래 줄을 파일의 마지막에 추가합니다:
				    
				    bash
				    
				    코드 복사
				    
				    `export PATH=$PATH:~/.local/bin`
				    
				3. **변경 내용 적용** 파일을 저장한 후, 다음 명령으로 변경 사항을 적용합니다:
				    
				    bash
				    
				    코드 복사
				    
				    `source ~/.bashrc`
				    
				4. **확인** 터미널을 새로 열고 `echo $PATH`를 실행하여 `~/.local/bin`이 포함되었는지 확인합니다.
		- ninja, cmake, yaml, ply 설치
	- ninja -C build install
		- libjpeg-dev 패키지 설치

- https://datasheets.raspberrypi.com/camera/picamera2-manual.pdf

--- 

- OpenCV로 하는 일
	- 이미지 처리 (필터링, 에지 검출, 색 변환 등)
	- 객체 감지 (Haar Cascade, DNN 등)
	- 영상 처리 (동영상 읽기, 프레임 추출 등)
	- 카메라 캘리브레이션 및 3D 비전 (스테레오 매칭, 깊이 추정 등)
	- 실시간 처리에 강점

- TensorFlow로 하는 일
	- 딥러닝 모델 설계 및 학습 (신경망 구축, 훈련)
	- TensorFlow Lite를 통한 모바일 및 임베디드 시스템 배포
	- TensorFlow.js를 통한 웹 브라우저 내 학습 및 실행
	- 데이터 전처리와 모델 최적화

- MNIST 데이터셋
	- NIST 데이터셋 기반
	- 머신러닝 연구에서 사용하기 쉽도록 전처리된 손글씨 숫자 이미지의 컬렉션
	- 숫자(0부터 9까지)가 포함된 28x28 픽셀 크기의 그레이스케일 이미지

- TensorFlow와 Keras는 datasets 모듈로 다양한 데이터셋을 제공
	- MNIST: 손글씨 숫자
	- SVHN: 프린트된 숫자

- TensorFlow로 다양한 데이터셋 학습 가능
	- 순차적 학습 (Sequential Learning)
	- 혼합 학습 (Joint Training)
	- 지속적 학습(Continual Learning)
	- 멀티 태스크 학습 (Multi-task Learning)
	- 전이 학습 (Transfer Learning)

- TensorFlow로 학습한 모델을 TensorFlow Lite 모델로 변환시켜 경량화 후 라즈베리파이에서 사용

- Tesseract, EasyOCR 등 이미 구현된 OCR 라이브러리 사용 가능

- TensorFlow와 Keras
	- Servlet과 RestAPI?
	- Java와 Spring?
