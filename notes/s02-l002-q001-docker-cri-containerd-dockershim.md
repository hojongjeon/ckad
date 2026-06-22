# Q001 - Docker, CRI, containerd, and dockershim

## Metadata

- Course: CKAD with Tests - Mumshad Mannambeth
- Section: 02 - Core Concepts
- Lecture: 002 - Docker-vs-ContainerD
- Timestamp: unknown
- Date: 2026-06-21
- Tags: docker, cri, containerd, runc, dockershim, kubelet
- Status: answered

## Question Thread

### Q1. Kubernetes의 최초 컨테이너 런타임은 Docker였다고 배웠는데, Docker는 정말 컨테이너 런타임인가요?

Kubernetes는 초기에 Docker를 컨테이너 런타임처럼 사용했다고 알고 있다. 그런데 이후 CRI를 도입하면서 Docker는 CRI와 직접 호환되지 않았고, Docker만을 위한 `dockershim` 중간 계층이 필요했다고 배웠다.

이후 dockershim이 제거되면서 Kubernetes가 더 이상 Docker를 런타임으로 지원하지 않는다고 들었다.

이 흐름에서 혼란스러운 점은 Docker의 정체다. Docker가 컨테이너 런타임이 아니라면, 실제 런타임은 `containerd`이고 Docker는 그 위에 만들어진 플랫폼인가?

## Short Answer

Docker는 과거 Kubernetes에서 넓은 의미의 "컨테이너 런타임"처럼 사용되고 불렸지만, 정확히는 컨테이너를 빌드, 관리, 실행하기 위한 플랫폼에 가깝다.

Kubernetes가 CRI(Container Runtime Interface)를 도입한 이후 기준으로 보면, Docker Engine 자체는 CRI를 구현한 런타임이 아니었다. 그래서 kubelet이 Docker와 통신하기 위해 `dockershim`이라는 중간 계층이 필요했다.

현재 Kubernetes 노드에서는 Docker Engine을 거치지 않고 `containerd`나 `CRI-O` 같은 CRI 호환 런타임과 직접 통신하는 방식이 일반적이다.

## Explanation

혼란은 "컨테이너 런타임"이라는 표현이 넓은 의미와 좁은 의미로 모두 쓰이기 때문에 생긴다.

넓은 의미에서는 Docker도 컨테이너를 실행할 수 있으므로 컨테이너 런타임처럼 불렸다. 하지만 구조적으로 Docker는 단순 실행기라기보다 이미지 빌드, 이미지 관리, push/pull, 네트워크, 볼륨, 사용자 CLI/API 등을 제공하는 컨테이너 플랫폼이다.

Docker 내부에서는 실제 컨테이너 생명주기 관리를 위해 `containerd`를 사용하고, `containerd`는 더 아래 단계에서 `runc`를 통해 실제 컨테이너 프로세스를 만든다.

```text
Docker
├── Docker CLI / API
├── image build, push, pull
├── container lifecycle management
├── networking and volumes
└── containerd
    └── runc
        └── Linux kernel features
```

Kubernetes 초기에는 Docker가 사실상 표준이었기 때문에 kubelet이 Docker와 직접 통신했다.

```text
kubelet -> Docker -> containerd -> runc
```

이후 Kubernetes는 여러 런타임을 표준 인터페이스로 지원하기 위해 CRI를 도입했다.

```text
kubelet -> CRI -> container runtime
```

하지만 Docker Engine은 CRI API를 직접 구현하지 않았다. 그래서 Kubernetes는 Docker 지원을 유지하기 위해 kubelet 내부에 `dockershim`이라는 어댑터를 두었다.

```text
kubelet -> dockershim -> Docker -> containerd -> runc
```

나중에는 Docker 내부에서 사용하던 `containerd`가 CRI를 직접 지원할 수 있게 되었고, Kubernetes 입장에서는 Docker Engine 전체를 중간에 끼울 필요가 줄어들었다.

```text
kubelet -> CRI -> containerd -> runc
```

따라서 dockershim 제거는 Docker로 만든 이미지가 Kubernetes에서 더 이상 동작하지 않는다는 뜻이 아니다. Kubernetes 노드의 컨테이너 실행 경로에서 Docker Engine을 중간 계층으로 사용하지 않는다는 뜻이다.

## CKAD Exam Focus

CKAD에서는 내부 런타임 구현을 깊게 묻기보다는 다음 정도를 구분하면 충분하다.

| Term | 기억할 역할 |
| --- | --- |
| Docker | 컨테이너 플랫폼. 빌드, 이미지 관리, 실행 UX 제공 |
| containerd | 고수준 컨테이너 런타임. 이미지 pull, 컨테이너 lifecycle 관리 |
| runc | 저수준 OCI 런타임. 실제 컨테이너 프로세스 생성 |
| CRI | kubelet과 컨테이너 런타임 사이의 표준 인터페이스 |
| dockershim | kubelet이 Docker와 통신할 수 있게 해주던 CRI 어댑터 |

시험 관점의 핵심 기억:

```text
과거:
kubelet -> dockershim -> Docker -> containerd -> runc

현재:
kubelet -> CRI -> containerd 또는 CRI-O -> runc
```

> Docker 이미지는 OCI 표준 형식이므로 Docker Engine을 Kubernetes 런타임으로 쓰지 않아도 Kubernetes에서 실행할 수 있다.

## Related Concepts

- CRI(Container Runtime Interface)
- OCI(Open Container Initiative)
- kubelet
- containerd
- CRI-O
- runc
- Docker image
