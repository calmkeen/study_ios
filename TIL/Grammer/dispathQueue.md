# dispathQueue / GCD

데드락 즉 큐를 사용하다보면 데드락이라는 교착상태에 빠질 경우가 있는데.

단순히 에러로 에..? 데드락이네..이게 뭐시여..… google 살려줘!  이러다가 조사하게 되었습니다~

간단하게  dispathquque는 대부분 써보셨다는 가정에 설명드리겠습니다.

스레드 단위에서 GCD가 업무가 하나의 쓰레드에 몰리는 걸 막기위해서 ,동기, 비동기 방식으로 (sync,async)업무를 큐로 나누어 주는것.? 정도로 알고 계신걸 조금더 깊이 보겠습니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/431647ef-51c4-430d-bb39-1ab3bc1bcbda/Untitled.png)

GCD(Grand Central dispatch) ??

- 큐에 작업을 보내면 쓰레드에 적절히 분배하준다.

dispathquqeue에는 어려 가지 종류가 있는데. 대부분 2가지 정도로 알고 계시지 않나 싶습니다.(동기/비동기제외)

- concurent Queue
- serial Queue

- Serial ( 직렬 )은 직렬 이라고 하는데 굉장히 잘 정리한 블로그가 있어 가져와 봤습니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0fca357f-8169-4457-8059-7dd52c3d11fd/Untitled.png)

이렇게 되면 장단점이 명확해 집니다. 메인에서 던진 작업들을 단 하나의 스레드에서 작업하기 때문에 순서도 명확하고 하나씩 명확하게 작업을 알수 있습니다. 하지만 반대로 업무가 과다하다면 언제 끝나는지 영영 기다림이 될수 도 있습니다.

- 이제 Concurrent( 동시 ) 라면 어떨까요

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77c250ac-5286-4aa1-aed5-80ede926ed51/Untitled.png)

이렇게 되면 장점은 여러 업무를 분산해서 처리하다 보니 결국 같은 속도라면 당연히 빠르겠죠?( 뇌피셜 아마맞음)

하지만 이렇게 되면 quque가  순서대로 가는 방식이지만 끝나는 시간은 알수가 없습니다. 이럼 데이터 로드같은 부분에 있어서 순서가 뒤죽밖죽이겠죠?

그리고 ios에서 지원하는 3가지 큐는 MainQ, globalQ, customQ가 있습니다.

 Main Queue 는 1개이며 이곳에는 MainThread가 직접 일을 처리해줍니다. 주로 UI를 처리하게 하죠

(메인 큐는 serial임)

이제 글로벌 큐는 Concurrent를 기본적으로 세팅되어있는 큐입니다. (qos: )를 처리하는 곳이 여기입니다.

그리고 customQueue 애는 설정을 따로 건드리지 않는 한 serial입니다.

## 주의사항

1. 메인 쓰레드에서 sync로 보낼때…

```jsx
DispatchQueue.global().sync{

}
```

바로 에러가 뜰텐데 이제 생각을 해봅시다. (sync는  작업이 끝날때까지 대기하고? 다음작업을 수행하죠?)

그런데 여기서 메인은 ui를 처리한다고 했습니다. 이상하지 않나요? 이렇게 되면 다른작업이 끝날때까지 ui가 늦게 뜹니다…. 하..? 화면이 버벅인다는 거죠…

1. 데드락 상황

이제 데드락이라는 경우는 process, thread가 서로 작업이 끝나기만 기다려서 task를 처리하지 못하는 경우를 말합니다.

```jsx
//task 1
DispatchQueue.global().async{
//task 2
	DispatchQueue.global().sync{
	}	
}
```

보시면 같은 글로벌 큐에 sync로 보내는 코드가 있습니다.

task a에서 글로벌로 큐를 보냈습니다. → 쓰레드에 할당이 되서 수행이 될겁니다.그런데 이  task 1안에 글로벌 큐에 보내는 작업이 또있습니다. sync로요. Task 2가 쓰레드에 넣어져 동작이 될때까지..task 1은 멈춰있습니다..sync니까요.  이제… task 2를 처리하러  GCD가 일을합니다… 그런데 같은 쓰레드에 할당이 된다면..? task1을 배정된 그 멈춘 쓰레드는… 멈춰있는 스레드니까… 이제 데드락이 된겁니다.어떻게 된거냐면 ….qos에 따라서 큐가 보내는곳이 정해져 있는데.. 이렇게 되면 데드락이 발생할수 있게 되는 겁니다…

위와 비슷한 예로 메인에 sync쓰면 또 직렬에 sync니까 …비슷하겠죠?

이제 이런걸 피하려면 …컴플리션 핸들러를 지정해주던지 ..클로저에콜백을 넣어주면 됩니다..

아니면 큐를 생성할때  dispathQueue( attribute: .concurrent)  로 큐를 변경해주시면 됩니다~

아니면 회피 알고리즘, mutex같은 방식으로 도망가면 된다네요! 뮤텍스라는 건 쓰레드가 임계영역에 들어갈때 잡아두었다가 나갈때 뮤텍스 해제같은 방식으로 쓰레드 작업을 볼수 있나 봐요! semaphore 라는것도 있다네요!

참고 블로그 : https://sujinnaljin.medium.com/ios-%EC%B0%A8%EA%B7%BC%EC%B0%A8%EA%B7%BC-%EC%8B%9C%EC%9E%91%ED%95%98%EB%8A%94-gcd-2-a65e1c28665d
