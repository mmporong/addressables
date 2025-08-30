[게임 플레이하기] (https://mmporong.github.io/addressables/index.html)

# Unity Addressables - GitHub Pages 원격 호스팅 구현

## 프로젝트 개요

Unity Addressable Asset System을 GitHub Pages 원격 호스팅으로 구현한 WebGL 게임 프로젝트입니다.

### 구현 목표
- Unity WebGL에서 Addressables를 통한 동적 에셋 로딩
- GitHub Pages를 활용한 원격 호스팅
- 오브젝트 풀링과 Addressables 통합
---

## 기술적 구현

### Addressables 아키텍처
```
ObjectPoolManager.cs
├── GitHub Pages 원격 호스팅 설정
├── 오브젝트 풀링 통합
└── 에러 처리 및 재시도 로직
```

### 핵심 구현 사항

#### 1. GitHub Pages 원격 호스팅 설정
```csharp
// Addressables Profile 설정
Remote.LoadPath: https://mmporong.github.io/addressables/StreamingAssets/aa
Remote.BuildPath: StreamingAssets/aa
```

#### 2. 오브젝트 풀링과 Addressables 통합
```csharp
// GitHub Pages에서 프리팹 로드
var handle = Addressables.LoadAssetAsync<GameObject>(arrowPrefabReference);
await handle.Task;

if (handle.Status == AsyncOperationStatus.Succeeded)
{
    Arrow prefab = handle.Result.GetComponent<Arrow>();
    arrowPool = new ObjectPool<Arrow>(prefab, arrowPoolParent, arrowPoolSize);
}
```

---

## 성공적인 구현 결과

### 해결된 기술적 과제

1. **WebGL 압축 해제 오류 해결**
   - `"Decompressing this format (1) is not supported on this platform"` 오류 완전 해결
   - GitHub Pages에서 번들 파일 정상 다운로드 확인

2. **404 오류 해결**
   - Addressables Profile 설정 최적화
   - GitHub Pages URL 구조와 완벽 매칭

3. **메모리 관리 최적화**
   - Addressables 핸들 자동 해제
   - WebGL 환경에 맞는 메모리 설정

### 구현 성공 로그 출력 
```
✅ [POOL MANAGER] WebGL settings configured for Addressables
✅ [UnityCache] Bundle successfully downloaded and stored in browser cache
✅ [POOL MANAGER] Arrow pool initialized with size 50 from GitHub Pages Addressables
✅ [POOL MANAGER] Initialized with 1 pools: - Arrow: 50 objects
```

---

## 설정 가이드

### 1. Addressables Profile 설정
```
Window → Asset Management → Addressables → Groups
Profile: Default
Remote.LoadPath: https://[username].github.io/[repository]/StreamingAssets/aa
Remote.BuildPath: StreamingAssets/aa
```

### 2. 프리팹 Addressables 설정
```
Arrow.prefab 선택 → Inspector
✅ Addressable 체크박스 활성화
Address: Arrow
Group: Default Local Group (Remote로 설정)
```

### 3. 빌드 프로세스
```
1. Addressables Groups → Build → New Build → Default Build Script
2. Assets/StreamingAssets/aa/ 폴더의 모든 파일을 GitHub에 업로드
3. WebGL 프로젝트 빌드
```

---

## 문제 해결 사례

### 1. WebGL 압축 해제 오류
**문제**: `"Decompressing this format (1) is not supported on this platform"`
**해결**: WebGL 환경 최적화 설정 추가 및 압축 방식 조정

### 2. 404 오류 (번들 파일 접근 실패)
**문제**: GitHub Pages에서 `.bundle` 파일 접근 불가
**해결**: Addressables Profile URL 설정 최적화

### 3. 메모리 누수
**문제**: Addressables 핸들 미해제로 인한 메모리 누수
**해결**: 자동 핸들 해제 로직 구현

---

