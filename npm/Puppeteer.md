# 🎁Puppeteer

>크롤링을 위해 제공되는 node의 라이브러리!!!

---

Puppeteer는 노드의 라이브러리이며 크롤링을 할 때 유용하다. 

현재 진행 중인 알고리즘 스터디에서 매일 매일 누가 문제를 풀었고, 풀지 않았는 지를 체크하고 있다. 스터디장을 맡고 계신 분께서 매일 매일 모든 스터디원들의 채점기록을 확인해야하다보니 굉장히 불편하다.

때문에!!! 이런 사용자의 불편함을 파악하고 이를 해결할 수 있는 사이트를 배포해야겠다라는 생각을 했다. Puppeteer로 푼 전체문제의 개수를 크롤링하고 이 데이터와 기존 데이터베이스에 저장해둔 기존에 푼 문제의 개수가 차이가 있으면 해당 사람의 이름 옆에 "오늘 문제를 풀었습니다!" 와 같은 모양의 사이트를 구상했다.

<br/>

**물론 실패했다....** 실패를 한 가장 큰 이유는 '캡챠' 의 존재 때문이었다. 캡챠란 가끔 로그인을 하다보면 로봇을 방지하겠다고 나오는 그림으로 "소화전이 있는 그림을 고르세요.", "버스가 있는 그림을 고르세요." 와 같은 것들이다.

백준 사이트에 접속해서 아이디와 비밀번호를 넣고 클릭하는 것 까지는 만들었지만, 그 이후에 이 캡챠를 통과할 방법이 없다....

다른 분들의 말에 의하면 '딥러닝' 혹은 중국, 인도에서 24시간동안 이를 해결해주는 업체를 통해 해결할 수 있다는데 둘 다 지금의 나로서는 쓸 수 없는 방법들 뿐이었다....

그래도 지금까지 이해한 Puppeteer의 개념이 아깝기도 하고 나중에 또 쓸 일이 있을 것 같아 정리를 해두어야겠다고 생각했다.

---



```javascript
const { ID, PW } = require('./dev')
const puppeteer = require('puppeteer');

const main = async () => {
    try {
        const browser = await puppeteer.launch({
            headless: false
        })
        const page = await browser.newPage()
        #백준 로그인 페이지로 이동한다.
        await page.goto('https://www.acmicpc.net/login?next=%2F')
        #이미 입력해둔 나의 백준 아이디와 비밀번호를 입력한다.
        await page.evaluate((id, pw) => {
            document.querySelector('#login_form > div:nth-child(2) > input').value = id;
            document.querySelector('#login_form > div:nth-child(3) > input').value = pw;
        }, ID, PW)
		#로그인 버튼을 클릭한다.
        await page.click('#submit_button')
 		#이후 랭킹 페이지로 이동하여 크롤링해야 하지만 이 위에서 캡챠 화면이 뜨고 막힌다.
        await page.goto('https://www.acmicpc.net/group/ranklist/10060')
        //await browser.close()
    } catch (error) {
        console.log(error)
    }

}

main()
```

코드 자체가 굉장히 직관적이어서 이해하기 쉽다.

---

## 🎁느낀점

장장 3시간정도 저 짧은 코드를 작성하기 위해 이짓저짓하면서 많은 삽질을 했다. 하지만, 굉장히 의미 있는 삽질이었다. 항상 크롤링을 공부해보고 싶다는 생각을 많이 했었는데 이번 기회를 통해 뭔가 틀을 잡아둔 것 같다. 재미있는 경험이었다.

---

## 🎁참고문헌

[Puppeteer 공식 사이트](https://pptr.dev/)

[네이버 로그인 예제](https://ncube.net/nodejs-puppeteer-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%84%A4%EC%9D%B4%EB%B2%84-%ED%9A%8C%EC%9B%90-%EB%A1%9C%EA%B7%B8%EC%9D%B8/)

[여기어때 예제](https://velog.io/@jinuku/Puppeteer%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9B%B9-%ED%81%AC%EB%A1%A4%EB%A7%81-%ED%95%B4%EB%B3%B4%EA%B8%B0-%EC%98%88%EC%A0%9C-1)

[학교 크롤링 예제](https://zoomkoding.github.io/web/nodejs/histime/2019/01/24/crawler.html)

