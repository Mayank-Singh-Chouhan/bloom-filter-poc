# Bloom Filter Username Checker

## Overview

This project is a simple demonstration of a **Bloom filter**.

The application checks if a username may already exist.

It uses:

- React
- JavaScript
- A Bloom filter
- An in-memory list as a simulated database

There is no backend or external database.

## How It Works

The application uses two steps to check a username.

```text
Username
    ↓
Bloom Filter
    ↓
Is the username possibly present?
    ↓
Yes ──→ Check database
    ↓
No
    ↓
Username is available
```

The Bloom filter is used as a fast first check.

### Step 1: Add Users

When a user is added:

1. The username is converted to lowercase.
2. The username is passed to three hash functions.
3. Each hash function returns a position in the bit array.
4. The application sets these positions to `1`.
5. The username is also added to the simulated database.

### Step 2: Check a Username

When the user enters a username:

1. The application runs the three hash functions.
2. It checks the three positions in the bit array.
3. If one position is `0`, the username is **definitely not present**.
4. If all positions are `1`, the username **may be present**.
5. The application then checks the simulated database.

## Why Use a Bloom Filter?

A Bloom filter can reduce the number of database checks.

For example:

```text
Username: xyz123

Bloom Filter
     ↓
One bit is 0
     ↓
Definitely not present
     ↓
Do not check database
```

For an existing username:

```text
Username: mayank

Bloom Filter
     ↓
All bits are 1
     ↓
May be present
     ↓
Check database
     ↓
Username is taken
```

## False Positives

A Bloom filter can produce a **false positive**.

This means:

> The Bloom filter says that an item may exist, but the item does not exist in the database.

Example:

```text
Bloom Filter → Maybe present
Database     → Not present
```

This is normal behavior.

A standard Bloom filter does **not** produce false negatives.

If the Bloom filter says:

```text
Definitely not present
```

the item is not in the set.

## Hash Functions

The demo uses three hash functions.

They use different seed values:

```text
Hash 1 → seed 7
Hash 2 → seed 13
Hash 3 → seed 37
```

The seed is the starting value for the hash calculation.

The same hash function is used with different seeds. This produces different positions in the bit array.

The basic implementation is:

```js
hash(value, seed) {
  let hash = seed;

  for (let i = 0; i < value.length; i++) {
    hash = (hash * 31 + value.charCodeAt(i)) % this.size;
  }

  return hash;
}
```

The `% this.size` operation keeps the result inside the bit array.

For example, if the bit array has `64` positions, the result is between:

```text
0 and 63
```

## Bit Array

The demo uses a bit array with 64 positions.

Initially:

```text
0 0 0 0 0 0 0 0 ...
```

When a username is added, the hash functions select positions.

For example:

```text
Hash 1 → 12
Hash 2 → 31
Hash 3 → 48
```

The application sets these positions to `1`.

```text
0 0 0 ... 1 ... 1 ... 1 ...
          ↑     ↑     ↑
         12    31    48
```

Different usernames can use the same position.

This is one reason why false positives can occur.

## Simulated Database

The project does not use a real database.

It uses a JavaScript array:

```js
const users = [
  "mayank",
  "john",
  "alice",
  "admin",
  "developer",
  "botsync"
];
```

You can add new usernames from the UI.

When you add a username:

1. It is added to the array.
2. It is added to the Bloom filter.
3. The bit array is updated.
4. The username appears in the database list.

The data is stored only in memory.

If you refresh the page, the added usernames are lost.

## UI Features

The demo provides:

- Username availability check
- Bloom filter bit array
- Three hash results
- Hash function implementation
- Expandable hash-function sections
- Simulated database
- Add username functionality
- False-positive demonstration
- Light and dark mode support
- Responsive layout

## Important Limitation

This project is an educational demo.

It does not provide:

- A real backend
- Persistent storage
- A real database
- Secure username validation
- Production-level hash functions

The hash function is simple so that the Bloom filter process is easy to understand.

## Example

Try:

```text
mayank
```

The Bloom filter should report that the username may exist.

The simulated database then confirms:

```text
Username is taken
```

Now try:

```text
xyz123
```

The Bloom filter may find a `0` bit.

The application can then report:

```text
Username is available
```

## Goal of the Project

The main goal is to show how a Bloom filter can work as a **fast pre-check before a database lookup**.

It helps explain:

- Hash functions
- Seeds
- Bit arrays
- Membership checks
- False positives
- Database lookups
- Memory-efficient data structures