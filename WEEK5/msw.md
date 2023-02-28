# MSW(Mock Service Worker)

- [ ] Service worker
- [ ] MSW(Mock Service Worker)
- [ ] polyfill(폴리필)

오프라인일 때 서버를 쓰는 것을 지원함

어떠한 주소로 뭔갈 요청했을 때 중간에 가로채서(프록시) 결과를 만들 때 사용 가능함.

테스트 환경(Node.js 기반) 외에 웹 브라우저도 지원하기 때문에, API 스펙은 나왔지만 아직 구현되지 않은 경우 임시로 사용할 수도 있다. 단순히 임시 서버를 만들 거라면 Express를 쓰는 게 더 낫지만, 테스트 코드도 지원하면서 겸사겸사 웹 브라우저를 지원하는 용도로는 나쁘지 않은 선택이다.

하지만 이모든 것도 다 진짜 같은 테스트이기 때문에 진짜 테스트도 필요하다.

실제 E2E 테스트는 다음 장에서 배울 Playwright로 한다.