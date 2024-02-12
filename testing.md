```mermaid

flowchart TD
    A[Unit test]----->B[vitest]
    C[Component test]--->D[Functional test]--->E[@vue-test-utils]
    C[Component test]--->C2[Visual test]--->E2[storybook+chromatic]
    F[E2E test]--->F1[Functional test]--->F2[cypress]
    F[E2E test]--->F3[Visual test]--->F2[cypress]
    D[Functional test]-..->|interactive|F2[cypress]

```

[![](https://mermaid.ink/img/pako:eNp9kctugzAQRX8FWUIsGiKV7ligpjx-oI-N3YVrhmIFbGSPqRDh32vaqihK2lnNtc-948dMhK6BpCQMZ6kkpsEcNZ3-EC03GAWrFM6MsLaRRRgODYKJlmUJQ6aY-mWDp4KpwNeBPvucAMHia7xW9kBH-SW_gZzmuh-0ArVRWUErpwRKrXi3rZb0fnQQrzp2KDv7X0Se0Bdp3Zk_oRa1md60Pt6I1uieoxQ_IRUtk3Jjq9urR6gSKqbBgLV_2O4upl5Yrlxuv4-zk1T-LbnfGOF0ZiI70oPpuaz9z8xrCCPYQg-MpL6tuTkywtTiOe5QP05KkBSNgx1xQ80RCsnfDe9J2vDOwvIJ4H-kAA?type=png)](https://mermaid.live/edit#pako:eNp9kctugzAQRX8FWUIsGiKV7ligpjx-oI-N3YVrhmIFbGSPqRDh32vaqihK2lnNtc-948dMhK6BpCQMZ6kkpsEcNZ3-EC03GAWrFM6MsLaRRRgODYKJlmUJQ6aY-mWDp4KpwNeBPvucAMHia7xW9kBH-SW_gZzmuh-0ArVRWUErpwRKrXi3rZb0fnQQrzp2KDv7X0Se0Bdp3Zk_oRa1md60Pt6I1uieoxQ_IRUtk3Jjq9urR6gSKqbBgLV_2O4upl5Yrlxuv4-zk1T-LbnfGOF0ZiI70oPpuaz9z8xrCCPYQg-MpL6tuTkywtTiOe5QP05KkBSNgx1xQ80RCsnfDe9J2vDOwvIJ4H-kAA)

|                |            | vitest | vue-test-utils | storybook | chromatic | cypress |
| -------------- | ---------- | ------ | -------------- | --------- | --------- | ------- |
| Unit test      | Functional | ✅     | ✅             | ✅        |           | ✅      |
| Component test | Functional |        | ✅             | ✅        |           | ✅      |
|                | Visual     |        |                |           | ✅        | ✅      |
| E2E test       | Functional |        |                |           |           | ✅      |
|                | Visual     |        |                |           |           | ✅      |
