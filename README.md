## Using DenseNet201 with Fine-Tuning

### Fast classifier-training with normal speed fine-tuning

- Data

  NIPA사전 검증 식물 병충해 사진 train 16000장  + test 4000장

- model

  pre-trained model : DenseNet201

- Work-Flow

  - Data EDA : 

    ImageDataGenerator를 위한 파일 정리 - 각 클래스별로 폴더생성(20개)

  - Feature extracting : 

    1. ImageDataGenerator를 사용하여 파일을 가져온 후 DenseNet을 include_top = False로 특성 추출기 부분만 가져온다. 

    2. ImageDataGenerator.next를 이용하여 이미지 데이터들의 특성을 추출하고 np.array로 저장한다.  이에 따른 라벨도 같이 저장한다.(만약 매우 많은 파라미터를 가진 사전훈련모델을 사용할 때는 RAM이 초과될 수 있다.)

         ex) NasNetLarge

    3. 나중에 다시 사용할 수 있으므로 .npy파일로 저장한다.

  - Classfier : 

    1. 분류기를 생성하고 모델을 훈련시킨다. 이 때 추출한 특성과 라벨을 데이터로 넣는다.

    2. 하이퍼 파라미터를 조정하며 최적의 하이퍼 파라미터를 찾은 후 저장한다.

  - Fine-tuning:

    1. 파인 튜닝을 하기 위해 훈련한 분류기를 DenseNet201모델에 결합시킨다.
    2. DenseNet의 trainable 파라미터를 True로 하고 분류기 모델 부분의 최상단부터 절반 정도를 제외하고 나머지는 다시 freeze해준다.
    3. init learning rate를 재조정하고 fine-tuning 모델을 훈련시킨다.

  - Prediction

    1. train_generator.class_indices()로 현재 분류된 라벨의 순서를 저장한다.
    2. test 이미지를 받아서 전처리 후 미세조정한 모델로 예측한다.

- More-to-do

  - ImageDataGenerator에서 데이터를 조금씩 움직이면서 batch_size를 조정했으면 좋았을 듯 하다.
  - 다른 모델도 사용한 실험을 통해 정확도를 더 높혔으면 좋았을 것 같다.

------------------------------
 - After works
   - 원래 fine-tuning 단계에서 loss가 비정상적으로 올라가며 학습이 안되는 현상이 발생하였다. ->
   batch_size를 1에서 32로 변경하여 학습을 진행하니 제대로 학습이 진행되었다. - 이유가 무엇일까?
   
   최종적으로 정확도는 0.9825 -> 0.9887 로 어느정도 fine-tuning의 효과가 있었다. (중간에 런타임 오류로 끝나지 않았으면 더 높아졌을 수도..)


