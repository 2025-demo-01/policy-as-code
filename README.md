# policy-as-code

 **클러스터 보안 및 공급망 보증(Supply Chain Security)** 을 위한 모든 정책을 관리합니다.  
ArgoCD에 의해 자동  deploy되먀, 모든 변경은 review 승인 후만 반영느는 척.

---

##  구성

| dir | contents |
|-----------|------|
| `kyverno/` | Pod 수준 보안 정책 (hostPath 금지, privileged 금지, 리소스 제한, 라벨 강제 등) |
| `gatekeeper/` | 조직 차원의 구조적 제약 (Public LB 금지, 라벨 템플릿 강제 등) |
| `supply-chain/` | 이미지 무결성 검증(Cosign), SBOM 확인, 취약점 스캔(Trivy), 비밀 검출 |
| `sops/` | SOPS 암호화 정책 및 예제 |
| `.github/workflows/` | 정책 검증 및 보안 알림 자동화 |

---

## 적용 원칙

1.  **Default Deny**
- HostPath, Privileged, 외부 Egress, Public LB 전부 기본 차단  
2. **Image Trust**
- 모든 Container Image는 Cosign으로 서명되어야 함  
3. **SBOM & Scan**
- SBOM 존재 여부 및 Trivy 취약점 스캔을 PR단계에서 수행  
4. **Secrets**
- 모든 Secret은 SOPS로 암호화된 file만 허용  
5. **Labels**
- 모든 Workload는 owner, env,service, 포함해야 함  

---

## 방법

1. ArgoCD의 `policy-enforcement` 앱에서 자동 동기화됩니다.  
2. Kyverno와 Gatekeeper는 `validationFailureAction=Enforce` 로 강제 적용됩니다.  
3. Cosign public key, SOPS key는 `global-secrets` namespacr에서 참조합니다.  
4. 정책 위반 시 Slack 알림(`notify-security.yaml`)이 자동 발송됩니다.  

---
 

---

##  샘플 키 관리(가짜)

| key | value |
|------|------|
| Docker 레지스트리 인증 | sophielog |
| Cosign 공개키 | sophielog |
| SOPS age 키 | sophielog |

---
