---
layout: post
title: React에서의 State 개념 정리
description: ""
tags: [React, Frontend, TIL, Today I Leaned]
comments : true
category : [Frontend, React]
sitemap:
  changefreq: daily
  priority : 1.0
---

# **1. 문제 정의: React에서의 State 관리, 왜 중요한가?**
React 애플리케이션을 개발할 때, **State(상태) 관리**는 UI의 동적인 변화를 처리하는 핵심 요소이다. 그러나 프로젝트 규모가 커질수록 **state가 어디에서 관리되고, 어떻게 변경되는지 예측하기 어려워지는 문제**가 발생한다.

📌 **대표적인 문제 상황:**
- **불필요한 리렌더링** → 성능 저하
- **State 공유 문제** → Props Drilling(깊은 계층으로 props 전달)
- **비동기 상태 관리** → 서버 상태와 클라이언트 상태를 효율적으로 분리해야 함

이를 해결하지 않으면 컴포넌트의 복잡성이 증가하고, 유지보수가 어려워진다. 따라서 **React에서의 올바른 State 관리 전략**을 이해하는 것이 중요하다.

<div class="space-item-3"></div>




# **2. 원인 분석: React에서 State가 동작하는 방식**
React에서 State는 **컴포넌트의 UI를 결정하는 동적인 데이터**이며, 특정 조건에서 변경될 때마다 **리렌더링을 트리거**한다.

### **🔹 React의 기본적인 State 동작 방식**
1️⃣ **State 선언 (`useState`)**  
   - `useState`를 사용하여 컴포넌트 내부에서 상태를 선언하고 관리함
   - 상태가 변경되면 해당 컴포넌트가 다시 렌더링됨

2️⃣ **State 업데이트 (`setState`)**  
   - `setState`를 호출하면, React는 새로운 state를 설정하고, UI를 다시 렌더링함
   - React 18부터는 **자동 배칭(Automatic Batching)**이 적용되어 여러 개의 `setState` 호출이 하나의 렌더링으로 최적화됨

3️⃣ **Reconciliation 과정에서 Virtual DOM 비교**  
   - 변경된 State를 기반으로 새로운 Virtual DOM을 생성하고, 이전 Virtual DOM과 비교(diffing)하여 최소한의 DOM 업데이트 수행

📌 **그러나, 규모가 커질수록 상태 관리가 복잡해지는 문제 발생!**  
➡ **로컬 상태 vs. 전역 상태 vs. 서버 상태를 구분하여 관리해야 함.**

<div class="space-item-3"></div>



# **3. 해결 방법: React에서 효율적인 State 관리 전략**

🚀 **핵심 해결 전략:**
- State의 종류에 따라 적절한 관리 방법을 선택
- 불필요한 리렌더링을 방지하는 최적화 기법 적용
- 서버 상태와 클라이언트 상태를 분리하여 관리

### **✅ 1) 로컬 State 관리 (`useState`, `useReducer` 활용)**
📌 **컴포넌트 내부에서만 사용되는 State는 `useState` 또는 `useReducer`로 관리**
```tsx
const [count, setCount] = useState(0);

const increment = () => {
  setCount((prev) => prev + 1);
};
```
✔ **사용 시기:** 단순한 UI 상태 관리 (입력 필드, 토글 버튼 등)
✔ **최적화:** `useMemo`, `useCallback`을 활용하여 불필요한 리렌더링 방지

### **✅ 2) 전역 State 관리 (`useContext`, Zustand, Redux 등 활용)**
📌 **여러 컴포넌트에서 공유해야 하는 State는 전역 관리 도구 활용**
```tsx
const CountContext = createContext();

function CountProvider({ children }) {
  const [count, setCount] = useState(0);
  return (
    <CountContext.Provider value={{ count, setCount }}>
      {children}
    </CountContext.Provider>
  );
}
```
✔ **사용 시기:** 로그인 정보, UI 테마, 다크 모드 상태 등
✔ **최적화:** React Query, Zustand 등의 라이브러리를 활용하여 전역 상태 효율적으로 관리

### **✅ 3) 서버 State 관리 (React Query, SWR 활용)**
📌 **API 데이터를 다룰 때는 `React Query` 같은 비동기 상태 관리 라이브러리를 사용**
```tsx
const { data, error } = useQuery(["user", userId], fetchUserData);
```
✔ **사용 시기:** API 요청 결과 관리, 서버 캐싱 필요 시
✔ **최적화:** 자동 캐싱 및 리패칭을 통해 불필요한 네트워크 요청 감소

<div class="space-item-3"></div>



# **4. 기술적 인사이트: React 18 & 19에서의 State 최적화**

🚀 **React 18의 주요 변화: Automatic Batching 도입**
✅ **여러 개의 `setState` 호출이 하나의 리렌더링으로 최적화됨**
```tsx
setCount((prev) => prev + 1);
setText("Updated");
// React 18에서는 한 번의 리렌더링만 발생!
```

🚀 **React 19의 주요 변화: `useOptimistic` 추가**
✅ **비동기 상태 업데이트 시, 즉각적인 UI 반영 가능 (낙관적 업데이트)**
```tsx
const [optimisticCount, setOptimisticCount] = useOptimistic(0);
setOptimisticCount((prev) => prev + 1);
```
✔ **서버 응답을 기다리지 않고, UI를 먼저 업데이트하여 사용자 경험 개선**

<div class="space-item-3"></div>



# **5. 결론: React에서의 올바른 State 관리 방법**

✔ **이슈 해결 후 효과**
- 불필요한 리렌더링 감소 → 성능 최적화
- State의 역할을 분리하여 유지보수성 증가
- 클라이언트 & 서버 상태를 분리하여 네트워크 최적화

📌 **React에서 State를 관리할 때 고려할 점:**
1️⃣ **로컬, 전역, 서버 상태를 구분하여 관리**
2️⃣ **불필요한 리렌더링을 최소화하는 최적화 기법 적용**
3️⃣ **React 18 & 19의 새로운 기능을 활용하여 성능 개선**
