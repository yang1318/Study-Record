## Overdraw란?
> **앱이 동일한 픽셀을 다시 그리는 것**  

사용자가 앱을 실행했을 때, 화면이 버벅거리고 반응이 느리면 앱의 품질이 좋지 않다고 느끼게 됩니다.  
Overdraw는 앱이 동일한 픽셀을 여러번 그리게되는 현상을 말합니다.
똑같은 픽셀에 여러번 그릴 필요는 없으니 Overdraw가 있다면 줄이거나 없애서 GPU의 부담을 줄여주는 게 앱 화면이 부드럽게 움직이는 데 도움이 됩니다.


## Overdraw를 확인하는 방법
> **GPU 오버드로 디버그**  

개발자 옵션을 통해 확인할 수 있습니다. 
(만약 개발자 옵션이 안보인다면 먼저 활성화부터 해야합니다.)  

[설정] -> [개발자 옵션] -> [하드웨어 가속 렌더링] 영역의 [GPU 오버드로 디버그]  
여기서 [오버드로 영역 표시]를 선택하면 됩니다.  

(사실 그냥 설정에서 "GPU 오버드로 디버그"를 검색하시면 빨리 찾으실 수 있습니다.)  

| <img src="../images/안드로이드/Overdraw를 확인하는 방법 - 개발자 옵션.jpeg" alt="개발자 옵션" width=180 height=440/> | <img src="../images/안드로이드/Overdraw를 확인하는 방법 - GPU 오버드로 디버그.jpeg"  alt="GPU 오버드로 디버그" width=180 height=440/> | <img src="../images/안드로이드/Overdraw를 확인하는 방법 - 오버드로 영역 표시.jpeg" alt="오버드로 영역 표시" width=180 height=440/> |
|:----:|:----:|:----:|  
  
  

오버드로 영역 표시를 키면 화면이 우측 사진처럼 울긋불긋 해집니다.  

<img src="../images/안드로이드/Overdraw를 확인하는 방법 - 오버드로 킨 화면.png" alt="오버드로 킨 화면" width=700/>  


각 색상이 의미하는 바는 다음과 같습니다.   
    - 원래 색상 : Overdraw 없음  
    - 파란색 : Overdraw 1회  
    - 녹색 : Overdraw 2회  
    - 연한 빨간색 : Overdraw 3회  
    - 빨간색 : Overdraw 4회 이상  

반투명한 색상이기 때문에 화면에 뜬 앱의 색상에 따라 조금 다르게 보일 수 있습니다.


## Overdraw를 줄이는 방법
무조건 발생할 수 밖에 없는 Overdraw도 있지만, 줄일 수 있는 부분은 줄이면 GPU의 부담이 줄어들어 앱이 더 부드럽게 움직이게 될 수 있습니다.  
안드로이드 공식문서에서는 오버드로 디버그 모드를 켰을 때 모든 화면이 원래 색상으로 보이거나 파란색(Overdraw 1회)만 보이는 것을 권장하고 있습니다.  

### Overdraw가 생기는 이유
시스템이 화면에 한 픽셀을 여러번 그리게 되는 이유는 아래 사진처럼 뒤에서부터 겹쳐 그리기 때문입니다.  

![alt text](<../images/안드로이드/Overdraw가 생기는 주요 원인 - 페인터 알고리즘.png>)  

이렇게 그리는 걸 "[페인터 알고리즘](https://en.wikipedia.org/wiki/Painter%27s_algorithm)" 이라고 합니다.  
위 그림에서는 겹쳐서 지워지게 되는 산 부분이 Overdraw 되고, 또 초원에도 나무가 생기면서 초원의 일부분도 Overdraw 됩니다.  
실제 앱에서의 예를 들자면 한 레이아웃에 까만색을 배경색으로 지정했는데, 그 레이아웃을 다 덮는 크기의 레이아웃이 흰색으로 배경색이 지정되어 있다면  
사용자의 눈에는 까만색은 보이지 않지만 시스템은 까만색 -> 흰색 순서대로 픽셀을 두 번 칠하게 됩니다.  

뷰 요소가 투명도가 있는 요소의 경우에도 아래 깔린 뷰와 위에 투명도가 있는 뷰 모두를 다 그리게 되기 때문에 Overdraw 가 발생합니다.

### Overdraw를 줄이는 법
1. **Layout에서 windowBackground로 대체할 수 있는 background 속성 제거** 

    앞서 설명한 예시처럼 background 속성을 이용해 배경을 설정해도 이를 가리게 된다면 이는 불필요한 Overdraw를 발생시킵니다.  

    특히 다른 뷰 요소보다 공식문서에서는 레이아웃을 주의하라고 말하고 있는데요, 레이아웃은 보통 다른 뷰 요소를 위에 쌓아올리는 데 사용되기 때문에 가려지는 면적이 당연히 더 많을 수 밖에 없는 뷰 요소입니다.   
    Layout에 background 속성을 설정하게 되면 Layout이 차지하는 면적 전체를 GPU가 한번 다 색칠하게 됩니다. 그 뒤에 Layout에 올려진 요소들을 색칠하면서 Overdraw가 생길수 밖에 없습니다.  

    Layout의 기본 background는 없는 투명색인데요, 그럼 핑크색 처럼 다른 색으로 배경을 색칠하고 싶다면 그때의 Overdraw는 어쩔 수 없는 선택일까요?  

    아닐 수 있습니다. 만약 앱의 주요 배경색이 분홍색이고, 하나의 Layout 뿐만 아니라 대부분의 Layout이 분홍색으로 배경색을 가져야한다면 themes.xml의 style 속성 중 `android:windowBackground` 를 이용해 기본 배경색을 변경할 수 있습니다.  
    windowBackground를 이용해 기본 배경색을 변경한다면 Overdraw가 발생하지 않습니다.  

    Layout Inspector를 이용하면 레이아웃의 계층구조를 한눈에 볼 수 있어 사용자에게 보이지 않는 배경을 쉽게 제거할 수 있습니다.  

2. **뷰 계층 구조가 겹치지 않게 평면화**  

    여러개의 뷰 객체가 겹쳐있다면 보이는 픽셀과 보이지 않는 픽셀을 모두 화면에 그려야하게 됩니다.  
    이를 평면화하면 성능을 향상시킬 수 있습니다. 

3. **투명도 요소를 줄이기**  

    화면에 투명한 픽셀을 그리는 것은 Overdraw 주요 원인입니다.  
    불투명한 픽셀이 겹쳐있는 경우는 제일 상위의 픽셀만 그리도록 한다면 원하는 결과가 나오게 되지만,  
    투명한 픽셀은 기존에 그려져 있는 픽셀들이 보여져야하므로 Overdraw를 줄일 수 없게 합니다.  

    만약 투명도가 있는 검은색을 써서 회색을 만드는 것 대신에 그냥 회색을 사용할 수 있다면 성능을 향상시킬 수 있습니다.

> 출처  
    - [안드로이드 공식문서 - Rendering](https://developer.android.com/topic/performance/rendering)  
    - [안드로이드 공식문서 - Inspect GPU rendering speed and overdraw](https://developer.android.com/topic/performance/rendering/inspect-gpu-rendering)  
    - [안드로이드 공식문서 - Reduce overdraw](https://developer.android.com/topic/performance/rendering/overdraw)  
