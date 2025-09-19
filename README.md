Roadmap: From Zero → Solid C + Sockets
---
Phase 0 — Setup & Tooling (1 day)
**Tools:**
- Compiler: `gcc` or `clang`
- Debugger: `gdb`
- Memory tools: `valgrind`, AddressSanitizer (ASan)
- Build tool: `make` (later `CMake`)
- Formatter/linter (optional but great): `clang-format`, `clang-tidy`
**Quick test:**
```bash
echo 'int main(){return 0;}' > hello.c
gcc -Wall -Wextra -O0 -g -fsanitize=address hello.c -o hello && ./hello
```
---
Phase 1 — C Foundations (3–5 days)
**Goal:** Be fluent with types, functions, control flow, arrays, strings.
**Learn:**
- Basic types (`int`, `unsigned`, `float`, `char`), casting
- Functions, headers, compilation units
- Control flow: `if/else`, `for`, `while`, `switch`
- Arrays & strings (`char[]`, `'■'`), `strlen`, `strcpy`, `strncpy`, `strtol`
**Exercises:**
- Write `max3(int a, int b, int c)` and unit-test it with a simple `main`.
- Parse `argv`: program prints the sum of all integer arguments.
- Implement `reverse(char *s)` in place; test with `"networking"`.
- Implement `trim(char *s)` (remove leading/trailing spaces).
---
Phase 2 — Pointers, Structs, Dynamic Memory (4–6 days)**Goal:** Deep understanding of pointers and struct-based data grouping.
**Learn:**
- Pointers: `&`, `*`, precedence; arrays vs pointers; const-correctness
- `struct` and `->`, nested structs
- `malloc`, `calloc`, `realloc`, `free`
- Ownership rules & lifetime; avoid leaks/double frees
**Exercises:**
- `swap(int *a, int *b);`
- `struct Person { char name[64]; int age; }`
Array of 5 people, sort by age.
- Build a dynamic `IntVector`:
```c
struct IntVec { int *data; size_t len, cap; };
```
Functions: `init`, `push`, `get`, `free`
- Singly linked list: `push_front`, `pop_front`, `find`, `destroy`. Verify with Valgrind.
---
Phase 3 — Files, FDs vs FILE*, Error Handling (2–3 days)
**Goal:** Comfort with syscalls and stdio.
**Learn:**
- `open/read/write/close` vs `fopen/fgets/fprintf/fclose`
- `perror`, `errno`, return-value checks
- Binary vs text I/O
**Exercises:**
- Copy a file (`cat.c`) using `read/write` (4KB buffer).
- Count lines and longest line using `fgets`.
- Implement a tiny CSV reader that prints the N-th column.
---
Phase 4 — Build Systems, Debugging, Testing (2–3 days)
**Goal:** Debug crashes and catch memory bugs early.
**Flags:**
`-Wall -Wextra -Wshadow -Wconversion -O0 -g`**Tools:**
- ASan: `-fsanitize=address,undefined`
- gdb basics: `run`, `bt`, `break`, `p var`, `si`, `ni`
- Valgrind:
```bash
valgrind --leak-check=full ./prog
```
**Exercises:**
- Introduce an out-of-bounds write and catch it with ASan.
- Add a simple Makefile.
---
Phase 5 — Sockets (Main Target) (5–7 days)
**Goal:** Write correct TCP clients/servers that handle partial I/O.
**Learn:**
- `socket`, `bind`, `listen`, `accept`, `connect`, `close`
- `struct sockaddr_in`, `htons/htonl`, `inet_pton/ntop`
- TCP as a byte stream → length-prefixed protocol
- `send/recv` partial I/O → `send_all/recv_all`
- Timeouts: `SO_RCVTIMEO`, `SO_SNDTIMEO`
**Exercises:**
1. Echo server: accepts one client, echoes until EOF.
2. Length-prefixed client/server exchange.
3. Looping server: accept sequential clients in a loop.
4. Multi-client server with `select()`.
5. (Optional) IPv6 + `getaddrinfo`.
---
Phase 6 — Robustness & Concurrency (Optional, 4–7 days)
**Concurrency options:**
- `fork()` per client
- `pthread` per client
- Single-threaded `select/poll/epoll`**Other:**
- Clean shutdown (SIGINT)
- Message framing & state machines
- Simple protocol: key-value server (`SET k v`, `GET k`)
**Exercises:**
- Multi-client echo with timeout (disconnect after 60s).
- Tiny in-memory KV server over TCP.
---
Phase 7 — Polishing & Real-World Habits
- Project structure with CMake
- Logging macros (`LOG_INFO`, `LOG_ERR(errno)`)
- Consistent style with `clang-format`
- Static analysis with `clang-tidy`
- Basic fuzzing (afl++ or libFuzzer)
---
Suggested Daily Plan (2–3 weeks)
- **Week 1:** Phases 1–2 (fundamentals + pointers/structs)
- **Week 2:** Phases 3–5 (I/O + sockets)
- **Week 3:** Phases 5–7 (multi-client server + polish)
---
Exercise Pack
Pack A — Core C
- A1: `atoi_safe(const char*, int *out)`
- A2: `join_with(char **words, size_t n, char sep)`
- A3: Ring buffer (`rb_init`, `rb_write`, `rb_read`, `rb_size`)
Pack B — Data Structures
- B1: Dynamic string builder
- B2: Hash table (open addressing)Pack C — Files & Parsing
- C1: `grep`-lite (substring match)
- C2: Log stats by log level
Pack D — Networking
- D1: Echo server with `select` (64 clients max)
- D2: Line protocol server
- D3: Length-prefixed chat broadcast
Pack E — Robustness
- E1: Add `SO_REUSEADDR`, `SO_RCVTIMEO`, SIGINT shutdown
- E2: Fuzz line parser with random inputs
---
Minimal Makefile
```makefile
CC := gcc
CFLAGS := -Wall -Wextra -Wshadow -Wconversion -O0 -g -fsanitize=address,undefined
LDFLAGS := -fsanitize=address,undefined
all: server client
server: server.c common.o
$(CC) $(CFLAGS) $^ -o $@ $(LDFLAGS)
client: client.c common.o
$(CC) $(CFLAGS) $^ -o $@ $(LDFLAGS)
common.o: common.c common.h
$(CC) $(CFLAGS) -c common.c
clean:
rm -f server client *.o
```
---
IDE Recommendations1. **VS Code** (recommended: free, lightweight, great extensions)
2. **CLion** (paid, free for students, full CMake integration)
3. **Qt Creator** (free, solid debugging)
4. **Neovim/Vim** (power user setup with clangd, dap)
**My pick for you:** VS Code + gcc/clang + gdb + valgrind.
---
Pro Tips
- Always check return values; use `perror("context")` on failure.
- Treat warnings as errors (`-Werror`).
- Manage buffers carefully (capacity + length).
- For sockets: never assume one `recv()` = one message.
- Commit often, run under ASan/Valgrind frequently.
---
