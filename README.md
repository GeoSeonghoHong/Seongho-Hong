# AI 기반 지반함몰 위험도 예측 실습

한남대학교 AI 부트캠프 특강에서 사용하는 실습 저장소입니다.

도시 지하 인프라 데이터를 이용해 지반함몰 위험도를 분류하고, 입력 변수 구성에 따라 모델 성능과 해석이 어떻게 달라지는지 확인합니다.

## 실습 자료

1. [학생용 실습 지침서](GUIDE.md)
2. [학생용 실습 노트북](notebooks/01_student.ipynb)
3. [완성 코드와 해설](notebooks/01_solution.ipynb)
4. [실습 데이터](data/hannam_sinkhole_data.xlsx)

## 권장 실행 환경

- Python 3.11 이상
- Jupyter Notebook 또는 Google Colab
- 예상 실습 시간: 약 60분

## 로컬 실행

```bash
git clone https://github.com/GeoSeonghoHong/Seongho-Hong.git
cd Seongho-Hong
pip install -r requirements.txt
jupyter notebook notebooks/01_student.ipynb
```

Google Colab에서는 저장소를 내려받거나 노트북과 데이터 파일을 같은 세션에 업로드한 뒤 실행할 수 있습니다.
