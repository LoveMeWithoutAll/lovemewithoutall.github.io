---
layout: single
title: Why We Chose XState for the 11Kitties Game
date: 2025-04-16 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend, xstate]
---

# Why We Chose XState for the 11Kitties Game

For the 11Kitties game, we chose XState to implement the core business logic. In typical frontend development, you manage state using React's useState, Redux, Zustand, and so on. But in the special context of a game, we judged that clarity of state transitions, development productivity, and quality management were what mattered most. In this article, I want to explain why XState is effective for web application game development, focusing on two main aspects.

## 1. Development Productivity and Quality: The Clarity of Finite State Machines

React is essentially an "Infinite State Machine." In other words, state changes freely according to conditions or user input, which means an infinite number of state combinations can occur. This makes it difficult to manage complex state-transition logic, and dependencies between states tend to get tangled, raising the likelihood of bugs.

XState, on the other hand, is based on a "Finite State Machine." A finite state machine declaratively defines a fixed set of states and clear transition rules, strictly constraining the possible states. It lets you explicitly specify the transitions allowed in each state and manage the behavior of each state in perfect isolation. As a result, code is easier to maintain and the chance of unexpected errors is reduced.

In general, a game implements its screens and behaviors through a very large number of state combinations. For example, when a player character has clearly defined states such as "idle," "moving," "attacking," and "defending," XState forces you to explicitly define only the transitions that are permitted from each state. This fundamentally prevents undefined state transitions, greatly reducing the risk of unexpected bugs or malfunctions.

For this "Kitties" project, there are various clearly defined states such as "eating," "playing," "using a booster," and "leveling up." If you were to manage state using only React's useState or Zustand, it would be difficult to separate the logic tied to each state, so every time you modify a particular state you would have to verify that all the other states still work correctly. But by completely isolating the logic of each state with XState, you can fundamentally avoid this kind of difficulty.

```
Infinite State Machine (React useState, etc.)
───────────────────────────────────────────
   State A ────┐
     │         │ User input/condition
     ▼         │
   State B ────┤
     │         │
     ▼         │
   State C ────┤ (free movement between all states is possible)
     │         │
     ▼         │
   State N ────┘ (the number of states is nearly infinite, and transition conditions are unclear)
───────────────────────────────────────────
⛔️ Downside: transition rules are unclear, making management hard and complex.


Finite State Machine (XState)
───────────────────────────────────────────
      ┌────────────────┐        ┌──────────────────┐
      │ State A (idle)  │───────▶│ State B (attack)  │
      └────────────────┘        └──────────────────┘
            ▲  │                    │  ▲
            │  │                    │  │
            │  ▼                    ▼  │
      ┌────────────────┐        ┌──────────────────┐
      │ State D (defend)│◀───────│ State C (move)    │
      └────────────────┘        └──────────────────┘
───────────────────────────────────────────
✅ Upside: clearly defined states and transition rules between them exist, making state easy to manage and greatly reducing the chance of errors.
```

## 2. Inversion of Control: The Convenience of Automatic Progression

The biggest difference between a game and a typical web application is that "automatic progression" scenarios occur frequently. For example, when a character levels up, or after a particular event completes, you need special flows such as the game state automatically transitioning to the next stage without any further user input, or skipping a stage according to business logic, or branching stage progression, or allowing only a specific input (e.g., a click event).

Manually managing these automatic-progression flows by hand is very complex and cumbersome for a developer. This is exactly where XState shines. Because the state machine in XState—rather than the user—takes control of the state flow, you can very easily implement logic that automatically transitions to the next state upon entering a particular state. This lets you separate roles so that React handles rendering and user-event processing, while XState handles the business logic that follows state changes.

In the case of 11Kitties, when a character reaches the "level up" state, you can declaratively define that it transitions to the "reward payout" state when needed, according to the transition rules declared in advance in the XState machine. Because XState manages this automated transition on its own, the developer simply needs to declaratively define the state flow and branching. Thanks to this, you can develop a game's automatic-progression events clearly and efficiently.

```
User                                  │            XState State Machine
──────────────────────────────────────┼─────────────────────────────────────
                                      │
[User input]                          │
(click, touch, etc.)───event passed───▶│───┐
                                      │   │ Event received
                                      │   ▼
                                      │┌────────────────────────┐
                                      ││ Event handling & state  │
                                      ││ decision                │
                                      ││ (applying the machine's │
                                      ││  declarative rules)     │
                                      │└─────────┬──────────────┘
                                      │          │
                                      │          ▼
                                      │┌─────────────────────────┐
                                      ││   Determine current state │
                                      ││ (e.g., idle, level up,    │
                                      ││  reward payout)           │
                                      │└─────────┬───────────────┘
                                      │          │
                                      │          ▼
                                      │┌───────────────────────┐
                                      ││ Run state-transition  │
                                      ││ logic                 │
                                      ││ (auto transition,     │
                                      ││  branching, etc.)     │
                                      │└─────────┬─────────────┘
                                      │          │
                                      │          │
                                      │◀─────────state update
                                      │
┌───────────────────────────────────┐ │
│React component (UI rendering,      │ │
│ animation, etc.)                   │ │
└───────────────────────────────────┘ │
              │                       │
              ▼                       │
[Check state and input on user screen]─▶│───────────┐
(repeats when a new event occurs)     │           │
                                      │           ▼
                                      │┌───────────────────────┐
                                      ││ Run state-transition  │
                                      ││ logic                 │
                                      ││ (auto transition,     │
                                      ││  branching, etc.)     │
                                      │└─────────┬─────────────┘
                                      │          │
                                      │          │
                                      │◀─────────state update                                      
┌───────────────────────────────────┐ │
│React component (UI rendering,      │ │
│ animation, etc.)                   │ │
└───────────────────────────────────┘ │                                      
──────────────────────────────────────┼─────────────────────────────────────
       Logic React handles            │     Logic XState handles
```

## 3. Things to Watch Out for When Using XState

XState is very useful, but there are a few things to keep in mind.

### Concentrate All Business Logic in XState

If logic is directly related to the state managed by the finite state machine, it's best to concentrate all of it inside the XState machine. If a particular piece of business logic changes, or a certain state is no longer used, and the logic is scattered across individual React components, you'll have to go through every component one by one to check and fix it—a cumbersome task. By delegating and isolating all state-related logic in XState, you can effectively reduce this unnecessary cost.

### Keep State Machines as Small as Possible

XState greatly improves development productivity, but if you cram all your states into a single, overly large machine, debugging becomes difficult. The more complex the machine gets, the longer the setup required to even begin debugging a particular situation. Compared to typical debugging in an FE application (e.g., clicking a button), it demands far more effort. So split your state machines into smaller pieces to manage them, and when necessary, combine several state machines together.

## Conclusion

XState is a tool very well suited to the clear state management and automatic-progression logic that game development demands. It resolves the complexity and ambiguity of typical React state management (useState, Zustand, Redux, etc.) and effectively meets the special requirements of game development.

In this 11Kitties project, by actively leveraging XState—clear state management, automatic progression through inversion of control, and so on—we were able to successfully improve development productivity, quality, and maintainability all at once.

I hope this article serves as a useful reference for developers considering adopting XState.

EOD

20250416
