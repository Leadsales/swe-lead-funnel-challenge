# Lead Funnel Service - Technical Test

Welcome to the **Lead Funnel Service** technical test.

> **How to start**: click **"Use this template"** to create a copy in your own GitHub account, and please set its visibility to **Private**. Share access with your interviewer when requested.

At Leadsales, a workspace organizes its leads in a **funnel**: a board of ordered stages (e.g. `New`, `Contacted`, `Qualified`, `Closed`). Leads enter the funnel and move between stages as the sales conversation progresses.

This test has **two parts**:

- **Part 1 — Implementation** (~45 min): implement the core domain logic.
- **Part 2 — Systems Analysis** (~15 min written, discussed afterwards): reason about how this design behaves in the real world.

We are as interested in **how you think** as in the code you write. Before writing any code, read everything, form a mental model, and **ask the clarifying questions you need**. Think out loud throughout.

## Project Structure

```bash
src/
├── domain/
│   ├── entities/              # Domain models (Lead, Funnel)
│   ├── repositories/          # Abstract interfaces
│   └── errors/                # Domain exceptions
├── application/
│   └── use-cases/             # Business logic (use cases)
├── infrastructure/
│   └── persistence/           # In-memory persistence adapter
└── index.ts                   # Entry point for simulation
```

## Business Rules

1. A lead is identified by its **phone number**. The same phone number cannot exist twice in a funnel.
2. A lead can only be moved to a stage that **exists** in the funnel.
3. Each stage may define a **capacity limit** (maximum number of leads it can hold). Moving or adding a lead into a full stage must be rejected.
4. Moving a lead to the stage it is already in is not a valid transition.

> The rules above do not cover every situation. Deciding what is underspecified — and asking about it — is part of the test.

## Part 1 — Implementation

1. Implement the `AddLeadToFunnel` use case:
   - Validate that no lead with the same phone number already exists in the funnel.
   - Use `DuplicateLeadError` if a duplicate is found.
   - New leads always enter the funnel's **first stage** (capacity rules apply).
   - Save valid leads to the repository.

2. Implement the `MoveLeadToStage` use case:
   - Validate the target stage exists (`StageNotFoundError`).
   - Validate the target stage has capacity (`StageCapacityExceededError`).
   - Validate the transition is valid (`InvalidStageTransitionError`).

3. Complete the `Lead` and `Funnel` entities with appropriate validations.

4. Implement the `InMemoryLeadRepository`:
   - Save leads.
   - Find leads by phone number and by stage.

5. Simulate the funnel flow in `index.ts` with at least: one lead added, one valid move, one duplicate rejected, and one invalid move rejected.

### Guidelines

- Follow **Hexagonal Architecture**: keep domain logic isolated.
- Respect **SOLID principles** and **Clean Code** practices.
- Use **TypeScript** types and interfaces correctly.
- Keep your code readable, modular, and well-named.

### Bonus

- Add a function to simulate HTTP request handling (input/output as JSON).
- Add simple unit tests for the funnel business rules.

## Part 2 — Systems Analysis

Answer in `ANALYSIS.md`. Short, concrete answers beat long generic ones — bullet points are fine. There is no single right answer; we want to see the **failure modes you anticipate** and the **trade-offs you weigh**.

1. **Concurrency.** Two sales agents move the *same lead* to *different stages* at the same time, and a third agent moves *another lead* into a stage with one slot left. What can go wrong with your current design? Where would you enforce correctness if the repository were a real database?

2. **Integration.** When a lead changes stage, other Leadsales-like services need to react (e.g. analytics, notifications). How would you communicate the change without coupling this service to its consumers? What new failure modes does your choice introduce?

3. **Scale.** A funnel grows to 500,000 leads and the board UI needs "the first 50 leads of each stage, most recent first". What breaks first in your current design, and what would you change?

4. **Evolution.** Product now wants **per-workspace configurable rules** (e.g. one customer forbids backward moves, another requires an approval to enter `Closed`). How does your design absorb this without a rewrite? What would you refuse to build, and why?

## Time Limit

Aim for **~1 hour total**: ~45 minutes for Part 1, ~15 minutes for Part 2. It is better to deliver Part 1 solid plus thoughtful partial answers than everything rushed.

Good luck, and happy coding!
