---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_6"
subtitle: 
date: 2022-03-31 09:01:25 +0900
categories: project
tags:
comments: true
related_posts:

---
# [Github blog] 블로그 테마 업그레이드하기_6<br/>

## Page Build Failure 문제의 해결<br/>
<br/>

지난 포스팅에서 Page Build Failure가 발생한 뒤, 관련 내용을 구글링하여 해결의 실마리를 찾을 수 있었다.<br/>

해결의 실마리는 [이곳](https://velog.io/@shg4821/%EA%B9%83%ED%97%88%EB%B8%8C-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-1.5)에서 찾았다.<br/>
<br/>

### 뭐가 문제였던 것인가?<br/>
<br/>

결론부터 말하면 **_config.yml파일의 문제**였다.<br/>

[깃허브 페이지의 안내사항](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/troubleshooting-jekyll-build-errors-for-github-pages-sites)을 읽어보니 주로 문제의 원인은

- 지원되지 않는 플러그인을 사용했거나
- 용량이 크거나 (권장용량 최대 1~5GB)
- _config.yml 파일에 문제가 있거나
- 파일이름에 콜론을 사용한 문제거나

위와 같은 4가지 문제라고 했는데 필자는 3번째 문제에 해당하였다.<br/>
<br/>

### 해결 방법<br/>
<br/>

[YAML-Validator](https://codebeautify.org/yaml-validator)에서 검사를 해보아도 파일 문법 자체에는 이상이 없었다.<br/>

**그래서 _config.yml 자체를 엎었다.**<br/>

지금 쓰려고 한 파일은 백업해두고, 예전 버전에서 쓰던 _config.yml 파일을 가져와서 push해보았다.<br/>

5분 뒤 그 결과...<br/>

![경과](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-30-project_1.jpg?raw=true)

몇 시간을 기다려도 되지 않던 페이지 빌드가 진행되었다!<br/>

하지만 테마는 9.1 버전이지만 config파일이 이전 버전이기 때문에 호환성이 문제가 있는 것 같다.<br/>

현재 포스트의 이전글, 다음글 버튼이 로컬서버에서는 의도한 대로 잘 나오지만 웹에서는 CSS가 반영이 잘 안된 것인지 이상하게 나온다.<br/>

100% 완벽하지는 않은 것 같다.<br/>

이 부분에 대해선 _config.yml파일을 조금씩 고쳐나가면 될 것이다.

백업해둔 파일과 현재 쓰는 파일을 비교해보면서 복사 붙여넣기를 해보자.<br/>
