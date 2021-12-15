---
layout: post
title:  "Web-Rendering"
date:   2021-05-27 23:10:12 +0900
categories: Web
overview: "웹을 렌더링하는 방식의 차이를 정리한 글"
---
웹 렌더링의 방식과 종류

### Static Site

---

서버에 잘 만들어진 HTML 문서들이 존재하고, 사용자가 브라우저에서 주소를 입력하면 서버에 이미 배포되어져 있는 HTML 문서를 보여준다.

- 다른 링크를 클릭하면 페이지 전체가 업데이트 된다
- 'iframe'태그가 생기면서 페이지 내에서 부분적으로 문서를 받아와서 업데이트 할 수 있게 됨
- 이후에 'XMLHttpRequest'가 개발이 되면서 HTML문서 전체가 아니라 JSON과 같은 포맷으로 서버에서 필요한 정보만 받아올 수 있게 되었다
- 이러한 방식이 AJAX 라는 이름을 갖게 되고, SPA가 개발되었다

### SPA

---

Single Page Application

사용자가 한 페이지 내에 머무르면서 필요한 데이터를 서버에서 받아 와서 부분적으로만 업데이트 한다. 이런 방식으로 하나의 애플리케이션을 사용하듯 웹을 구성한다.

- SPA는 기본적으로 필요한 모든 정적 리소스를 최초에 한번 다운로드 한다.
- 이후 새로운 페이지 요청 시 페이지 갱신에 필요한 데이터만을 전달받아 페이지를 갱신하므로 전체적인 트래픽이 감소한다.
- 전체 페이지를 다시 렌더링하지 않고 변경되는 부분만을 갱신하므로 새로고침이 발생하지 않는다.
- Client Side Rendering 방식을 사용한다

### MPA

---

Multi Page Applicaton

- 검색엔진 최적화 관리에 매우 적절하며 쉽다.
- 프론트 엔드와 백엔드 개발이 강하게 결합되어 있다.
- 개발이 복잡하게 된다. 개발자들은 클라이언트와 서버사이드 모두에 대해 프레임워크들을 사용해야 한다.
- MPA는 웹을 구성하는 전통적인 방법으로, Server Side Rendering 방식을 사용해왔다.

![../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr%202.png](../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr%202.png)


### CSR

---

Client Side Rendering

서버에서 인덱스라는 HTML파일을 클라이언트에 보내주고, 애플리케이션에서 필요한 자바스크립트의 링크만 들어가 있다.

- HTML은 정보가 적기 때문에 처음에 접속하면 빈 화면만 보인다
- 자바스크립트에는 로직들 뿐만 아니라 프레임워크와 라이브러리의 소스코드들이 다 포함되어 있다
- 사이즈가 커서 다운로드 받는 데 시간이 소요된다
- 추가로 필요한 데이터가 있다면 서버에 요청해서 데이터를 받아온(JSON) 다음 이것들을 기반으로 동적으로 HTML을 생성함
- SSR보다 초기 전송되는 페이지의 속도는 빠르지만 서비스에서 필요한 데이터를 클라이언트에서 추가로 요쳥하여 재구성해야하기 때문에 전체적인 페이지 완료 시점은 SSR보다 느리다
- SEO가 좋지 않다

### SSR

---

Server Side Rendering

- 서버에서 사용자에게 보여줄 페이지를 모두 구성하여 사용자에게 페이지를 보여주는 방식이다.
- CSR보다 페이지를 구성하는 속도는 늦어지지만 전체적으로 사용자에게 보여주는 콘텐츠 구성이 완료되는 시점은 빨라진다.
- 일반적으로 빠른 FP (First Paint - 픽셀이 처음으로 사용자에게 표시되는 시점)과 FCP (First Contentful Paint - 요청 콘텐츠가 표시되는 시점)를 생성한다
- JavaScript를 서버에서 구성하므로 빠른 TTI(Time to Interactive - 요청 콘텐츠가 표시되는 지점)를 수행한다.
- 느린 TTFB(Time To First Byte - 링크를 클릭한 후 처음으로 들어오는 콘텐츠 비트 사이의 시간)로 사용자가 상대적으로 느리다고 느낀다.
- 서버에 과부하가(계산 오버헤드) 올 수도 있다.
- 웹이 보이기는 하지만 반응은 하지 않는 상태가 존재한다.
- SEO가 효율적이다

