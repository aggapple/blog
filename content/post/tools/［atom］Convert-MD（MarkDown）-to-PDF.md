---
title: "［atom］Convert MD（MarkDown） to PDF"
date: 2022-09-07T16:03:24+09:00
tags: ["atom","MD","MarkDown","PDF"]
categories: ["tools"]
---
####  # Convert MD（MarkDown） to PDF in Atom
***
### 1. Atom에서 MD파일 미리보기 실행
- Windows : Ctrl + Shift + M
- Mac : control + Shift + M
***
### 2. 미리보기창 우클릭 후 "Save As HTML.." 선택
![](/static/images/20220907160324_01.png)
***
### 4. 저장된 HTML을 Chrome에서 열기
***
### 5. Chrome 메뉴에서 "인쇄(P)"를 선택 후 인쇄미리보기창 표시
### 6. 인쇄 대상을 "PDF로 저장"으로 지정 후 저장
![title](/static/images/20220907160324_02.png)
***
### (옵션_1) 페이지 분할 필요 시 html style에 개행 tag 적용
- 페이지 분할 tag 정의
```
<style>
    .break { page-break-before: always; }
</style>
```
- 페이지 분할 tag 사용
```
<body>
    <p class="break"/>
</body>
```

***
### (옵션_2) 링크이동 tag 적용
- 링크 위치 지정
```
<body>
    <a name="Move to here!!">
</body>
```
- 링크 이동 호출
```
<body>
    <a href="#Move to hear!!">Click hear!!</a>
</body>
```
