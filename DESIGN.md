# kvstore design

## 1. Goal
A lightweight, custom key-value store built from scratch in Python to learn network programming, data structures, and persistence fundamentals.

## 2. Commands
PING            -> PONG
SET key value   -> OK                (the value may contain spaces)
GET key         -> the value, or (nil) when the key does not exist
DEL key         -> 1 if the key existed, else 0
EXISTS key      -> 1 or 0
KEYS            -> all keys, separated by spaces
QUIT            -> BYE (closes the connection)

## 3. Data model
Keys and values are text. Everything is kept in a hash table in memory.

## 4. Protocol
One command per line, one reply per line, over TCP, text encoded in UTF-8.

## 5. Key decisions
| Decision              | My choice | Why | Alternative |
|-----------------------|-----------|-----|-------------|
| Collision strategy    | Chaining | Simple and easy to explain | Open addressing (probe the next free slot) |
| Load factor to resize | 0.75 | Keeps the lists short | 0.5 (faster but more memory) |
| Hash function         | djb2-style (h = h * 33 + code) | Simple, spreads text keys well | Python's hash() (we write ours to learn) |
| Several clients       | One thread per client + a lock | Simplest correct design | An event loop (asyncio) |
| Durability            | Append-only log with fsync | Simple and safe | Periodic snapshots (faster, risk of data loss) |

## 6. Not included (out of scope)
Expiry, passwords, clustering, TLS, authentication.