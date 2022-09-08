---
title: "［hugo］Hugo로 Github Pages에 Contents 배포하기"
date: 2022-09-07T14:00:12+09:00
tags: ["tools","hugo","github","github-pages","contents"]
categories: ["tools"]
---
####  # Distributing contents to Github-pages with Hugo
***
### 1. Contents 생성 및 public에 반영하기
```
# contents 생성
> hugo new post/Demo-post.md

# 생성된 contents를 public에 반영하기
> hugo -t PaperMod
```
- Contents 생성 후 draft: true 로 설정할 경우 hugo server -D 에서만 표시되므로 배포 시 삭제 또는 false로 변경

### 2. Contents 배포하기(수동)
```
# public directory로 이동
> cd public

# public 배포
> git add .
> git commit -m 'commit message'
> git push origin domain

# project root directory로 이동
> cd ..

# project root 배포
> git add .
> git commit -m 'commit message'
> git push origin master
```

### 3. 사이트에서 contents 반영 확인
- 사이트 반영까지 약 1분정도 소요
