---
title: "[메모] Jupyter Notebook 사용자 설정"
categories: note
tags: memo jupyter-notebook
toc: true
toc_sticky: true
---

Jupyter Notebook 커스터마이징

## 글자 크기 변경

셀에 다음과 같은 html 코드 입력 후 실행

```shell
%%html
<style type='text/css'>
.CodeMirror{
  font-size: 14px;
}
</style>
```

기본 사이즈가 14px이고 글자 크기 키우고 싶으면 이보다 큰 값으로 바꾸면 됨.
