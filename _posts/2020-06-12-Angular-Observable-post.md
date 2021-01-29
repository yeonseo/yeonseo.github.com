---
title: Angular Http 400 에러 처리하기 (Observabel 데이터 처리)
author: Yeonseo Nam
date: 2020-06-12 19:34:00 +0900
categories: [Angular, Tutorial]
tags: [Angular, TypeScript, Observabel, Frontend, Web, TIL, Today I Leaned]
comments : true
---

서버에 요청을 보낼 시, 발행할 수 있는 에러는 여러 가지가 있습니다. 회원가입을 하는데 서버에서 필요한 데이터는 ID, password, email인데 사용자가 ID와 password만 적고 email이 귀찮아서 적지 않는다면 에러를 뱉어내죠. 사용자 입장에선 화면이 아무것도 변하지 않고 회원가입도 안 되기 때문에 무엇이 잘못된지 인지하지 못합니다. 또, 게시물을 작성할 때도 같습니다. 제목과 내용이 필요한데, 제목을 적지 않았을 경우 적절한 조치를 취해주지 않으면 사용자는 무엇을 개선해야할지 정확히 모르기 때문에 제목을 입력하라고 알려주어거나 코드 상에서 기본값을 정해주어야 하죠.

가입 좌절 이미지

하지만 이것들은 form 검증에서 이루어지는 일이고, 요청에 대한 return에서 바로 해결해야 할 필요성이 있습니다. 에러를 개발자가 핸들링할 수 있다면 자체 로직으로 에러를 발생시키지 않으면서 문제를 처리할 수 있습니다.

## 참고
> MDN HTTP 상태 코드
https://developer.mozilla.org/ko/docs/Web/HTTP/Status

<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 06. 12
* OS : macOS Catalina 64bit, Windows 10 64bit
* Node : ''
* Angular : ''
* WebStorm : 2017.2.6
* JRE: 1.8.0_152-release-915-b12 amd64
* JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o

---


에러가 발생하면, Requst로 에러 정보가 담겨옵니다. 이 정보는 개발자 도구 창에서 볼 수 있습니다. 에러 코드에 대해서 컨트롤 하는 것은 초반부터 하는 것은 권장되지 않고, 개발이 완성될 즘에 처리를 합니다. 저는 게시판과 회원가입 기능이 거의 완성되었기 때문에 400, 403 에러에 대해서 Interceptor에서 처리를 할 수 있도록 구현하기로 했습니다.

# Interceptor 수정

아래와 같이 Interceptor에서 error를 처리하는 부분에 status(상태코드 값이 들어옴)가 400, 403인 경우에 제가 커스텀한 message가 return 되도록 처리하고 싶었습니다. 그러다 오류가 발생했는데, 이유는 전송하려는 데이터가 Observable이란 클래스 형식이 아니기 때문입니다.


``` ts
return next.handle(req).pipe(
      map((event: HttpEvent<any>) => {
        if (event instanceof HttpResponse) {
          console.log('Interceptor Event--->>>', event);
        }
        return event;
      }),
      catchError(error => {
        this.error_data = {
          error : error,
          reason: error && error.error && error.error.reason ? error.error.reason : '',
          status: error.status
        };
        // error_m = error && error.error && error.error.reason ? error.error.reason : '';
        // status_m = error.status;
        // messeage = error.error.error.message;
        console.log('Interceptor error--->>>', this.error_data );

        if (this.error_data['status'] === 400) {
          console.log('잘못된 요청입니다 에러', this.error_data );
          this.errorCostom = {'message' : '잘못된 요청입니다. 입력칸을 다시 확인해주세요. \n계속 오류가 발생될 경우, 관리자에게 문의바랍니다.'};
          return this.errorCostom; // <<<<<----- 이렇게 보내면 안됩니다~
        }

        if (this.error_data['status'] === 403) {
          console.log('권한이 없습니다 에러', this.error_data );
        }

        return throwError(error);
      })
    );
```
아래와 같은 에러 메세지를 만났습니다.
매번 새로운 에러 💫 이렇게 또 알아가는거지,,,
> error TS2345: Argument of type '(error: any) => {}' is not assignable to parameter of type '(err: any, caught: Observable<HttpEvent<any>>) => ObservableInput<any>'.
      Type '{}' is not assignable to type 'ObservableInput<any>'.

데이터 흐름은 Observable이라는 클래스가 가진 형태로 전송이 되어야 하는데, 보내려고 하는 {'message' : '!@#$'} 객체 형태는 적합하지 않다고 합니다.

# 해결!

```ts
export class HttpInterceptorService implements HttpInterceptor {

  private globalToken = 'EmptyToken';
  public error_data = {};
  public errorCostom = '';

  constructor(private injector: Injector,
              private requestService: RequestService,
              private authService: AuthenticationService) {
  }

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // const token: string = localStorage.getItem('token');
    const currentUser: string = JSON.parse(localStorage.getItem('currentUser'));
    if (currentUser) {
      req = req.clone({headers: req.headers.set('Authorization', 'Bearer ' + currentUser['token'])});
    }
    if (!req.headers.has('Content-Type')) {
      req = req.clone({headers: req.headers.set('Content-Type', 'application/json')});
    }
    req = req.clone({headers: req.headers.set('Accept', 'application/json')});

    return next.handle(req).pipe(
      retry(3),
      map((event: HttpEvent<any>) => {
        if (event instanceof HttpResponse) {
          console.log('Interceptor Event--->>>', event);
        }
        return event;
      }),
      catchError(this.handleError)
    );
  }


  handleError(error) {
    this.error_data = {
      error: error,
      reason: error && error.error && error.error.reason ? error.error.reason : '',
      status: error.status
    };
    // error_m = error && error.error && error.error.reason ? error.error.reason : '';
    // status_m = error.status;
    // messeage = error.error.error.message;
    console.log('Interceptor error--->>>', this.error_data);

    if (this.error_data['status'] === 400) {
      this.errorCostom =
        `잘못된 요청입니다. 입력칸을 다시 확인해주세요.
        계속 오류가 발생될 경우, 관리자에게 문의바랍니다.
      \n code : ${this.error_data['status']}`;
      // return this.errorCostom; -> Observable ㅠㅠ
    }
    if (this.error_data['status'] === 403) {
      this.errorCostom =
        `접근 권한이 없습니다. 권한 변경을 원하시면 관리자에게 문의바랍니다.
      \n code : ${this.error_data['status']}`;
    }

    if (this.errorCostom['message'] !== '') {
    }
    return throwError(error);
  }

  handleErrorRef(error) {
    let errorMessage = '';

    this.error_data = {
      error: error,
      reason: error && error.error && error.error.reason ? error.error.reason : '',
      status: error.status
    };

    if (error.error instanceof ErrorEvent) {
      // client-side error
      errorMessage = `Error: ${error.error.message}`;
    } else {
      // server-side error
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    window.alert(errorMessage);
    return throwError(errorMessage);
  }

}
```

위처럼 전달하려는 에러 정보가 어떤 메세지로 나타났으면 좋겠는지 작성을 하고, 그 내용을 message라는 항목에 넣어서 에러자체를 전달해주어야 합니다. 👌

## 참조
> RxJava - Observable이란?
https://penguin-story.tistory.com/10


