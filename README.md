# AI-Project

# 멀티모달과 단일 CNN 모델의 얼굴 감정 인식 성능 비교 연구
> 얼굴 이미지 기반 감정 인식에서 단일 CNN과 멀티모달 구조의 성능을 비교한 AI 프로젝트입니다.

---

## 1. Motivation

최근 AI 기술의 발전으로 감정 분석 기술의 중요성이 증가하고 있습니다.  
기존 CNN 기반 감정 분류는 시각적 특징만 반영하기 때문에 감정의 복합성과 문맥 이해에는 한계가 있습니다.  
본 프로젝트에서는 VLM(Vision-Language Model)을 활용해 자연어 기반 설명을 CNN feature와 결합하여  
정적 이미지에서의 감정 인식 성능을 향상시키는 멀티모달 구조를 제안합니다.

<p align="center">
  <img src="motivation.png" alt="motivation" width="100%"/>
</p>

---

## 2. Related Work

- [Yang et al. (2024)](https://arxiv.org/abs/2404.07078),  
  *VLLMs Provide Better Context for Emotion Understanding Through Common Sense Reasoning*  
  → 이미지 속 인물의 감정을 상식 기반으로 이해하는 데 있어 VLM의 효과를 강조한 논문

- LLaVA 및 QFormer 등을 활용한 이미지-텍스트 융합 기반 감정 추론 사례 소개

<p align="center">
  <img src="Related work.png" alt="Related work" width="100%"/>
</p>

---

## 3. Flowchart

1. CNN 모델 학습 및 feature 추출  
2. BLIP2로 이미지 설명 문장 생성  
3. CLIP으로 텍스트 임베딩  
4. CNN + CLIP feature 결합 및 정규화  
5. 다양한 MLP 및 Attention 구조 학습  
6. Flask 웹으로 결과 시각화

<p align="center">
  <img src="flowchart.png" alt="flowchart" width="100%"/>
</p>


---

## 4. Dataset

- **RAF-DB** (Real-world Affective Faces Database)  
- 총 이미지 수: 15,339장  
  (Train: 9,816 / Val: 2,455 / Test: 3,068)  
- 클래스 불균형 존재 → 일부 클래스에 augmentation 적용 (flip, rotate 등)

<p align="center">
  <img src="dataset.png" alt="dataset" width="100%"/>
</p>

<p align="center">
  <img src="labelimage.png" alt="labelimage" width="100%"/>
</p>


---

## 5. CNN (Baseline)

- 모델 후보: VGG16, ResNet18, DenseNet121
- 데이터셋: RAF-DB  
- Hyperparameter: Batch size=64, Epochs=30, LR=0.001, EarlyStopping(patience=5)

| Model     | Accuracy | F1-score |
|-----------|----------|----------|
| VGG16     | 0.6680   | 0.6558  |
| ResNet18  | 0.7462   | 0.7348   |
| **DenseNet121** | **0.7697**   | **0.7666** |

>  **DenseNet121**은 정확도와 F1-score에서 가장 우수한 성능을 보여,  
> 본 프로젝트의 최종 baseline CNN 모델로 선정되었습니다.

---

## 6. VLM

- BLIP2: 이미지 → 설명 문장 생성  
  (Prompt 예시: *“What emotion is the person showing?”*)  
- CLIP: 생성된 문장을 768차원 텍스트 임베딩으로 변환

<p align="center">
  <img src="vlm.png" alt="vlm" width="100%"/>
</p>

- Prompt 결과
<p align="center">
  <img src="blip2result.png" alt="blip2result" width="100%"/>
</p>
---

## 7. Feature Extraction & Fusion

- CNN: 1024차원 feature 추출  
- BLIP2 + CLIP: 768차원 텍스트 임베딩  
- 두 feature를 concat 후 L2 정규화하여 MLP 입력으로 사용

<p align="center">
  <img src="feature fusion.png" alt="feature fusion" width="100%"/>
</p>

---

## 8. MLP Training & Results

멀티모달 feature(CNN + CLIP)를 결합한 후 다양한 MLP 구조를 실험하였습니다.  
특히 Attention 계열 구조를 적용하여 정보 간 상호작용을 학습하도록 설계하였으며,  
다음은 CNN 단일모델(DenseNet121) 대비 Test Accuracy 향상률을 기준으로 비교한 결과입니다.

| MLP 구조                 | Test Accuracy | CNN 단일 모델 대비 향상률 |
|--------------------------|---------------|----------------------------|
| **MLP + Attention**      | **0.8226**    | **7.3% 🔺**                |
| MLP + Self-Attention     | 0.8147        | 5.8%                       |
| MLP + Cross-Attention    | 0.8089        | 5.3%                       |

> ✅ **MLP + Attention 구조**가 가장 뛰어난 성능을 보여,  
> 본 프로젝트의 멀티모달 최종 구조로 채택되었습니다.

---

---

## 9. Best MLP Results (F1-score per class)

아래는 CNN 단일모델과 Best MLP (MLP + Attention) 구조 간의  
클래스별 F1-score 비교 결과입니다.

| Class (감정)     | CNN 단일모델 | CNN + VLM 멀티모달 | 향상률  |
|------------------|--------------|---------------------|---------|
| 0 (surprise)     | 0.7548       | 0.7976              | 5%      |
| 1 (fear)         | 0.4324       | **0.5846**          | **35% 🔺** |
| 2 (disgust)      | 0.5176       | 0.5521              | 6%      |
| 3 (happy)        | 0.9012       | 0.9184              | 1.9%    |
| 4 (sad)          | 0.7326       | 0.7930              | 8%      |
| 5 (angry)        | 0.6418       | 0.7284              | 13%     |
| 6 (neutral)      | 0.6864       | 0.8000              | 16%     |

> ✅ 모든 클래스에서 성능이 향상되었으며,  
> 특히 `fear` 클래스에서 **35% 향상**이라는 큰 개선 효과가 나타났습니다.
> 이는 Attention이 미묘한 차이를 정교하게 반영하고 학습할 수 있다는 것을 보여줍니다.

## 10. Final Comparison – 단일 CNN 모델 vs 멀티모달 구조

| 모델         | Test Accuracy | CNN 대비 향상률 |
|--------------|----------------|------------------|
| CNN (DenseNet121) | 0.7697         | -                |
| **CNN + VLM**     | **0.8226**     | **7.3% 🔺**       |

> ✅ CNN + VLM 구조는 기존 CNN 모델보다 **정확도가 7.3% 향상**되었으며,  
> 멀티모달 구조의 의미 기반 정보 보완 효과가 감정 분류 성능 향상에 기여함을 확인할 수 있었습니다.

### 🔍 분석 요약

- CNN은 시각 정보만 반영하지만, VLM은 **시각 + 언어 정보 간의 상호 보완적 의미**를 함께 활용
- 표정이 모호하거나 혼합적인 경우, **텍스트 기반 맥락이 분류에 도움**을 줄 가능성이 큼
- 7.3% 정확도 향상은 실질적 성능 향상으로 해석 가능
- 단일모델 대비, 멀티모달 구조는 **정보를 더 풍부하고 정교하게 표현**할 수 있음을 실험적으로 입증

## 11. Problems & Improvements

- **BLIP2 프롬프트 품질 문제**  
  → 부정확하거나 반복적인 응답 발생, 감정 단어 대신 설명 출력

<p align="center">
  <img src="prompt problem.png" alt="prompt problem" width="100%"/>
</p>

- **데이터 불균형**  
  → 소수 클래스 학습 불안정, 향후 `class_weight` 적용 예정

  <p align="center">
  <img src="data imbalance.png" alt="data imbalance" width="100%"/>
</p>

- **Augmentation 실험의 일관성 부족**  
  → crop 제외, flip 위주로 통일. Crop 비율 조정 실험 필요  

<p align="center">
  <img src="crop.png" alt="crop" width="100%"/>
</p>

---

## 12. Flask

- 사용자 로그인 및 이미지 업로드  
- 감정 예측 결과 + 랜덤 피드백 코멘트 출력  
- 감정 분석 결과 DB 저장 (SQLite + SQLAlchemy)  
- 사용자별 분석 히스토리 + 대시보드 시각화 (matplotlib)  
- 관리자 페이지에서 전체 예측 기록 조회 및 삭제  
- 외부 접근은 `ngrok`을 통해 웹 공유 가능

---
