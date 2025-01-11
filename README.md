# Wasabi - Battlesnake

Wasabi is a (spicy) RL-based Battlesnake.

This documents the specific approach to this Battlesnake algorithm while obfuscating competitive details.

## Architecture

The following is a high-level architecture overview of *Wasabi* battlesnake.

![Wasabi Architecture Diagram](assets/Wasabi%20Architecture%20Diagram.svg)

### The Intuition

As per the [Battlesnake API specification](https://docs.battlesnake.com/api/introduction), each snake has some endpoints that are called to play games. It's API design is stateless by nature, with moves for multiple games potentially being made at once. However, we want to maximize our performance advantage by caching prior calculations (i.e. "ponder"). Wasabi's moves are determined by a MCGS-based search guided by a policy network. Moves are cached in-memory and re-used given a matching hash.

## The Technicals

### The Policy

The policy makes up the **heart** of the algorithm. It is essentially a heavily modified version of a ResNet CNN. The model was trained with supervised reinforcement learning.

### Monte Carlo Graph Search (MCGS)

Monte Carlo Graph Search is a variation of Monte Carlo Tree Search (MCTS) that uses a policy network to guide the search ("planning"). In actual implementation, to leverage the GPU hardware, we batch subsequent game states for batch inference.

In order to maximize positions searched, we use Bitboards to represent each game state and either vectorized or SIMD operations for simulation. The bitboard representations are also conveniently aligned for converting into input tensors for the policy network.

Side Note: Since food generation in Battlesnake is arbitrary, we use approximate snake lengths through game progression, which does not appear to have a significant impact on simulations in testing.

## Putting it all together

We have a policy network trained with

## Areas Being Worked On

- [ ] Collaborative snakes: Could we gain a competitive advantage by using one policy (model) to control multiple snakes? Current difficulty is effectively modelling and training sufficiently strong policies across a larger action space.
- [ ] Lower inference costs: The amount of computation required to simulate deeply is high. Looking for ways (likely non-ML) to raise efficiency.

## License

This repository is primarily documentation. But we'll keep it as GPL-3.0.
