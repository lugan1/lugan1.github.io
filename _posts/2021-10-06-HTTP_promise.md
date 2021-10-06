---
title: "[angular] Promise를 이용한 Service와 Compnent의 HTTP 데이터 처리"
tags:
- log
- angular
- typescript
categories:
- angular
date: '2021-10-06 08:54:00'
classes: wide
---
# 출저 및 참고 사이트 :  
[https://programmingsummaries.tistory.com/325](https://programmingsummaries.tistory.com/325)  



# Javascirpt Pormise
- Promise 패턴을 사용하면, 비동기적인 작업들을 순서대로, 순차대로 진행하거나, 병렬로 진행할수 있게된다.
- 현재 Promise 패턴은 ECMA Script 6 스펙에 정식으로 포함되었다.


## Promise 기초 예제)
```javascript
//Promise 선언
var _promise = function (param) {

	return new Promise(function (resolve, reject) {

		// 비동기를 표현하기 위해 setTimeout 함수를 사용 
		window.setTimeout(function () {

			// 파라메터가 참이면, 
			if (param) {

				// 해결됨 
				resolve("해결 완료");
			}

			// 파라메터가 거짓이면, 
			else {

				// 실패 
				reject(Error("실패!!"));
			}
		}, 3000);
	});
};

// new Promise로 Promise 가 생성되는 직후부터 resolve 나 reject가 호출 되기 전까지의 순간을 pending 상태라고 볼 수 있다.
// 이후 비동기 작업이 마친 뒤 결과물을 약속대로 잘 줄 수 있다면

// 첫번째 파라메터로 주입되는 resolve 함수를 호출하고, 실패했다면 두번째 파라메터로 주입되는 reject 함수를 호출한다.



//Promise 실행
// _promise() 를 호출하면 Promise 객체가 리턴된다.
_promise(true)
.then(function (text) {
// Promise 객체에는 정상적으로 비동기 작업이 완료되었을때 호출하는 then 이라는 API 가 존재한다.

	// 성공시 호출할 함수
	console.log(text);
}, function (error) {
	// 실패시 호출할 함수
	console.error(error);
});


```

<br/>
<br/>

```javascript
var _promise = function (매개변수) {
    return new Promise(function (resolve, reject)) {
        비 동기적으로 동작하는 함수, 코드
    }
}


_promise(매개변수).then( { 성공시 처리함수 구현}, {실패시 처리함수 구현 } )

```

<br/>

**Primise 선언부**
- **pending** : 아직 약속을 수행 중인 상태(fullfilled 혹은 reject가 되기 전)
- **fulfilled** : 약속(promise)이 지켜진 상태
- **rejected** : 약속(promise)가 어떤 이유에서 못 지켜진 상태
- **settled** : 약속이 지켜졌든 안지켜졌든 일단 결론이 난 상태

<br/>
<br/>

## 체이닝형태로 연결된 상태에서 비동기 작업이 중간에 에러가나면 처리 Promise.catch API

<br/>
<br/>

## 여러 프로미스가 모두 완료될 때 실행 Promise.all API

<br/>
<br/>
<br/>


<br/>

# 출저 및 참고 사이트 :  
[https://blog.eunsatio.io/develop/Angular-HttpClient%EC%9D%98-%ED%94%84%EB%A1%9C%EB%AF%B8%EC%8A%A4%ED%99%94,-%EA%B7%B8%EB%A6%AC%EA%B3%A0-HttpInterceptor](https://blog.eunsatio.io/develop/Angular-HttpClient%EC%9D%98-%ED%94%84%EB%A1%9C%EB%AF%B8%EC%8A%A4%ED%99%94,-%EA%B7%B8%EB%A6%AC%EA%B3%A0-HttpInterceptor)

<br/>

# Angular HttpClient 라이브러리를 이용한 Promise
## 기본적인 Http 요청 예제)
```javascript
constructor(
    private http: HttpClient
) {}
 
public getData(): void {
    this.http.get('{{ request uri }}')
    .subscribe((data: any): void => {
        //do somthing with data
    }, error => {
        //handle error
    });
}
```
- subscribe 메서드를 이용해 데이터를 받아 처리
- subscribe의 두 번째 인자는 에러를 처리하는 콜백 함수
- 단점 : 콤포넌트에서 직접 Http 통신을 하는 것보다 서비스로 따로 빼서 관리하는 것이 유지보수 측면에서 좋다. (MVVM 패턴)


## 서비스에서 HTTP 메소드 처리 후, Component 로 데이터 전달
- Component를 콜백호출로 데이터 처리를 하면, 코드가 길어지니 HTTP 통신 메소드를 Promise로 만들어서 Component 에게 데이터를 전달하는 방식으로 한다.

<br/>
<br/>

## HttpClient 에서 제공하는 toPromise() 메서드 사용 예제)

```javascript
constructor(
    private http: HttpClient
) {}
 
public async getData(): Promise<any> {
    return await this.http.get('{{ request uri }}').toPromise();
    //or return await this.http.get('{{ request uri }}').toPromise() as Data;
}
```

<br/>
<br/>

## 보안 문제 등으로 http request url 에 header 를 붙여서 처리하고 싶으면 HttpHeaders 모듈 사용

<br/>
<br/>

## Http 요청받으면 intercept -> 새로운 헤더를 붙여 복제 및 요청하는 방법 : HttpInterceptor