# AI Dungeon Planner
A browser game where the AI plans its entire route through a dungeon before it moves a single step, instead of just reacting to whatever's in front of it.
I built this for my Advanced AI coursework to actually show how STRIPS planning and A* search work, rather than just writing about them.

## What's the idea?
Most game AI is reactive — look at what's nearby, pick the best-looking move, repeat. This agent doesn't work like that. Before it takes a single step, it works out the entire path from start to goal, including which gems to grab on the way and which traps or enemies are worth walking around.

It does this with:

- **STRIPS actions** — every move (Move, Collect, Fight) has a precondition that has to hold and an effect that happens after
- **A\* search** — searches through possible world states (position, inventory, HP) and finds the cheapest way to the goal
- A heuristic of `Manhattan distance + uncollected gems × 4`, which keeps the search efficient without giving up the guarantee of finding the best plan

You can actually watch it think — it shows the full computed plan and its reasoning before it executes anything, then walks through it step by step on the map.

## Features
- 10 dungeon levels, starting at a simple 9×11 grid and going up to a 12×17 boss level
- Set your own start and goal by clicking on the map
- The full plan is shown before execution — you can see exactly what the AI decided and why
- Live HP, step count, gems collected, and total plan cost
- Speed slider for execution
- Traps take 25 HP, enemies take 40 HP — the planner avoids both when there's a way to

## How the planning actually works
Each action is defined the way STRIPS expects it:

```
MOVE
  Precondition: target cell is walkable, HP > 0
  Effect: agent is now at the target cell

COLLECT
  Precondition: agent is on a gem cell, gem not already picked up
  Effect: gem goes into inventory

FIGHT / TRAP
  Precondition: agent HP > damage the cell would deal
  Effect: agent moves to the cell, HP is reduced
```

A* searches over states defined as `(position, inventory, HP)` — not just position. That matters, because what the agent should do next depends on what it's already carrying and how much health it has left. It's what lets the planner choose something like "go the long way around this enemy" instead of always taking the shortest path regardless of cost.

The search keeps a priority queue (open list) ordered by `f(n) = g(n) + h(n)`, where `g(n)` is the actual cost so far and `h(n)` is the heuristic estimate to the goal. Because the heuristic never overestimates, the plan it finds is guaranteed to be optimal — not just "good enough."


## Tech

HTML, CSS, and vanilla JavaScript.
