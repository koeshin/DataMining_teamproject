# DataMining_teamproject

## 주제 선정 이유
코로나 유행 이전, 한국 영화산업은 매해 꾸준한 성장을 이루었으며 특히 2014년 이후로 2조원이 넘는 매출을 보여주고 있다.
극장 매출이 영화 산업 전체 수익의 80% 이상을 차지할 정도로 극장 매출을 정확하게 예측하는 것이 영화 흥행 예측의 핵심.
## 변수 설정
영화제목, 개봉당일 관객수, 개봉후 1주일간 누적 관객수, 개봉 2주일간 누적관객수, 개봉당일 스크린수, 개봉 7일차 스크린수, 개봉 14일차 스크린수, 영화 국적, 상영 등급, 개봉 계절, 개봉 월, 배우점수, 사회적 거리두기 단계, 총 누적 관객수로 이루어져 있습니다.
Nationality(국적)은 0-> 한국, 1-> 미국 
Grade(상영 등급) 0-> 전체 관람가, 1->12세 관람가, 2-> 15세 관람가, 3-> 청소년 관람불가
Season은 봄,여름.가을,겨울 순으로 0,1,2,3으로 변환
Actorscore은 주연 배우 두 명의 출연작중 누적 관객수 100만 이상 작품수의 평균
50만 100 만 200만 300만 500만 1000만-> 0,1,2,3,4,5로 카테고리형 변수로 변환(18-21데이터통합_1.csv)
50만,100만이 독립영화 와 상업영화로 분류하는 위한 기준.
200, 300 만 손익분기점을 넘길 수 있는 관객수로 가장 많이 언급되는 기준. 
500만은 소위말하는 대박 영화 흥행 한 영화.
1000만영화는 말그대로 천만영화 국민영화.

영화제목은 변수에서 제외하고 방법론 진행

## 사용 방법론
SVM,DT, Clustering

## SVM-SVC 사용
labels(누적관객수)의 카테고리가 6개 이므로 kernel='rbf'로 하여 진행.
C 값이 5,11,12 일떄 0.796296으로 가장 높은 정확도를 보임. 여러 gamma값을 돌려서 사용해 보았지만 default 값일떄 가장 성능이 높았음.

### 교차검증
cv=6일떄 0.6888로 가장 높은 평균을 보임. cv>7일 경우 점차 낮은 정확도를 보임.
### 표준화 (StandardScaler)
C=100,gamma=0.01 일때 0.6865로 가장 높은 성능을 보임.

### 교차검증
cv=4 일떄 평균이 0.6388로 가장 높은 평균을 보임.

-----
## DT (DecisionTreeClassifier)
### 모델 성능
초기 모델 테스팅 성능 0.7419
min-samples-leaf를 이용해 최적의 가지수를 확인한 결과 4일 때 최적.
msl=4일때 테스팅 성능:0.7741

### 변수 중요도
A2audience(2주간 누적관객수)는 labels(총 누적관객수)와 거의 비슷한 값을 가짐. 그러므로 변수 중요도를 구할때 제외하였음.

중요도 그림

![feature_important](https://user-images.githubusercontent.com/77971228/170222549-43a6ed19-4320-4c9f-b838-3cde5d1f00f0.png)'

## Clustering
### 변수 설정
DT에서 찾은 중요 변수 ' Daudience ', ' A1audience ', ' A2audience ', ' Dscreen ', ' A1screen ', ' A2screen ', ' Actscore '를 변수로 사용. labels는 직관적으로 데이터를 보기위해 카테고리형이 아닌 숫자로 표현.
### Scaling
변수간의 크기차이로 인해 MinMaxScaler를 사용하여 scaling진행.
### 적정 clusters 찾기
cluster=2~9로 하여 inertia와 Silhouette Score 확인
inertia
<img width="80%" scr='[https://user-images.githubusercontent.com/77971228/170224815-308a3d49-231f-4867-95f9-7f386e852b72.png](https://github.com/koeshin/DataMining_teamproject/issues/4#issue-1247798838)'/>
Silhouette Score
<img width="80%" scr='https://user-images.githubusercontent.com/77971228/170225112-03ba0082-001e-40a1-932c-ca0b81b73956.png'/>

이너셔는 클러스터에 속한 샘플이 얼마나 가깝게 모여 있는지를 나타내는 값.
일반적으로 클러스터의 개수가 늘어나면 클러스터 개개의 크기는 줄어들기 때문에, 이너셔 또한 함께 줄어든다. 클러스터가 4일 때 감소 그래프가 꺽이는 것을 볼수있는데 이 지점이후로는 클러스터 개수를 늘려도 클러스터에 밀접된 정도가 크게 개선되지 않고 클러스터의 개수가 많으면 효율이 떨어져서 적정 클러스터는 4라고 판단. 
실루엣스코어에서도 클러스터가 4일 때 값이 3번째로 높았기 때문에 적절하다라고 판단.

Clustering 진행후 시각화
<img width="80%" scr='https://user-images.githubusercontent.com/77971228/170226456-ccfafbd2-5568-4a50-9705-2360f296ade1.png'/>
변수가 7개여서 겹치는 점들이 발생.
### 차원축소 (PCA)
2차원으로 자원 축소 진행
<img width="80%" scr='https://user-images.githubusercontent.com/77971228/170226980-345b03f6-d775-4441-a93b-d9473888cf3f.png'/>

이전 보다 clustering이 잘 됨.
### Cluster별 비율 확인
cluster별 비율 확인을 위해 카테고리형 열을 추가. 중앙값 이하 =0, 중앙값~상위 75%=1, 75%~ =2로 변환.

클러스터 특징:
클러스터 0은 누적관객수 평균이 가장 낮고 전체연령가 비율이 가장 높은 그룹
클러스터 1은 누적 관객수 평균이 가장 높고 청소년 관람 불가가 없고 14일차에 스크린수가 15%가량 감소한 그룹.
클러스터 2는 누적 관객수가 두 번째로 높았고 14일차에 스크린수가 10%가량 감소하였고 누적 관객수가 감소한 그룹.
클러스터 3은 누적 관객수가 3번째로 높았고 전체 연령가 영화가 없고 스크린수 누적관객수가 거의 일정하게 유지된 그룹
