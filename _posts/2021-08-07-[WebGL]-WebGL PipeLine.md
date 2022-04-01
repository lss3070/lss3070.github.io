---
title: "[WebGL] WebGL PipeLine"
subtitle: "기초지식"
layout: post
auther: "Hux"
header-style: text
catalog: true
tags:
    Javascript
    React
    WebGL
---

최근 면접을 몇번 보며 여러가지 질문을 받아봤는데 그중에서 말문이 턱 막히게 되는 질문이 있었다. WebGL의 PipeLine에 대해서 설명해보란 질문이였는데 매번 3D 렌더링에 관련된 프로젝트를 진행 할떄마다 three.js란 라이브러리의 도움을 받아 초기 렌더링을 했었어서 내부 렌더링과정에선 신경쓰지 않고 넘어갔었었다. 결국 그 면접과정에서 질문의 답은 못했지만... 면접보고 나서 렌더링과정에 대해 자세하게 공부해보지 못한게 후회가 되어 이렇게 WebGL의 pipeline에 대해 정리해본다.(다행히 기술면접은 합격이였다^^)

PipeLine과정을 알아보기에 앞서 먼저 웹에서 3d렌더링을 띄어주는 WebGL에 대해 간단히 알아보자.

WebGL이란?
===

WebGL은 웹 기반의 그래픽 라이브러리이다. 자바스크립트 프로그래밍 언어를 통해서 사용할 수 있으며 호환성이 있는 웹 브라우저에서 인터랙티브한 3D 그래픽을 사용할 수 있도록 제공된다.
또한 WebGL은 OpenGL ES 2.0을 기반으로 하고 3차원 그래픽을 사용하기 위한 프로그래밍 인터페이스를 제공한다. WebGL은 HTML5 캔버스 요소를 사용하고 문서 객체 모델 인터페이스를 사용해서 액세스할 수 있다. 자바스크립트 언어의 일부로서 자동 메모리 관리자가 제공된다.

WebGL Pipe Line
===

대부분의 그래픽 카드는 그래픽 처리 과정을 3차원 사영, 윈도 클리핑, 셰이딩, 렌더링 등으로 나누어 각각의 하부 모듈에서 병렬적으로 수행한다.

![pipeline]({{site.url}}/img/webgl/webgl_pipeline.jpeg)

1.JavaScript
---
WebGL이 만들어지는 동안 GPU와 통신하기 위해 Javascript에서는 Shader언어코드를 작성합니다
그 과정으로는 아래와 같습니다.

    1. Js를 이용해서 WebGL context를 초기화 해준다.
    2. 지오메트리 데이터가 저장될 객체를 만듭니다.
    3. 위의 지오메트리 배열 데이터를 통해 버퍼 객체를 만듭니다.
    4. Js를 이용하여 셰이더를 만들어 줍니다.
    5. 3d에 사용되는 객체들을 만든다음(ex. mesh) mesh의 속성에 버퍼 데이터를 넣어줍니다.
    6. Js를 이용하여 유니폼과 연결시켜줍니다.
    7. Js를 이용해서 매트릭스를 만들어 줍니다.
    

처음에는 필요한 지오메트리에 대한 데이터를 만들어 버퍼 형태로 셰이더에 전달합니다. 셰이더 언어의 속성 변수는 버퍼 객체를 가리키며, 버퍼 객체는 정점 셰이더에 입력으로 전달됩니다.

현실에서 어떤 물체를 보기 위해선 빛이 필요하다.WebGL상에서도 이건 마찬가지이며 객체가 존재해도 빛이 없으면 객체를 볼수 없다. 그러니 빛이 객체를 비추는 방법을 생각해봐야한다.
객체가 빛에 반사되어 나타나는 광원효과를 처리하는걸 셰이딩이라고 한다.


2.버텍스 셰이더
---
drawElement()와 drawArray() 메서드를 호출하여 렌더링 프로세스를 시작하면 버텍스 버퍼 객체에 제공된 각 정점에 대해 버텍스 셰이더가 실행됩니다. 원시 폴리곤의 각 버텍스의 위치를 계산하여 다양한 gl_position에 저장합니다. 또한 색상, 텍스처 좌표 및 일반적으로 버텍스와 연관된 버텍스와 같은 다른 속성도 계산합니다.

3.프리미티브 어셈블리
---
각 버텍스의 위치와 세부정보를 계산하고 난 후 데이터는 삼각형으로 조립되어 레스터화된다.

4.레스터화(단편화)
---
레스터화 단계에는 컬링단계와 클리핑단계 두가지가 있다.
Culling(컬링) - 폴리곤 방향을 결정해주고, 뷰 영역에 표시되지 않는 모든 삼각형들이 삭제됩니다.
Clipping - 삼각형의 일부분이 뷰 영역 외부에 있으면 그 일부분만 제거가 됩니다.


5.플래그먼트 세이더
---
플레그먼트 셰이더는 다음 3가지 값을 가집니다.
* 버텍스 셰이더의 다양한 값
* 레스터화의 원시 요소 값
* 버텍스 사이의 각 픽셀에 대한 색상값을 계산합니다.

플래그먼트 셰이더는 각 조각의 모든 픽셀의 색상값을 저장합니다. 이 색상 값은 플레그먼트 작업중에도 액세스 할 수 있습니다.

6.플래그먼트 작업
---
원시 자료에선 각 픽셀의 색상을 파악한 후 플래그먼트 작업을 수행합니다.(깊이,색상 버퍼 혼합,디더링을 포함합니다.)
위의 모든 작업이 처리되면 2D 영상이 형성이 되고 화면에 표시됩니다.


![pipeline]({{site.url}}/img/webgl/fragment_operations.jpeg)

프레임 버퍼
---
프레임 버퍼는 씬(scene) 데이터를 보관하는 그래픽 메모리의 일부입니다. 이 버퍼에는 지표면의 너비 및 높이(픽셀), 각 픽셀의 색상, 깊이 및 스텐실 버퍼와 같은 세부 정보가 포함되어 있습니다.


참고
---
https://ko.wikipedia.org/wiki/WebGL
https://www.tutorialspoint.com/webgl/webgl_graphics_pipeline.htm
https://webglfundamentals.org/webgl/lessons/ko/webgl-fundamentals.html