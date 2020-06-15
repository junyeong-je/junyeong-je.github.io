---
title: "AWS CodeCommit Migration, data transfer"
layout: post
category: Migration
tags: [AWS, CodeCommit, Github]
---


## 개요

Git에서 CodeCommit으로 이전하거나, CodeCommit에서 CodeCommit으로 이전하더라도, 전체 리포지토리를 한번에 옮기는 방법은 찾지 못했습니다.<br>
이 문서에서는 하나의 git 레포지토리를 이전합니다.

## 상세사항

![Git 리포지토리를 CodeCommit으로 마이그레이션](https://docs.aws.amazon.com/ko_kr/codecommit/latest/userguide/images/codecommit-migrate-existing.png)
레포지토리 이동은 위 이미지와 같은 순서로 처리합니다.

\#\# clone복제 시 옵션이 존재하는데 차이점에 대해서는 아래 링크에서 자세히 다뤄놓아 확인해보시길 바랍니다\.<br>
[https://pinocc.tistory.com/138](https://pinocc.tistory.com/138)

\#\# Git 클론 가져오기

```
git clone --mirror {레포지토리URL}
```

ex) git clone --mirror [https://git-codecommit.us-west-2.amazonaws.com/v1/repos/KapaTraining](https://git-codecommit.us-west-2.amazonaws.com/v1/repos/KapaTraining)

\#\# 신규 리포지토리로 밀어넣기

```
git push {레포지토리URL} --all
```

ex) git push [https://git-codecommit.ap-northeast-2.amazonaws.com/v1/repos/roboland](https://git-codecommit.ap-northeast-2.amazonaws.com/v1/repos/roboland) --all

## 참고문서

* [https://docs.aws.amazon.com/ko\_kr/codecommit/latest/userguide/how-to-migrate-repository-existing.html](https://docs.aws.amazon.com/ko_kr/codecommit/latest/userguide/how-to-migrate-repository-existing.html)
* [https://pinocc.tistory.com/138](https://pinocc.tistory.com/138)
* [https://docs.aws.amazon.com/ko\_kr/codecommit/latest/userguide/getting-started.html](https://docs.aws.amazon.com/ko_kr/codecommit/latest/userguide/getting-started.html)
* [https://zeddios.tistory.com/5](https://zeddios.tistory.com/5)