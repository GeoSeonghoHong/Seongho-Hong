# AI 기반 지반함몰 위험도 예측 실습

한남대학교 AI 부트캠프 특강에서 사용하는 실습 저장소입니다.

도시 지하 인프라 데이터를 이용해 지반함몰 위험도를 분류하고, 입력 변수 구성에 따라 모델 성능과 해석이 어떻게 달라지는지 확인합니다. 세 학생용 노트북은 **분석 목표와 데이터는 같고, 제공되는 코드와 힌트의 수준만 다릅니다.**

## 난이도 선택

| 과정 | 추천 대상 | 제공 방식 | 노트북 |
|---|---|---|---|
| Beginner | Python·머신러닝 입문자 | 코드 대부분 제공, 실행과 결과 해석 중심 | [열기](notebooks/beginner.ipynb) · [Colab](https://colab.research.google.com/github/GeoSeonghoHong/Seongho-Hong/blob/main/notebooks/beginner.ipynb) |
| Intermediate | pandas와 기본 ML 경험자 | 분석 골격 제공, 핵심 변수와 인수 완성 | [열기](notebooks/intermediate.ipynb) · [Colab](https://colab.research.google.com/github/GeoSeonghoHong/Seongho-Hong/blob/main/notebooks/intermediate.ipynb) |
| Advanced | 스스로 분석을 설계하고 싶은 학습자 | Task와 짧은 힌트만 제공 | [열기](notebooks/advanced.ipynb) · [Colab](https://colab.research.google.com/github/GeoSeonghoHong/Seongho-Hong/blob/main/notebooks/advanced.ipynb) |
| Solution | 복습과 정답 확인 | 완성 코드와 결과 해설 | [열기](notebooks/solution.ipynb) · [Colab](https://colab.research.google.com/github/GeoSeonghoHong/Seongho-Hong/blob/main/notebooks/solution.ipynb) |

처음 참여하거나 Python 경험이 많지 않다면 **Beginner**로 시작하세요. Model A까지 수행한 뒤 여유가 있으면 Intermediate 또는 Advanced에 도전해도 좋습니다.

## 실습 자료

- [실습 지침서](GUIDE.md)
- [실습 데이터](data/hannam_sinkhole_data.xlsx)
- [필요 패키지](requirements.txt)

## 권장 실행 환경

- Python 3.11 이상
- Jupyter Notebook 또는 Google Colab
- 예상 실습 시간: 약 60분

## 로컬 실행 예시

```bash
git clone https://github.com/GeoSeonghoHong/Seongho-Hong.git
cd Seongho-Hong
pip install -r requirements.txt
jupyter notebook notebooks/beginner.ipynb
```

Google Colab에서는 위 표의 **Colab** 링크를 누른 뒤 셀을 위에서부터 차례로 실행하면 됩니다. 로컬 데이터 파일이 없으면 노트북이 GitHub의 실습 데이터를 자동으로 불러옵니다.
