---
title: Angular Interceptor로 로그인 상태 확인하기
author: Yeonseo Nam
date: 2020-06-02 19:34:00 +0900
categories: [Angular, Tutorial]
tags: [Angular, TypeScript, Frontend, Web, TIL, Today I Leaned]
comments : true
category : [Angular]
---

사용자가 로그인을 성공했을 시 생성되는 Token 값을 이용해서 페이지 접근권한을 관리할 때 Interceptor라는 것을 사용한다고 들었습니다. 실제로는 localStorage에 저장한 token값을 사용하여 임시프로젝트를 수행했습니다. 나중에 Interceptor를 사용할 수도 있다고 생각되어서 코드 흐름을 파악하는 정도로만 기록해두려고 합니다.

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

<br/><br/><br/><br/><br/>

이전의 포스팅에서 사용자가 가입할 수 있고 게시판이 있는 사이트를 만들어본다고 했습니다. 그래서 'users' App과 'boards' App이 필요했고, 다음 명령어를 통해서 같이 추가했습니다.
# Interceptors
> HttpInterceptor was introduced with Angular 4.3. It provides a way to intercept HTTP requests and responses to transform or handle them before passing them along.

HttpInterceptor는 Angular 4.3에서 도입되었습니다. 전달하기 전에 HTTP 요청 및 응답을 가로 채서 변환하거나 처리하는 방법을 제공합니다.

주로 AuthToken을 사용한 Api 요청이나 전역적으로 Http 에러를 처리할 때 자주 쓰입니다.

Interceptor를 사용하여 할 수 있는 것들은 다음과 같습니다.
* Add bearer token
* Refresch Token
* Redirect to the login page

지금 백앤드 측에서는 Django가 제공하는 rest-auth 를 통해서 유저가 로그인하면 유저 정보와 Token 값을 받고 있으므로 이를 활용하는 방법을 알아보겠습니다.


## Interceptor Service 간단한 예제
출처: https://alexband.tistory.com/54 [GoodBye World]
``` ts
// app/src/services/http.interceptor.service.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators'; 

@Injectable()
export class HttpInterceptorService implements HttpInterceptor { 
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        let request: HttpRequest<any> = req.clone();
        return next.handle(request).pipe(
            catchError(e => {
                /** * 여기서 Error 원하는 방식으로 에러를 처리 */
                alert(e.error.error.message); return throwError(e);
            })
        )
    }
}
```


``` ts
// src/app/app.module.ts 
import { BrowserModule } from '@angular/platform-browser'; 
import { NgModule } from '@angular/core'; 
import { AppComponent } from './app.component'; 
import { CommonModule } from '@angular/common'; 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http'; 
import { HttpInterceptorService } from '../services/http-interceptor.service'; 

@NgModule({ 
    declarations: [ 
        AppComponent 
        ], 
    imports: [ 
        BrowserModule, 
        CommonModule,
        HttpClientModule, 
    ], 
    providers: [ 
        { provide: HTTP_INTERCEPTORS, useClass: HttpInterceptorService, multi: true },
    ], 
    bootstrap: [AppComponent]
})

export class AppModule { }
```



## 여러가지 상황을 고려한 코드 이해하기
출처 : https://medium.com/angular-in-depth/top-10-ways-to-use-interceptors-in-angular-db450f8a62d6 [Michael Karén, medium]

``` ts
import { Injectable } from "@angular/core";
import { 
  HttpEvent, HttpInterceptor, HttpHandler,
  HttpRequest, HttpErrorResponse
} from "@angular/common/http";
import { throwError, Observable, BehaviorSubject, of } from "rxjs";
import { catchError, filter, take, switchMap } from "rxjs/operators";

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  private AUTH_HEADER = "Authorization";
  private token = "secrettoken";
  private refreshTokenInProgress = false;
  private refreshTokenSubject: BehaviorSubject<any> = new BehaviorSubject<any>(null);

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {

    if (!req.headers.has('Content-Type')) {
      req = req.clone({
        headers: req.headers.set('Content-Type', 'application/json')
      });
    }

    req = this.addAuthenticationToken(req);

    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        if (error && error.status === 401) {
          // 401 errors are most likely going to be because we have an expired token that we need to refresh.
          if (this.refreshTokenInProgress) {
            // If refreshTokenInProgress is true, we will wait until refreshTokenSubject has a non-null value
            // which means the new token is ready and we can retry the request again
            return this.refreshTokenSubject.pipe(
              filter(result => result !== null),
              take(1),
              switchMap(() => next.handle(this.addAuthenticationToken(req)))
            );
          } else {
            this.refreshTokenInProgress = true;

            // Set the refreshTokenSubject to null so that subsequent API calls will wait until the new token has been retrieved
            this.refreshTokenSubject.next(null);
            
            return this.refreshAccessToken().pipe(
              switchMap((success: boolean) => {               
                this.refreshTokenSubject.next(success);
                return next.handle(this.addAuthenticationToken(req));
              }),
              // When the call to refreshToken completes we reset the refreshTokenInProgress to false
              // for the next time the token needs to be refreshed
              finalize(() => this.refreshTokenInProgress = false)
            );
          }
        } else {
          return throwError(error);
        }
      })
    );
  }

  private refreshAccessToken(): Observable<any> {
    return of("secret token");
  }

  private addAuthenticationToken(request: HttpRequest<any>): HttpRequest<any> {
    // If we do not have a token yet then we should not set the header.
    // Here we could first retrieve the token from where we store it.
    if (!this.token) {
      return request;
    }
    // If you are calling an outside domain then do not add the token.
    if (!request.url.match(/www.mydomain.com\//)) {
      return request;
    }
    return request.clone({
      headers: request.headers.set(this.AUTH_HEADER, "Bearer " + this.token)
    });
  }
}
```

interceptor는 intercept() 함수를 통해 구현됩니다. 
> intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>
req : Header 값이 들어가는데, 기존 값을 바로 사용하지 못하므로 clone하여 사용합니다.
next : HttpHandler로 액션 정의를 해주는 부분입니다.
Return : Observable로 주어집니다. HTTP 클래스들이 대부분 Observable로 주는데, 이전에 HttpClient를 사용했을 때와 비교해서 이해하면 될 것 같습니다.




## 참조
> Top 10 ways to use Interceptors in Angular
https://medium.com/angular-in-depth/top-10-ways-to-use-interceptors-in-angular-db450f8a62d6

> Angular Http Interceptor 사용하기
https://alexband.tistory.com/54

> https://angular.io/api/common/http/HttpInterceptor