![../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr.png](../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr.png)

이 실험은 특정 페이지의 홈페이지, 카테고리 페이지, 서치 페이지의 렌더링 방식에 시간별 작업 진행 상황 나타낸다.  SSR이 CSR에 비해 많은 양의 정보가 구성되어 있음을 볼 수 있다.

![../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr%201.png](../../../../../public/assets/2021-05-27-Web-Rendering/ssr_csr%201.png)

SSR의 경우 서버에서 페이지가 모두 구성되어서 넘어오기 때문에 용랑이 더 크다. 위 예시의 경우 SSR이 더 빠르게 나타나지만, 이 페이지만의 특징이라고 필자는 설명한다.

### SSG

---

Server Side Generation

화면을 서버에서 미리 만들어 전송해주는 기법

- 페이지 생성을 요청을 받고서 하는 것이 아닌 빌드 시간에 페이지를 렌더링한다.
- 새 컨텐츠나 구성요소를 추가하려면 사이트를 다시 빌드해야 한다
- 사이트의 모든 페이지와 콘텐츠가 빌드 시간에 생성되었으므로 클라이언트는 거의 즉시 콘텐츠를 볼 수 있다. 또한 서버에 대한 API 호출에 대해 염려할 필요가 없으며, 이로 인해 사이트가 매우 빨라진다
- 서버 렌더링 과정에서 데이터를 가져오는데 필요한 모든 요청을 수행하므로 클라이언트에서 추가 서비스 호출을 수행하지 않는다. 사용자가 페이지를 가지고 놀지 않는 한에서.
- SEO에 적합하다
- 서버를 실행할 필요가 없다. 서버 모니터링이 필요없다.

### SSR with Hydration

---

Hydration 아키텍쳐에서는 첫 웹페이지 렌더는 SSR로 이루어지지만 그 이후부터 전환될 모든 페이지는 CSR로 이루어진다. 특히 이후에 이루어지는 이벤트 리스너 등록 작업 등을 Hydrate라고 부른다. 이런 작업은 원래 개발자가 직접해야하는 것이지만, 개발자가 신경 쓸 필요가 없도록 구현되어 있는 프레임워크가 개발되었다. Next.js 와 Nuxt.js 등이 그렇다.

- 클라이언트가 서버에서 렌더링 한 HTML의 DOM 트리와 데이터를 재사용하도록 자바 스크립트 뷰를 "부팅"한다.

### Prerendering

---

Prerendering은 빌드 타임에 모든 HTML을 렌더링한다. 이미 렌더링 되어있으므로 SSR하는데 걸리는 시간이 필요하지 않아 FCP는 더욱 빨리질 것이다. 쉽게 말해 정적으로 페이지를 렌더링하는 것이고 유명한 툴로는 Gatsby가 있다.  단점은 빌드 타임에 모든 페이지 생성을 끝내야 하기 때문에 항상 정적인 컨텐츠에만 활용이 가능하다

- 빌드 타임에 클라이언트 측 애플리케이션을 실행하여 초기 상태를 정적 HTML로 캡처한다.

- **TTFB:** Time to First Byte (첫 번째 바이트까지의 시간) - 링크를 클릭한 후 처음으로 들어오는 콘텐츠 비트 사이의 시간
- **FP:** First Paint - 픽셀이 처음으로 사용자에게 표시되는 시점
- **FCP:** First Contentful Paint - 요청 콘텐츠(기사 본문 등)가 표시되는 시점
- **TTI:** Time To Interactive - 페이지가 상호작용 가능하게 될 때까지의 시간 (이벤트 발생 등)

