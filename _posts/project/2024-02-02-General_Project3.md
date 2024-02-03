---
layout: post
title:  "[공통프로젝트] 플랫폼 제작 일지 3"
subtitle: 
date: 2024-02-02 06:56:34 +0900
categories: project
tags:
comments: true
related_posts:

---
# [공통프로젝트] 플랫폼 제작 일지 3<br/>

총 개발기간 : 01월 08일 ~ 02월 16일<br/>
<Br/>

## Next.js를 Docker에 올리기<br/>
<br/>

Next.js가 SSR을 지원하고, SSR 기반으로 돌아가기에 index.html같은 결과물이나 엔트리 포인트가 나오지 않는다는 것에 당황했다.<br/>

백엔드, 인프라 측에서 Nginx, Docker을 이용해서 웹에서 구동하도록 만들어야 했다.<br/>

그래서 우선 아래와 같은 조치를 취했다.<br/>
<br/>

### module: export <br/>
<br/>

next.config.js를 아래와 같이 바꿔줬다.<br/>

핵심은 output:'export'를 붙인 것이다.<br/>

```
const withImages = require('next-images');
module.exports = withImages({
    images: {
        disableStaticImages: true,
        domains:[
            'res.cloudinary.com',
            'via.placeholder.com'
        ]
    },
    output: 'export'
});
```

이 기능은 이제 Next.js 프로젝트를 SSR에서 SSG로 바꿔주는 것이다.<br/>

즉, 빌드를 통해서 html코드를 만들어서 Nginx, Docker에서 엔트리 포인트를 찾을 수 있게 해주는 것이다.<br/>

그렇기 때문에 테스트를 하는 방법도 바뀌었다.<br/>

```
npx next build
npx next start
```

빌드를 해서 배포를 하기전에 위와 같은 명령어를 터미널에 입력해서 사이트를 살펴본 뒤에 배포해야했다.<br/>

한가지 편했던 점은 npx next build는 전체를 컴파일 하기 때문에<br/>

어디서 오류가 나는지 발견이 쉽고, 웹 상에서 오류가 나기전에 오류를 잡을 수 있었다.<br/>
<br/>

## 백엔드와 연동<br/>
<br/>

이전에는 prisma를 이용해서 가 db를 만들고, 백엔드 작업까지 JS로 진행해서 올바르게 작동하는지 확인할 필요가 있었다.<br/>

하지만 이제는 Spring으로 만든 백엔드와 본격적으로 연동을 해야 했기에<br/>

prisma, 백엔드(JavaScript)와 관련된 모든 파일을 삭제하였다.<br/>

더 작성 중...