# 🩺 흉부 X-ray 병변 감지 및 리포트 생성 시스템  
### 딥러닝 모델과 시각화 기술을 활용한 MIMIC-CXR 기반 자동 판독 소견 생성

---

## 📌 Motivation

> “암 관련 피해구제 신청 3건 중 1건은 ‘오진’” – 뉴스웰 (2021)

의료 인공지능 기술의 발전에도 불구하고, **방사선 영상 판독의 오류 및 오진 문제**는 여전히 심각합니다.  
이에 따라, **정확하고 자동화된 의료 리포트 생성 시스템**의 필요성이 높아지고 있으며, 본 연구는 흉부 X-ray를 기반으로 리포트 생성 모델을 비교 분석합니다.

<img width="519" alt="image" src="https://github.com/user-attachments/assets/036cca89-47b1-4333-bd9f-c8e21646b270" />
<img width="480" alt="image" src="https://github.com/user-attachments/assets/c410c932-4824-4bb0-935f-d323741e9fc8" />
<img width="400" alt="image" src="https://github.com/user-attachments/assets/aaf8bf12-2bbe-4302-8e88-bb93093f4200" />
<img width="550" alt="image" src="https://github.com/user-attachments/assets/d62fb75f-e8f9-4230-9712-325886f4627b" />

---

## 🔍 Related Work

1. **Vision-Language 기반 프레임워크**
   - Arısoy et al. (2025): 이미지-텍스트 기반 자동 보고서 생성 구조 제안
   - <img width="476" alt="image" src="https://github.com/user-attachments/assets/cc5c1e41-5476-48cc-a7c1-fc7fff28488e" />


2. **Transformer 기반 리포트 생성**
   - Agarwal & Verma (2025): ViT와 GPT-4 활용한 자동 리포트 생성 구조 설계
   - <img width="444" alt="image" src="https://github.com/user-attachments/assets/8d098b55-ee7a-4c65-bc36-b9ddeba52e6c" />


3. **Memory-driven Transformer**
   - Chen et al. (2020): 리포트 내 정보 연결 강화를 위한 메모리 기반 모델
   - <img width="354" alt="image" src="https://github.com/user-attachments/assets/6be34b99-ea65-4269-a7ac-13efdb028ede" />


4. **Anatomical Attention 기반 모델**
   - Nguyen et al. (2025): 해부학적 정렬 기반 리포트 생성으로 중요한 부위 강조
   - <img width="333" alt="image" src="https://github.com/user-attachments/assets/f38f0ad0-6cf8-4690-bb59-813855818cc2" />

---

## ⚙️ Experimental Environment

