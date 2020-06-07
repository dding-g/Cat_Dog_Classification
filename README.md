# Cat_Dog_Classification
Pytorch를 이용한 강아지, 고양이 사진 분류

**설명**
==
1. 데이터 준비
  * 훈련의 다양화를 위해 `location, RandomResize, RandomHorizonCrop`들을 적용시켰다.
  * 연산의 부담을 덜기위해 이미지를 resize 할때 128 x 128 로 비교적 작게 주었다. 사진을 실제로 뽑아보니 10개중 7개 정도 비율로 얼굴이 잘 나와있는걸 볼 수 있었다.
  * batch size는 처음에 200, 500, 1000 등 상당히 크게 주었다. `SGD`의 특성상 큰 `Batch size`를 주어도 된다는 생각이었는데 이는 우리의 모델이 잘 구성되었을때 통용하는 이야기 였다. loss값이 점점 커지는걸 보고 최대한 모델을 잘 만들어 보고 `Batch size`는 조금 작게 주었다.
  ---
2. 학습
* epoch를 60회 정도로 주고 학습시켜서 기록을 보고 괜찮은 epoch이 어느정도인지 파악한다.
* 처음에는 ConvLayer를 5 ~ 6 곂으로 쌓아 채널 수를 늘려보고, `full connected`할때 사용되는 `nn.Linear`의 parameter들을 조정해보기도 했다.  `Cuda OutofMemory Error`와 별로 개선되지 않는 loss를 보고 다른 방법을 찾았다.
* 가장 효과적인 방법은 `Normalization`을 제대로 해 주는 것 이다. 위에서도 말했듯이 RGB image를 `Normalization`할때 널리 사용되는 `mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]` 값으로 바꾸어 주었다. 원래는 0.5 로 `Normalization` 해주었는데, 생각해보니 이는 흑백 사진. 즉 값의 분포에 대한 변수가 없는 이진 데이터에 대해서만 효과가 있다.
* 다음은 `batch size`를 조절했다. 기존의 100회 이상의 size에서 30,20,10 으로 점차 내렸다. `BGD`와 `SGD`의 특성을 생각해 정확도를 높이기 위해 size를 줄였다.
* `batch size`가 20인 경우
 - test accuracy가 dog : 88%, cat : 80%, total : 84% / training loss : 0.253, validation loss : 0.254 가 나왔다. 
* `batch size`가 10인 경우
 - test accuracy가 dog : 90%, cat : 86%, total : 88% 가 나왔다.
* learning rate을 0.01로 두고 60회 epoch를 돌려 loss를 0.25 까지 떨구었다.
 + 더 떨구기 위해 조금 더 촘촘한 작업이 필요하다고 생각했다. 그래서 SGD의 Iearning rate 을 0.01 -> 0.001로 낮춰 gradient를 더 촘촘하게 찾을 수 있도록 하였다. 하지만 
loss가 떨어지는 속도는 너무 느렸고 충분한 성능을 낼 수 없었다. 2만개의 데이터를 Iearning rate  0.001로 학슴시키기엔 충분한 epoch를 낼 수 없어서(학습 속도가 너무 느려서 많이 줄 수 없었다.) Ir을 0.008 정도로 주고 학습을 다시 시켜 보았다.
* Iearning rate을 0.008로 준 경우
  - test accuracy가 dog : 87%, cat : 89%, total : 88% 가 나왔다.
  - epoch를 더 주고 학습을 시키면 더 좋은 성능을 낼 것 같았지만 이정도로만 학습시켜도 5시간정도 걸려서 더 늘리는건 힘들것같다.
  - 성능 차이를 보기위해 7x7 사이즈로 주었던 filter size를 3x3으로 바꾸었다. 결과는 dog : 90%, cat : 84%, total : 87% 로 큰 차이는 없는것으로 보였다.

  ---
3. 결과

* 결과는 강아지 분류 90%, 고양이 분류 84%로 그닥 좋지 않은 성능을 보였다. 정확도를 더 올리기 위해서는 epoch을 좀더 늘리고 model을 Conv Layer를 추가하거나, image resize를 좀 크게 해주거나 하는 방식으로 최대한 효과적으로 구성해야 하는데, 메모리 등 제한적인 환경에서 진행하다 보니 Layer의 크기, image의 크기 등 더 많은 시도를 하지 못해 아쉽다.
