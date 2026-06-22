# Q001 - Kubernetes command, args, ENTRYPOINT, and CMD

## Metadata

- Course: CKAD with Tests - Mumshad Mannambeth
- Section: 03 - Configuration
- Lecture: 004 - Commands and Arguments in Kubernetes
- Timestamp: unknown
- Date: 2026-06-22
- Tags: command, args, entrypoint, cmd, dockerfile
- Status: answered

## Question Thread

### Q1. `docker run ubuntu`는 왜 바로 종료되나요?

### Q2. Docker의 `ENTRYPOINT`와 `CMD`는 Kubernetes `command`와 `args` 이해에 어떻게 연결되나요?

### Q3. Pod YAML의 `command`와 `args`는 언제 Dockerfile 설정을 override하나요?

## Short Answer

컨테이너는 VM처럼 OS 전체를 계속 실행하는 것이 아니라, 하나의 메인 프로세스를 실행하기 위한 환경이다. 컨테이너 안의 메인 프로세스가 종료되면 컨테이너도 종료된다.

Dockerfile의 `ENTRYPOINT`는 실행할 프로그램, `CMD`는 그 프로그램에 전달할 기본 인자다. Kubernetes Pod에서는 이 기본 실행 설정을 `command`와 `args`로 override할 수 있다.

```text
Kubernetes command = Docker ENTRYPOINT
Kubernetes args    = Docker CMD
```

Pod YAML에 `command`나 `args`를 쓰지 않으면 이미지 기본값을 그대로 사용한다. 명시했을 때만 해당 값을 override한다. 실무에서는 보통 이미지의 기본 실행 설정을 따르고, 실행 모드나 인자를 바꿔야 할 때만 override한다.

## Explanation

`docker run ubuntu`가 바로 종료되는 이유는 Ubuntu 컨테이너의 기본 명령이 `bash`이기 때문이다. `bash`는 터미널 입력을 기다리는 shell 프로그램인데, 기본 `docker run ubuntu`는 입력받을 터미널을 붙여주지 않는다. 그래서 `bash`가 종료되고, 메인 프로세스가 끝났으므로 컨테이너도 종료된다.

```text
ubuntu 컨테이너
└─ bash 실행
   └─ 입력받을 터미널 없음
      └─ bash 종료
         └─ 컨테이너 종료
```

반면 다음 명령은 컨테이너 안에서 `sleep 5`를 실행한다.

```bash
docker run ubuntu sleep 5
```

```text
ubuntu 컨테이너
└─ sleep 5
   └─ 5초 후 종료
      └─ 컨테이너 종료
```

Dockerfile에 다음 설정이 있다고 가정한다.

```dockerfile
ENTRYPOINT ["sleep"]
CMD ["5"]
```

이 이미지는 기본적으로 `sleep 5`를 실행한다. Kubernetes에서는 다음처럼 대응된다.

| Dockerfile | Kubernetes Pod | 의미 |
| --- | --- | --- |
| `ENTRYPOINT` | `command` | 실행할 프로그램 |
| `CMD` | `args` | 프로그램에 넘길 인자 |

override 규칙은 다음과 같다.

| Pod YAML | 결과 |
| --- | --- |
| `command` 없음, `args` 없음 | 이미지의 `ENTRYPOINT`, `CMD` 그대로 사용 |
| `args`만 있음 | 이미지의 `ENTRYPOINT`는 유지, `CMD`만 override |
| `command`만 있음 | 이미지의 `ENTRYPOINT` override |
| `command`, `args` 둘 다 있음 | 이미지의 `ENTRYPOINT`, `CMD` 모두 override |

예를 들어 이미지 기본값이 `sleep 5`일 때 다음 Pod는 `CMD ["5"]`만 바꿔서 `sleep 10`을 실행한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      args: ["10"]
```

실행할 프로그램과 인자를 모두 명시하면 이미지 기본값에 덜 의존할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sleeper
spec:
  containers:
    - name: sleeper
      image: busybox
      command: ["sleep"]
      args: ["4800"]
```

최종 실행:

```bash
sleep 4800
```

실무에서는 일반 애플리케이션 Deployment에 `command`를 매번 직접 쓰기보다는 이미지에 정의된 `ENTRYPOINT`/`CMD`를 그대로 따르는 경우가 많다. `args` override는 실행 모드나 옵션을 바꿀 때 비교적 자연스럽고, `command` override는 이미지의 기본 실행 방식을 바꾸는 강한 개입이므로 더 신중하게 사용한다.

## CKAD Exam Focus

CKAD에서는 다음 mental model을 빠르게 떠올리면 된다.

```text
command = 실행할 프로그램
args    = 프로그램에 넘길 인자
```

```yaml
command: ["sleep"]
args: ["4800"]
```

는 결국 다음 명령을 컨테이너 시작 시 실행하라는 뜻이다.

```bash
sleep 4800
```

가장 중요한 암기 포인트:

```text
Kubernetes command는 Docker CMD가 아니다.
Kubernetes command = Docker ENTRYPOINT
Kubernetes args    = Docker CMD
```

## Related Concepts

- Dockerfile
- Docker `ENTRYPOINT`
- Docker `CMD`
- Kubernetes Pod `command`
- Kubernetes Pod `args`
- container main process
- shell and `bash`