- **Dataset**: [MIMIC-CXR](https://physionet.org/content/mimic-cxr/2.0.0/)
  > 흉부 X-ray와 방사선 전문의 리포트가 1:1로 매칭된 대규모 의료 데이터셋
  > <img width="784" alt="image" src="https://github.com/user-attachments/assets/f13ce814-368f-4b64-b46d-ff9ddfa1f296" />


- **Fine-Tuning 모델**:

- <img width="1037" alt="image" src="https://github.com/user-attachments/assets/52c53d9f-425c-4bca-905b-f351d566ca22" />

- **하드웨어**:
  - <img width="487" alt="image" src="https://github.com/user-attachments/assets/e2e2557d-1808-4826-869f-ee2870a408b0" />


- **데이터 사용 비율 및 평가지표**:
  - 25%, 50%, 75%, 100%로 증분 실험 수행
  - <img width="538" alt="image" src="https://github.com/user-attachments/assets/4fb5354c-9135-4f7e-a7e1-bf697cf57f6e" />


---

## 🧪 Experimental Results

### 📊 학습 이미지 사용량과 모델에 따른 성능 비교 (표 9)

| Model  | IMAGE Volume (%) | BL-1   | BL-2   | BL-3   | BL-4   | MTR    | RG-L   | AVG. △   |
|--------|------------------|--------|--------|--------|--------|--------|--------|-----------|
| **R2Gen** | 25%              | 0.1900 | 0.1072 | 0.0667 | 0.0450 | 0.0874 | 0.2245 | -         |
|        | 50%              | 0.2411 | 0.1411 | 0.0906 | 0.0622 | 0.1066 | 0.2431 | 22.81%    |
|        | 75%              | 0.2484 | 0.1441 | 0.0922 | 0.0634 | 0.1068 | 0.2442 | 24.74%    |
|        | 100%             | **0.2738** | **0.1615** | **0.1038** | **0.0712** | **0.1139** | **0.2500** | **35.22%** |
| **A3Net** | 25%              | 0.2174 | 0.1207 | 0.0737 | 0.0490 | 0.0966 | 0.2277 | -         |
|        | 50%              | 0.2576 | 0.1446 | 0.0891 | 0.0590 | 0.1101 | 0.2397 | 14.72%    |
|        | 75%              | 0.2760 | 0.1540 | 0.0948 | 0.0637 | 0.1140 | 0.2388 | 19.89%    |
|        | 100%             | **0.3011** | **0.1736** | **0.1098** | **0.0739** | **0.1250** | **0.2513** | **31.99%** |

> ✅ **A3Net**은 모든 데이터 비율에서 R2Gen보다 전반적으로 우수한 성능을 보였으며,  
> 특히 BLEU-4, MTR, ROUGE-L 지표에서의 상승 폭이 컸습니다.


### 🧾 실제 의사 리포트 vs 생성 리포트 비교
- <img width="1044" alt="image" src="https://github.com/user-attachments/assets/cabe431b-7ce4-4cbc-80a8-3ad82269d414" />

### 🔍 주요 분석
- 데이터가 많을수록 성능 향상 폭 증가
- A3Net은 anatomy 기반 attention으로 더 정교한 리포트 생성
- ### 🧠 R2Gen vs A3Net 모델 비교

| 항목             | R2Gen | A3Net | A3Net이 더 우수한 이유 |
|------------------|--------|--------|------------------------|
| **기본 구조**     | CNN + Transformer<br>Encoder-Decoder | CNN + Transformer<br>+ Cross-modal Memory Network (CMM) | **멀티모달 정보 통합 강화** |
| **이미지 인코더** | ResNet 기반 CNN<br>→ global feature 추출 | ResNet 기반 CNN<br>→ Memory 저장<br>→ Cross-modal attention에 활용 | **이미지 정보의 활용 범위와 해석력 ↑** |
| **텍스트 디코더** | Transformer Decoder | Transformer Decoder + CMM | **이미지-텍스트 대응 관계를 기억하고 재참조** |
| **Attention 방식** | Self-attention | Cross-attention<br>+ Memory-attention | **문맥 기반 디코딩 가능** |

### 🩻 리포트 문장 분석: R2Gen vs A3Net

| 항목 | R2Gen | A3Net | 설명 |
|------|--------|--------|------|
| **저폐용적**<br>(Low lung volume) | 간단 설명 | 구조적 설명 추가 | **A3Net**:<br>“low lung volumes”뿐 아니라,<br>그 원인인 “secondary crowding of bronchovascular markings”까지 명시 |
| **흉수**<br>(Pleural Effusion) | 모호한 표현 | 정확한 해부학 표현 | **R2Gen**: “bibasilar opacities” (양측 기저부 음영)<br>**A3Net**: “blunting of the costophrenic angle” (늑횡격각 둔화) |
| **폐경화**<br>(Consolidation) | 명시 없음 | 명시 있음 | **A3Net**:<br>Ground-truth와 유사하게 “no confluent consolidation” 명시하며<br>pneumonia(폐렴) 배제하는 표현 포함 |
| **문장 구조/스타일**<br>(Sentence Structure/Style) | 단순 나열 | 의료 보고서 스타일 | **A3Net**:<br>더 정돈된 문장 구조의 리포트 생성 |


---

## ✅ Conclusion

- 데이터 사용량 증가가 모델 성능에 **직접적인 영향**을 미침
- A3Net은 **리포트 내 해부학적 일관성** 측면에서 우수한 결과를 보임
- 향후 더 복합적인 Transformer 구조나 VLM 모델 결합 가능성 존재

---

## 🚀 Future Work

- ViT-GPT 기반 멀티모달 구조 도입
- 병변 시각화 (Grad-CAM 등) 기반 LLM 리포트 정교화
- 다양한 질병/클래스에 대한 generalization 성능 확인

---

## 👨‍💻 Contributors

- 서재석 (Konyang Univ., Dept. of Artificial Intelligence)  
- 장근혁 (Konyang Univ., Dept. of Artificial Intelligence)  

Contact:  
- vhvh1398@naver.com  
- forren0418@naver.com

---

## 📚 References

1. M. Varol Arısoy et al., *A vision attention driven Language framework for medical report generation*, Scientific Reports, 2025.  
2. L. Agarwal and B. Verma, *Advanced Chest X-Ray Analysis via Transformer-Based Image Descriptors*, arXiv:2504.16774  
3. Z. Chen et al., *Generating Radiology Reports via Memory-driven Transformer*, EMNLP, 2020  
4. Q. V. Nguyen et al., *Anatomical Attention Alignment*, arXiv:2505.07689  
5. A. Johnson et al., *MIMIC-CXR: A large publicly available database*, NeurIPS ML4H, 2019
