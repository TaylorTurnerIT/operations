<img src="assets/icon.svg" width="64" height="64" alt="">

# operations

How Taylor's departments work together, and the threads that improve that system. The `operations` lead owns the mail rules, the directory, the proposals log, the lead skill, and usage (finance): quality and throughput without running out of usage early. The lead does not supervise other departments. It has no schedule of its own.

- [Department mail, v0](department-mail-v0.md): the pilot rules for cross-department requests.
- [Directory](directory.md): each department, its lead thread, and what to ask it for.
- [Proposals](proposals.md): improvements that anyone can propose.
- [lead-technical-department](skills/lead-technical-department/SKILL.md): the skill that department leads load.

## Related threads

Threads that belong here. T3 has no tool to move a thread between projects. The lead moves a thread with a row-level edit of `statev2.sqlite` in one transaction, with an undo file. The four settled threads are moved. The others still sit in No project until the lead has seen that moved threads stay put.

- [Cross-department email system](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/mcp:launch:470f502bac6c67d8358b665c5f1a628079409fc067c8b813ba27d8f58b7cf3b4): designed the mail rules and created the lead role.
- [Audit Department Lead Skill](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/f33c79fb-d6ee-44fa-a04e-7feeefa3ffce): audit of the lead skill. Snoozed until 2026-11-05T15:00Z.
- [Assess Per-Thread Usage Sustainability](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/0d37f2c1-9813-48db-8db7-619ea0dd610f): usage and model tiers. The schedule `Daily Codex tier-mix check` is bound to it. Snoozed until 2026-11-05T15:00Z.
- [Explore Council for Loss Minimization](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/af77b42e-cb52-407c-ad7a-da474988f521): whether multi-vendor review (Theo's Council skill) can drive the loss-minimizing workflow on qualitative work. It is expensive. Taylor holds it until a usage reset.
- Settled, for history: [Review DeepSeek Flash Usage Plans](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/7facbf94-4484-4e9d-bec4-cb4d3933394d), [Add Per-Project Usage Metrics](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/ee27f52d-dd18-4e25-accc-7b25f6089fe9), [Keep Only Free OpenCode Models](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/715efcda-2b80-42d2-ac60-d24cc77f995f), [Free DeepSeek Flash Subagent Reviews](t3-thread://v2/3e277669-74f3-4705-bb1b-012dfe5868ff/4099b63e-776d-4176-95db-ba7ce0b91b59).

Keep this checkout on `main`. Every lead loads the skill from it through a symlink, so a branch here changes the skill for all of them. Do other work in a worktree.
