# Redhat_Practice using XGBoost

## Abstract
- XGBoost에 대해 이해하고, HyperParameter Tuning을 통해 결과값을 개선하는 연습을 해본다.
- Feature importance 를 활용해보고, reduce demension을 통해서 결과값을 개선할 수 있는지 확인해본다.
- 이를 위해 kaggle의 'Predicting Redhat Business Value'라는 프로젝트를 수행한다.
- 프로젝트를 통해 배운 점을 정리하고, 추가과제를 도출한다.

## Background
- 이전 프로젝트인 'shelter animals'에서 최종선택된 모델인 XGBoost
- 모델 자체에 대한 이해 부족
- 도구에 대해서 이해해야, 도구를 개선할 수 있다!

## XGBoost
- Supervised Learning
- CART(classification & Regression trees) : Prediction score in each leaf
- Tree Ensemble / Additive Training
- Objective Function = Trainig Loss + Regularization
- 사용자 정의 손실함수 지원 가능
- Model Complexity
- The Structure Score

## Project Definition
- Predicting Red Hat Business Value in kaggle
- Identifies which customers have the most potential business value for Red Hat based on their characteristics and activities
- Binary classification
- Eval function : auc

## Methods & Process
- EDA
- Preprocessing
- Modeling

## EDA
- column name이 아무런 정보도 담고 있지 않아서(ex.char_1, 2, ...) domain knowledge를 활용하기 힘듦.
- features(52) : str(21) / bool(28) / int(1) / datetime(2)
- feature engineering : split date features to year/month/day/weekday features. 
- feature engineering : making 'from_join_to_act'(date_act - date_ppl) feature.
- bar chart로 확인했을 때, 'activity_category' 등의 소수의 feature를 제외하면 대부분의 feature들이 outcome과 거의 유사한 구조로 되어있어 유의미한 변수가 아닐 것으로 예상됨.
- feature importance를 확인해보고, 의미없어보이는 변수들을 줄이면 결과값에 어떤 영향을 주는지 확인해 보기로 함.

## Preprocessing
- 함수를 만들어서, 모든 feature의 dtype을 int로 바꾸어주는 작업 진행.
- Split dataset to train/validation by LabelKFold
- Convert to np.array

## Modeling
- Using XGBoost
- HyperParameter Tuning : max_depth(9~30) / subsample(0.8~0.9) / colsample_bytree(0.8~0.9) / nums_rounds(500~2000)
- Feature_importance

## Results & Discussions
- Best Result : train_auc(1) / validation_auc(0.982235) / test_auc(0.969019)-1555/2271teams in kaggle competition
- HyperParameters : max_depth(20), subsample(0.9), colsample_bytree(0.9), nums_rounds(207_earlystopping)
- max_depth가 계산횟수와 결과에 가장 큰 영향을 미치는 것을 확인함. max_depth부터 먼저 조정하고, 그 다음에 과최적화를 방지하는 변수들 조정해주는 것이 효율적임.
- Early_stopping_rounds를 걸어놓고, nums_rounds는 굉장히 큰 수로 설정해 놓는 것이 효율적임. 만약 early_stop에 안걸리고 발산하는 것 같으면 멈추고 다시 설정하면 됨.
- Feature importance 를 확인해보니, group_1 이나 from_join_to_act 처럼 10%가 넘는 중요도를 가지는 feature도 있지만, char_2_ppl이나 char_6_act 처럼 0.01%도 안되는 feature들도 있음. 
- 중요도 0.001 이하의 feature를 제거하는 함수를 만들어서 적용하여 5개의 변수를 제거하고 계산해 봄.
- 그 결과 train_auc(1) / val_auc(0.981906) / test_auc(0.899310) 으로 성능이 크게 떨어짐.
- 결과적으로 이 dataset에서는 영향이 적어보이는 feature도 모델에 적용하는 것이 바람직함.

## Conclusion
- XGBoost code 분석
- AWS에서 GridSearchCV 로 HyperParameterTuning 해보기
- Domain knowledge 를 학습하고 적용해 볼 수 있는 Project 해보기
