---
layout: post
title: Kubernetes Ingress 413 오류 해결 과정과 최적화 전략
description: ""
tags: [K8S, Kubernetes, DevOps, TIL, Today I Leaned]
comments : true
category : [DevOps, Kubernetes]
sitemap:
  changefreq: daily
  priority : 1.0
---


# **1. 문제 정의: Ingress를 통한 파일 업로드의 한계**
Kubernetes 환경에서 대용량 파일을 업로드하려 할 때 `413 Payload Too Large` 오류가 발생했다.  
로컬 환경에서는 정상 동작했지만, **Ingress를 통한 요청만 차단되는 현상이 발생**했다.

❌ **Ingress에서 요청 크기를 제한할 가능성**  
- 기본적으로 `client-body-buffer-size`와 `proxy-body-size`의 기본값이 작음  
- 대용량 파일 업로드 시 차단될 가능성이 높음  

📌 **이 문제를 해결하기 위해, Ingress 설정 변경과 API 서버의 요청 처리 방식을 점검해보기로 했다.**  

<div class="space-item-3"></div>





# **2. Ingress 설정 변경 및 테스트**  

🚀 **핵심 해결 전략:**  
- Ingress에서 `413 Payload Too Large` 오류가 발생하는 원인을 분석  
- `nginx.ingress.kubernetes.io/proxy-body-size` 설정을 변경  
- ConfigMap을 수정하여 글로벌 설정을 적용  

### **🔹 기존 Ingress 설정 (413 오류 발생)**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nginx-issue-bo
  namespace: st-issue
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: issue-bo.blockchainglobal.co.kr
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: issue-bo-front
              port:
                name: http
```
📌 **이 상태에서는 기본 크기 제한으로 인해 413 오류가 발생함**  


### **🔹 변경된 Ingress 설정 (최대 요청 크기 증가)**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nginx-issue-bo
  namespace: st-issue
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/proxy-body-size: "300m"
    nginx.ingress.kubernetes.io/client-body-buffer-size: "300m"
spec:
  rules:
  - host: issue-bo.blockchainglobal.co.kr
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: issue-bo-front
              port:
                name: http
```

🚀 **Ingress 컨트롤러 재배포**
```sh
kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx
```

✅ **결과:**  
- `413 Payload Too Large` 오류가 **여전히 발생**  

📌 Ingress 설정만으로는 해결되지 않음을 확인하고, **ConfigMap을 수정하여 글로벌 설정을 변경하기로 함.**

<div class="space-item-3"></div>




# **3. Ingress ConfigMap 수정 및 적용**  

Ingress 컨트롤러가 **전역 설정(ConfigMap)** 에 의해 요청 크기를 제한하고 있을 가능성이 높았다.  
이를 확인하기 위해 현재 ConfigMap을 조회했다.

```sh
kubectl get configmap -n ingress-nginx
```

🚀 **ConfigMap 편집**
```sh
kubectl edit configmap ingress-nginx-controller -n ingress-nginx
```

🔹 **변경된 ConfigMap 설정**
```yaml
data:
  proxy-body-size: "300m"
  client-body-buffer-size: "300m"
```

🚀 **변경 사항 적용 후 Ingress 컨트롤러 재시작**
```sh
kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx
```

✅ **결과:**  
- 요청 크기 제한이 해제되어 파일 업로드가 가능해짐  
- **Ingress에서 413 오류가 해결됨**

📌 하지만, **API 서버의 요청 크기 제한도 확인해야 함.**  

<div class="space-item-3"></div>




# **4. API 서버의 요청 크기 제한 해제**  

### **🔹 기존 Spring Boot 설정**
```yaml
spring:
  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 10MB
```
📌 **이 설정이 Ingress에서 허용한 크기보다 작다면, API 서버에서 요청이 차단될 수 있음.**

---

### **🔹 변경된 API 설정 (최대 크기 증가)**
```yaml
spring:
  servlet:
    multipart:
      max-file-size: 500MB
      max-request-size: 500MB
```

🚀 **API 서버 재배포**
```sh
kubectl rollout restart deployment api-backoffice -n st-issue
```

✅ **결과:**  
- **Ingress 및 API 서버에서 요청 크기 제한이 모두 해제됨.**  
- 대용량 파일 업로드가 정상적으로 동작함.  

📌 **이제 남은 것은 실제 네트워크 요청이 정상적으로 전달되는지 검증하는 것!**  

<div class="space-item-3"></div>




# **5. 네트워크 요청 검증 및 최종 확인**  

Ingress가 내부 API 서버로 요청을 제대로 전달하는지 확인하기 위해 **네트워크 로그를 분석**했다.

🚀 **Ingress 로그 확인**
```sh
kubectl logs <ingress-nginx-pod> -n ingress-nginx
```

🚀 **API 서버 로그 확인**
```sh
kubectl logs <api-pod-name> -n st-issue
```

✅ **최종 결과:**  
- Ingress → API 서버로 요청이 정상적으로 전달됨  
- API 서버에서 500MB 이하의 파일 업로드가 가능해짐  
- **최종적으로 `413 Payload Too Large` 오류 해결!**  

<div class="space-item-3"></div>




# **6. 결론 및 최적화 포인트**  

✔ **이슈 해결 후 효과**
- Ingress에서 **대용량 요청 차단 문제 해결**  
- API 서버에서 **파일 업로드 크기 제한 해제**  
- ConfigMap을 활용하여 **전체 클러스터에 적용되는 글로벌 설정 관리 가능**  

📌 **Ingress 및 API 서버의 설정을 함께 변경해야 대용량 요청이 원활하게 처리됨을 확인했다.**  

---

### **🚀 Kubernetes Ingress에서 413 오류를 해결하는 핵심 체크리스트**  

✅ **1. Ingress 설정 변경**
- `proxy-body-size`와 `client-body-buffer-size` 값 증가  
- Ingress 컨트롤러 재배포  

✅ **2. ConfigMap 확인 및 수정**
- Ingress 컨트롤러의 글로벌 설정 변경 (`proxy-body-size`, `client-body-buffer-size`)  
- 변경 후 Ingress 컨트롤러 재시작  

✅ **3. API 서버 설정 확인**
- Spring Boot의 `multipart.max-file-size` 값 증가  
- API 서버 재배포  

✅ **4. 네트워크 로그 확인**
- `kubectl logs` 명령어로 Ingress와 API 서버의 로그 확인  
- Ingress에서 정상적으로 API 서버로 요청이 전달되는지 검증  

---

## **맺음말: 시스템적 사고의 중요성**
이번 문제 해결 과정에서 **단순히 Ingress 설정만 변경하는 것이 아니라, 전체 시스템의 흐름을 분석해야 한다는 점**을 다시금 깨달았다.  

개발자로서 **Ingress, API 서버, 네트워크 설정을 유기적으로 이해하는 것이 중요하며, 문제 해결 시 전체적인 관점에서 접근하는 것이 효과적이다.**  

🚀 **여러분은 Kubernetes에서 API 요청이 차단될 때, 어떻게 문제를 해결하시나요?**  
경험을 공유해주시면 더 좋은 해결 방법을 찾을 수도 있겠네요! 💡