# Dacon_Credit Project
카메라 이미지 품질 향상 AI 경진대회 \

# 빛 번짐으로 저하된 📷카메라 이미지 품질을 향상시키는 AI 모델 개발
## 평가산식(PSNR)
* Filter를 통해 빛 번짐 현상이 나타나는 영상을 원상태로 Reconstruction하는 모델 개발
* 평가지표는 PSNR : 원본과 빛 번짐 영상간의 잡음 비율(차이) 계산
## 모델
* UNet - Backbone(ResNet101-V2) => LossFuuction 재구성
* Unet++ - No Backbone
* Unet++은 논문에서 소개하길 Decoder에서 출력된 Feature map과 Encoder에서 보내지는 Feature map이 semantically simillar할 때 최적화하기 쉬울 것이라고 말함.
* 즉, Pyramid Level이 높을수록 Unet보다 Unet++의 성능이 높을 것이라고 봄

# 손실 함수 정의
* 학습에 사용할 손실 함수를 정의합니다.

 $Loss = αL2 + βSSIM + λL1$

1. 왜 PSNR 대신 L2(MSE; Mean Squared Error) 손실 함수를 사용하였는가?
- 보편적인 손실 함수와 달리 PSNR은 최소가 아닌 최대를 찾아야하므로 다른 손실 함수와 조합이 어려움
- 또, PSNR의 최소값은 정의되어있으나, 최대값은 정의되지 않아 학습 중 NaN으로 발산 하여 학습이 진행 되지 않음
- 따라서, PSNR을 간접적으로 최적화 할 수 있는 L2 손실 함수를 사용

 $PSNR(x,y) = 10log_{10}(\frac{MAX^2}{MSE(x,y)})$

 L2(MSE)는 PSNR의 분모이고 학습단계에서 작아지므로 결과적으로 PSNR은 커지게됨

2. SSIM(Structual Similarity Index Map)은 무엇인가?
- SSIM은 밝기(Luminance), 명암비(Contrast), 구조(Structural) 이 3가지 지표를 영상 품질 지표로 사용
- PSNR은 원본 영상과의 차이를 지표로 사용하지만, 원본 영상과의 차이가 인간 시각의 지각품질을 완벽히 반영하지 못함

 $SSIM(x,y) = L(x,y) + C(x,y) + S(x,y)$
 
3. 왜 L1(MAE; Mean Absolute Error) 손실 함수도 사용되었는가?
- GAN, Pix2Pix와 같은 생성 모델에서 L1 손실 함수를 Adversarial Loss와 함께 재건항(Reconstruction Loss)로 사용됨
- 해당 모델들의 결과에 의하면 L2 손실 함수를 재건항으로 사용하게 되면 영상이 흐려지는 문제(Blurring)가 있음
