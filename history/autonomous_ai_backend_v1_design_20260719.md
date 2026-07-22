# Autonomous AI Backend V1 Design

V1 adds bounded Research Round, Hypothesis, Memory Proposal, Assistance Request, Provider Profile, and Provider Secret state while retaining the existing Campaign, execution transport, Result Ingestion, Factor Center, and MiningEvent facts.

The `/api/v2/autonomous/readiness` projection separates local research, real Provider, real Single, and Consultant Multi readiness. Every projection exposes configured, validated, armed, running, ready, blockers, warnings, and evidence. A closed Gate is a warning for configuration but forces real execution `armed=false`.

The server Mock profile is capped at two rounds, three unique candidates per round, and six total executions. Fixture Skill output must still bind server Registry fields and profiles. Client payloads cannot select real execution or enlarge those limits.
