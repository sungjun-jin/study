## bcrypt란
> 1999년에 Niels Provos와 David Mazieres가 발표한 가장 강력한 단방향 비밀번호 해시 매커니즘 중 하나이다. C, C++, C#, Go, Java, PHP, Perl, Python, Ruby등의 언어를 지원한다.

### 단방향 해시 함수

![](https://images.velog.io/images/sungjun-jin/post/9b929c3b-ed64-4db8-92aa-3b73c05cf5f5/image.png)

> 단방향 해시 함수는 수학적인 연산을 통해 원본 메세지를 변환하여 암호화된 메세지인 digest를 생성한다. 이 방식으로 암호화된 digest는 다시 평문으로 볼 수 없다. 

하지만 위와같은 단방향 해시 알고리즘은 다음과 같은 문제점이 있다.

**1. 동일한 메세지가 동일한 digest를 생성하는 경우. 즉, 똑같은 비밀번호가 똑같은 해시값을 생성하는 경우**

비밀번호별로 해당 digest(해시값)을 미리 저장한 테이블을 만들어 원본 비밀번호를 역추적하여 사용자의 비밀번호를 알아 낼 수 있다. 이와 같은 digest 목록을 담고 있는 테이블을 rainbow table이라고 하고 이 공격 방식을 rainbow attack이라고 한다.

**2. 해시 함수의 빠른 처리 속도**

사실 해시 함수는 짧은 시간에 데이터를 검색하기 위해 설계된 것이다. 따라서 공격자가 마음만 먹으면 임의의 digest를 대량으로 생성하여 사용자의 암호가 해시된 digest와 빠른 속도로 비교하여 원래의 비밀번호를 알아낼 수 있다.

[참고링크](http://wiki.hash.kr/index.php/%EB%A0%88%EC%9D%B8%EB%B3%B4%EC%9A%B0_%ED%85%8C%EC%9D%B4%EB%B8%94)

### 솔팅 & 키 스트레칭

위와 같은 해시 함수들의 단점들을 보완하기 위해 솔팅(salting)이라는 기법과 키 스트레칭(key stretching) 기법이 사용된다. 

**솔팅**

단방향 해시 함수에서 추가적으로 임의 길이의 문자열을 생성하여 digest를 생성하는 방법을 솔팅이라고 한다.

![](https://images.velog.io/images/sungjun-jin/post/cd6dfa9b-2968-4dee-9d25-e9cf1bda6c9c/image.png)

평문의 `redfl0wer`를 알아내더라고 솔팅된 digest를 대상으로 패스워드 일치 여부를 확인하기 어렵다. 

일반적으로 모든 비밀번호가 랜덤한 고유의 솔트값을 가지고, 솔트값의 길이는 32 바이트 이상이어야 솔트와 digest를 추측하기 어렵다고 한다.

**키 스트레칭**

입력한 비밀번호의 digest를 생성하고, 생성된 digest를 입력 값으로 다시 digest를 계속 반복 생성하는 방법이다. 이 방법을 사용하면 공격자가 입력한 패스워드를 동일한 횟수만큼 해시해야만 digest의 일치 여부를 확인할 수 있다.

![](https://images.velog.io/images/sungjun-jin/post/550628d5-caf1-412b-9610-e6f3e606e484/image.png)

아래에 password 목록은 프로젝트때 임의로 생성한 가짜 사용자의 비밀번호 digest 값들이다. 앞부분에 공통적인 특성을 가지고 있다. 

![](https://images.velog.io/images/sungjun-jin/post/0ad1bf94-17d7-4886-8dbc-c56e0aeee931/image.png)	

+ $2b는 bcrypt의 버전정보이다.
+ $12는 12 round를 의미하며 2^12번 만큼 키 스트레칭을 돌린 것이다.
+ 나머지 부분은 salt + 암호문으로 구성되어 있다.

### 그 외
bcrypt를 제외한 비밀번호 단방향 해시 알고리즘의 종류는 다음과 같다.

**1. PBKDF2(Password-Based Key Derivation Function)**
PBKDF2는 NIST(National Institute of Standards and Technology, 미국표준기술연구소)에 의해서 승인된 알고리즘이고, 미국 정부 시스템에서도 사용자 패스워드의 암호화된 digest를 생성할 때 사용한다.

+ 해쉬 컨테이너 알고리즘
+ 사람이 기억할 수 있는 암호 기반으로 랜덤키 생성
+ 입력한 암호 기반으로 salt를 추가 정해진 횟수 만큼 해시 수행
+ 가볍고 구현하기 쉽다는 장점이 있고 검증된 해시 함수만을 사용한다

**2. scrypt** 
PBKDF2와 유사한 Adaptive Key Derivation Function이며 Colin Percival이 2012년 9월 17일에 설계되었다. 다른 해시 알고리즘에 비해 많은 양의 메모리를 요구하지만 그만큼 더 성능이 좋다고 평가받는다. 

정리하자면, 

+ 가장 전통적이고 공인된 기준의 단방향 해시 알고리즘은 PBKDF2
+ 보다 더 강력한 패스워드 digest를 사용하고 싶다면 bcrypt
+ 그것보다 더 강력한 알고리즘이 필요한 민감한 시스템을 개발하고 있고 비용 문제가 전혀 없다면 scrpyt








