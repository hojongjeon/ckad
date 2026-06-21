# CKAD Study Repository Guide

This repository is used to capture CKAD study notes from the Udemy course
`CKAD with Tests - Mumshad Mannambeth`.

AI sessions should use this guide when answering CKAD lecture questions and when
creating or updating study notes.

## Core Workflow

1. Answer the user's CKAD question first.
2. Track whether follow-up questions belong to the same learning thread.
3. When the question thread becomes stable, propose note capture.
4. If the user confirms, create or update a note in `notes/`.
5. Update `notes/index.md` whenever a note is created or renamed.
6. Do not create git commits unless the user explicitly asks.

The default capture mode is `Confirmed`: answer first, then ask before writing
or updating note files.

If the user explicitly says "저장해줘", "기록해줘", "노트로 남겨줘", or gives an
equivalent instruction, create or update the note without another confirmation.

## Note Unit

A note represents one resolved learning question thread, not necessarily one
user message.

Use one note when follow-up questions refine the same mental model, clarify the
same concept, ask for examples, ask for CKAD exam relevance, or add commands/YAML
for the same topic.

Split into separate notes when the conversation moves to a distinct Kubernetes
resource, tool, task pattern, troubleshooting scenario, or CKAD exam objective.

When the split is ambiguous, propose a concise structure before writing files.

## Directory Structure

Use a flat `notes/` directory by default.

```text
ckad/
  AGENTS.md
  notes/
    index.md
    course-catalog.md
    s02-l005-q001-docker-cri-containerd-dockershim.md
    s02-l005-q002-nerdctl-vs-kubectl.md
    unsorted-q001-pod-vs-container.md
```

Avoid adding deeper directory trees until there is a clear need.

## Course Catalog

To reduce repetitive metadata entry, maintain a course catalog when lecture data
is available.

Preferred file:

```text
notes/course-catalog.md
```

The catalog should list sections and lectures in a simple selectable format.

```md
# Course Catalog

## Section 02 - Core Concepts

- L005 - Pods
- L006 - Pods with YAML
- L007 - Practice Test: Pods
```

When lecture context is missing, the AI should first consult
`notes/course-catalog.md` if it exists. Then it should ask the user to choose
from likely Section/Lecture options instead of asking them to type full metadata.

If no catalog exists yet, ask for the minimum useful context:

- Section
- Lecture
- optional timestamp

If the user does not know the lecture context, use `unknown` metadata and place
the note under `Unsorted` in `notes/index.md`.

## File Naming

Use this format for normal notes:

```text
notes/sXX-lYYY-qZZZ-slug.md
```

Rules:

- `sXX`: section number, zero-padded.
- `lYYY`: lecture number, zero-padded. Prefer the Udemy lecture number if known.
- `qZZZ`: question-thread number within that lecture.
- `slug`: short English kebab-case summary.

Examples:

```text
notes/s02-l005-q001-docker-cri-containerd-dockershim.md
notes/s02-l005-q002-nerdctl-vs-kubectl.md
notes/s03-l014-q001-replicaset-selector-matchlabels.md
```

If metadata is unknown, use:

```text
notes/unsorted-qZZZ-slug.md
```

Rename the file later when lecture context becomes known.

## Note Template

The template is a default structure, not a mandatory checklist. Include only
sections that are useful for the current question thread. Do not create empty
sections.

Required sections:

```md
# Q001 - Title

## Metadata

## Question Thread

## Short Answer

## Explanation
```

Optional sections:

```md
## Visual Notes
## CKAD Exam Focus
## Commands
## YAML
## Related Concepts
## Connected Notes
## Follow-up Questions
## References
```

## Metadata Format

Keep metadata keys consistent even when values are unknown.

```md
## Metadata

- Course: CKAD with Tests - Mumshad Mannambeth
- Section: 02 - Core Concepts
- Lecture: 005 - Pods
- Timestamp: unknown
- Date: 2026-06-21
- Tags: docker, cri, containerd, runc, dockershim
- Status: answered
```

Allowed `Status` values:

- `draft`: captured but not fully organized.
- `answered`: the user's confusion is directly answered.
- `needs-review`: metadata, facts, commands, or structure need review.
- `verified`: answer has been checked against official docs, local practice, or
  a reliable source.

## Question Thread Format

Use multiple subquestions when a conversation spans several turns.

```md
## Question Thread

### Q1. Kubernetes는 처음에 Docker를 런타임으로 썼나요?

### Q2. Docker는 왜 CRI와 맞지 않았나요?

### Q3. Docker는 컨테이너 런타임인가요, 플랫폼인가요?
```

Do not split every user message into a separate file. Preserve the learning
flow when the follow-up questions belong to the same concept.

## Visual Notes

Prefer visual explanations when they make the concept easier to review.

Useful formats:

- Mermaid `flowchart` for resource relationships and control flow.
- Mermaid `sequenceDiagram` for API server, scheduler, kubelet, and runtime
  interactions.
- Mermaid `stateDiagram` for lifecycle and status transitions.
- Markdown tables for comparisons.
- ASCII diagrams for simple terminal-friendly hierarchy.
- `diff` blocks for manifest corrections.
- Blockquotes for exam traps or important cautions.

Do not force a diagram when text or a table is clearer.

## Commands and YAML

Include commands when the question has practical CKAD value.

Use `bash` fences for commands:

```bash
kubectl get pods
kubectl describe pod nginx
```

Use `yaml` fences for manifests:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
```

Prefer exam-useful commands and minimal manifests over broad examples.

## Note Capture Timing

Do not rush to create a note after the first answer if the user appears to be
actively exploring the same topic.

Propose note capture when:

- the user's confusion has been resolved,
- the conversation naturally shifts to a new topic,
- the user explicitly asks to save or organize the content,
- the answer has enough stable structure to be useful later.

Signals such as "이해했습니다", "정리됐습니다", or similar reactions can indicate
that the current question thread is ready to document.

## Completion Criteria

A question thread is ready to document when:

- the original confusion is directly answered,
- CKAD-relevant implications are included when applicable,
- useful commands or YAML are included when helpful,
- unresolved assumptions are listed as follow-up questions,
- missing lecture metadata is explicitly marked as `unknown`.

## Index Rules

Every note must be linked from `notes/index.md`.

Group known notes by Section and Lecture:

```md
# CKAD Notes Index

## Section 02 - Core Concepts

### L005 - Pods

- [Q001 - Docker, CRI, containerd, and dockershim](s02-l005-q001-docker-cri-containerd-dockershim.md)
- [Q002 - nerdctl vs kubectl](s02-l005-q002-nerdctl-vs-kubectl.md)

## Unsorted

- [Q001 - Pod vs container](unsorted-q001-pod-vs-container.md)
```

## Updating Existing Notes

Before creating a new note, check whether a note for the same question thread
already exists.

Update an existing note when the new conversation adds clarification, examples,
commands, YAML, diagrams, exam focus, related concepts, or corrections to the
same thread.

Create a new note when the new conversation can be reviewed independently or has
a distinct CKAD objective.

## Commit Messages

Do not commit automatically.

When the user asks for a commit, use Conventional Commits:

```text
docs(ckad): add note for docker cri runtime flow
docs(ckad): update pod vs container note
docs(ckad): add course catalog
chore(ai): update study guide
```

## Style

- Write notes primarily in Korean unless the user asks otherwise.
- Keep Kubernetes object names, commands, field names, and API terms in English.
- Prefer concise explanations with clear mental models.
- Optimize notes for later review and search, not for transcript preservation.
- Preserve useful user questions, but rewrite noisy conversation into clean
  study material.
