# Aditya Chauhan

`hooiv` — compiler optimization passes, CPU pipelines, Rust systems.

I work on the layer where a wrong assumption becomes a wrong answer: offload decisions, pipeline hazards, cache bounds, request routing. Most of it is C++, Rust, and Verilog. The rest is TypeScript, when a system needs a face.

Most of my merged work is in private repositories, so the public evidence is what's below.

---

## Work

### [QuickLink](https://github.com/hooiv/urlshort) — routing, experiments, and attribution

TypeScript · Next.js 16 · React 19 · Prisma · PostgreSQL · ~30k lines

A multi-tenant link platform where routing, experiments, and conversion attribution are all decided per request, deterministically.

- **Experiments are sticky.** Weighted arms are bucketed by a SHA-256 of the visitor, so the same person doesn't flip arms between clicks.
- **Destinations are append-only.** Every change is a revision with a scheduled `effectiveAt`; releases roll back in one click, and history can't be rewritten.
- **Attribution doesn't leak the click.** HMAC-signed conversion tokens travel in the URL fragment, so they never reach the destination server. A JS snippet and `QuickLink.track()` close the loop.
- **Probes assume the destination is hostile.** Hostnames are DNS-resolved with private IPs blocked, every redirect hop is re-validated, and repeated failures trip a circuit breaker on a cron sweep.
- Five tenant roles, email-bound expiring invites, hashed revocable API keys. Tested with vitest, Playwright, k6 load runs, and a `test:soak` suite against Redis and a read replica.

Not deployed publicly. The repository is the artifact.

### [llvm-gpu-pass](https://github.com/hooiv/llvm-gpu-pass) — GPU offload decisions with a real cost model

C++ · LLVM · ~6.5k lines

An LLVM pass that decides whether a loop should run on the GPU. The decision comes from a per-architecture cost model, not from "this loop looks parallel."

- Models NVIDIA Maxwell through Ampere, AMD Vega through RDNA2 and CDNA2, and Intel Xe LP/HPG/HPC, so the same loop can get different answers on different silicon.
- Weighs computational intensity, available parallelism, memory access pattern, trip count, and transfer overhead before committing to an offload.

### [riscv](https://github.com/hooiv/riscv) — a five-stage RV32I core

Verilog · RV32I

IF/ID/EX/MEM/WB, with a separate ALU, control unit, register file, hazard unit, and pipeline registers.

- Forwarding from EX/MEM and WB into EX, with load-use stalls inserted only where forwarding can't cover the gap; branches flush the pipeline.
- `alu_tb.v` and `processor_tb.v` testbenches, plus synthesis scripts and SDC constraints targeting an Altera Cyclone IV at 50 MHz.

## Also public

**[llvm-tensor-opt](https://github.com/hooiv/llvm-tensor-opt)** — C++/CUDA, ~4.6k lines. LLVM passes for tensor fusion, vectorization, parallelization, and CUDA offload, with a PyTorch extension and a gtest suite.

**[redis-rate-limiter](https://github.com/hooiv/redis-rate-limiter)** — Python. A distributed token bucket as one atomic Redis Lua script: tiered policies, accurate `Retry-After`, async client, pytest suite, Docker.

## The revert I'd make again

In [wordcraft](https://github.com/hooiv/wordcraft), a Rust word processor, I bounded the paragraph layout cache at 32,768 entries. 159 layout tests passed and `cargo xtask ci` was green. Then I reverted it myself: the existing prune already capped the cache at 4× the working set, and my new test never reached the cap, so the bound was unsound and unproven.

A test that can't fail when the change is wrong isn't evidence.

*(`wordcraft` is a snapshot of an upstream codebase. The cache change and its revert are mine.)*

## Stack

<img alt="Rust" src="https://img.shields.io/badge/Rust-000000?style=flat-square&logo=rust&logoColor=white"> <img alt="C++" src="https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white"> <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white"> <img alt="Python" src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white"> <img alt="Verilog" src="https://img.shields.io/badge/Verilog-8A2BE2?style=flat-square"> <img alt="LLVM" src="https://img.shields.io/badge/LLVM-262D3A?style=flat-square&logo=llvm&logoColor=white"> <img alt="CUDA" src="https://img.shields.io/badge/CUDA-76B900?style=flat-square&logo=nvidia&logoColor=white">

**Systems** — LLVM, CUDA, PyTorch extensions, Redis, PostgreSQL, Prisma.

**Web and test** — Next.js, React, Tailwind, vitest, Playwright, k6, pytest, gtest, Docker.

## Contact

I don't publish an email address. The channel I actually read is an issue on any repository above.

Happy to talk compiler passes, CPU design, or a Rust codebase that needs a second pair of eyes.
