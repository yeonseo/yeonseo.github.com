---
layout: post
title: React.StrictMode - React 애플리케이션의 품질을 향상시키는 도구
description: ""
tags: [React, Frontend, TIL, Today I Leaned]
comments : true
category : [Frontend, React]
sitemap:
  changefreq: daily
  priority : 1.0
---

# **1. 문제 정의: React에서 발생할 수 있는 잠재적 문제들**
React 애플리케이션을 개발할 때, 개발 환경에서 미리 감지하지 않으면 **비효율적인 렌더링, 예기치 않은 사이드 이펙트, 레거시 API 사용 문제** 등이 발생할 수 있다. 이런 문제를 방지하지 않으면 유지보수 비용이 증가하고, 사용자 경험이 저하될 가능성이 크다.

📌 **대표적인 문제 상황:**
- 잘못된 `useEffect` 사용으로 인해 **의도치 않은 사이드 이펙트 발생**
- 레거시 라이프사이클 메서드 사용으로 인해 **업데이트 시 비효율적인 렌더링 발생**
- 비동기 데이터 로딩 과정에서 **메모리 누수 및 예기치 않은 상태 변화 발생**

이를 해결하지 않으면 애플리케이션의 신뢰성이 떨어지고, 유지보수가 어려워진다. 이를 방지하기 위해 React는 `StrictMode`를 제공한다.

<div class="space-item-3"></div>



# **2. 원인 분석: React.StrictMode의 역할과 동작 방식**
React.StrictMode는 **개발 환경에서만 실행되는 도구**로, 애플리케이션 내에서 발생할 수 있는 **잠재적인 문제를 사전에 감지하는 역할**을 한다. 프로덕션 환경에서는 영향을 주지 않는다.

### **🔹 React.StrictMode의 주요 기능**
1️⃣ **의심스러운 사이드 이펙트 감지**
   - `useEffect` 내부에서 예상치 못한 비동기 처리나 잘못된 cleanup 함수 호출을 감지할 수 있다.

2️⃣ **레거시 API 사용 감지**
   - React 17 이후 지원이 중단된 `componentWillMount`, `componentWillReceiveProps` 등의 라이프사이클 메서드 사용을 경고한다.

3️⃣ **비효율적인 렌더링 감지**
   - `StrictMode` 내에서 컴포넌트를 두 번 렌더링하여 **불필요한 re-rendering이 있는지 체크**할 수 있다.

📌 **React 18 & 19에서 StrictMode의 주요 변화**
✅ **React 18:** Concurrent Rendering 환경에서 `StrictMode`가 **마운트된 컴포넌트를 자동으로 언마운트 후 다시 마운트**하여 메모리 누수를 감지한다.
✅ **React 19:** `useOptimistic`, `useActionState` 등의 새로운 상태 관리 기능이 추가되면서, `StrictMode`가 더욱 강력하게 상태 흐름을 검증할 수 있도록 개선되었다.

<div class="space-item-3"></div>



# **3. 해결 방법: React.StrictMode 적용 방법과 예제 코드**

🚀 **핵심 해결 전략:**
- 개발 환경에서 `StrictMode`를 활성화하여 문제를 사전에 감지
- useEffect 내부에서 예상치 못한 사이드 이펙트가 발생하는지 확인
- React 18+ 환경에서 자동 배칭 및 Concurrent Mode와의 호환성 점검

### **✅ 1) StrictMode 적용 방법**
React에서 `StrictMode`를 활성화하려면, `index.js` 또는 `main.tsx`에서 `<React.StrictMode>`로 애플리케이션을 감싸주면 된다.

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

➡️ **개발 환경에서만 동작하며, 프로덕션 환경에서는 무시됨!**

### **✅ 2) 특정 컴포넌트에서만 StrictMode 적용**
필요한 경우 특정 컴포넌트에만 `StrictMode`를 적용할 수도 있다.
```tsx
function MyComponent() {
  return (
    <React.StrictMode>
      <ChildComponent />
    </React.StrictMode>
  );
}
```
➡️ **애플리케이션 전체가 아닌 특정 컴포넌트에서만 테스트 가능!**

<div class="space-item-3"></div>



# **4. 기술적 인사이트: React.StrictMode의 주요 감지 항목**

🚀 **StrictMode가 감지하는 주요 항목 정리**
✅ **비효율적인 렌더링 감지**
- `StrictMode`가 컴포넌트를 두 번 렌더링하여 불필요한 리렌더링을 확인함

✅ **잘못된 useEffect 사용 감지**
- `useEffect` 내부에서 비동기 함수 호출 시, cleanup 함수가 올바르게 동작하는지 검증 가능

✅ **레거시 API 사용 경고**
- `componentWillMount`, `componentWillReceiveProps` 같은 React 17 이전 라이프사이클 메서드 사용 시 경고 출력

✅ **비동기 처리 검증 (React 18 이후)**
- `React 18+`에서는 `StrictMode` 내에서 **비동기 상태 업데이트 흐름을 더 정밀하게 감지**할 수 있음

<div class="space-item-3"></div>



# **5. 결론: React.StrictMode 활용 가이드**

✔ **이슈 해결 후 효과**
- 불필요한 리렌더링을 사전에 감지하여 성능 최적화 가능
- useEffect의 cleanup 로직 검증을 통해 메모리 누수 방지
- 레거시 API 사용 감지를 통해 코드 품질 향상

📌 **React 애플리케이션에서 StrictMode를 활용할 때 고려할 점:**
1️⃣ **개발 환경에서만 활성화하여 잠재적 문제를 조기에 발견하자**
2️⃣ **StrictMode에서 감지하는 내용을 기반으로 리팩토링을 진행하자**
3️⃣ **React 18 & 19의 새로운 기능과 함께 활용하여 최적화하자**