![https://developers.google.com/web/updates/images/2019/02/rendering-on-the-web/infographic.png](https://developers.google.com/web/updates/images/2019/02/rendering-on-the-web/infographic.png)

처음에 드는 비용을 감안할 수 있다면 SSR을 사용하지 않을 이유가 없다. -Naver D2

[Gray Area on When to use Different Rendering Modes CSR, SSR, SSG](https://kirillibrahim.medium.com/gray-area-on-when-to-use-different-rendering-modes-csr-ssr-ssg-214a636a24a4)

[Google I/O 2019: Day 3 후기](https://hyunseob.github.io/2019/05/26/google-io-2019-day-3/)

[The Benefits of Server Side Rendering Over Client Side Rendering](https://medium.com/walmartglobaltech/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8)

[Rendering on the Web | Google Developers](https://developers.google.com/web/updates/2019/02/rendering-on-the-web)

[NAVER D2](https://d2.naver.com/helloworld/7804182)

[https://www.youtube.com/watch?v=iZ9csAfU5Os](https://www.youtube.com/watch?v=iZ9csAfU5Os)

### Amazon 현직자의 유튜브 댓글

---

```
개인적으로는 어떤 방식이라도 HTML을 받아와야 어떤 JS가 필요한지 브라우저가 인식하고 그걸 다시 받아오는 과정이 결국엔 사용자 컴퓨터 성능과 네트워크 환경에 영향을 받는게 성에 안차서... ServiceWorker를 적극 활용하고 있습니다. 어차피 모듈화된 JS들은 빌드시에 이름과 디렉토리를 전부 인지하고 있기에 첫 스크립트 로딩직후에 ServiceWorker로 미리 받아와서 캐싱해두는 형태로요.

SSG를 쓰면 그래도 랜더링 로직이 최소화되면서 스크립트 다운로드 + 로딩 시간이 짧아지긴 하지만... 어플리케이션이 커지고 인터렉티브 요소가 많아지면 CSR이나 SSR대비 큰 차이를 느끼긴 힘들더라구요. CI/CD환경 구축시점에서도 빌드에 많은 리소스가 요구되는 SSG의 특성상 개발 파이프라인에 빌드용 클라우드 컴퓨터가 따로 있는데 이게 빌드용으로만 쓰이는거라... 성능이 좋지도 못해서 디플로이까지 시간이 오래 걸리기도 하고 ㅠㅠ

전 보통 로그인이 필요없는 페이지는 SEO때문에 SSR로, 로그인이 필요한 페이지는 어차피 SEO에 영향을 안받으니까 CSR로 만든후에 ServiceWorker를 붙이는 방식으로 만들고 있습니다.
```
### SSR 개발하고 느낀점

---

SSR을 사용하여 Authenticated 과정을 진행하는 법이 어렵다.
CSR의 경우 token을 localstrage에 저장하여 사용할 수 있었으나 SSR의 경우 localstorage를 사용할 수 없다.
SSR의 경우 browser에서 실행되는 것이 아니라 api에서 실행되기 때문에 렌더링이 끝날 때까지
window, document, navigator, location 같은 객체를 사용할 수 없다.
rendering 된 후에는 사용할 수 있었다.

Cookie의 경우도 HTTP 브라우저에서 사용하기 때문에 사용할 수 없었다.

대안으로 firebase 라는 툴이 있다는 것을 확인 했고 다음 개발에 사용해 볼 예정이다.

### 같은 결과의 css와 js를 적용한 후 웹 서버를 CSR과 SSR로 구현한 경우의 차이

---

CSR 방식으로 웹에 렌더링 된 후 개발자 도구로 확인한 모습

```
[truncated]<!DOCTYPE html><html><head><link href="/js/app.6029ce48.js" rel="preload" as="script"><link href="/js/chunk-vendors.42656474.js" rel="preload" as="script"></head><body><div id="app"></div><script src="/js/chunk-vendors.4265647
```

CSR 방식의 경우 최초 접속시 JS및 Static file을 다운로드 받아서 클라이언트에서 사용한다

SSR 방식의 경우

```
<span style="color:black; font-size:40px"> server side rendering : page A</span>
```

SSR 방식의 경우 JS 및 Static file을 서버에서 렌더링하여 반환한다