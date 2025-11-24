# Dynamic Contact Network

### What is this problem for?

As part of the effort to combat a virus outbreak in 2020, the city administration created a dynamic contact tracing system. The system must not only register new contacts between residents but also allow analysts to "roll back" recent events to model various scenarios. Your task is to implement a program capable of efficiently processing a large stream of events.

### Problem Description

Initially, there are `n` residents in the city (numbered from 1 to `n`). Each resident represents a separate contact group of size 1.

You will receive a sequence of `m` commands of three types:

1.  `contact u v` — Registers a contact between residents `u` and `v`. If they are already in the same contact group, nothing happens. Otherwise, their groups are merged into one.
2.  `size u` — Outputs the current size of the contact group that resident `u` is in.
3.  `undo` — Reverts the most recent successful `contact` operation. If no `contact` operations have occurred yet, or if all have been reverted, this command is ignored. `size` and `undo` commands cannot be reverted.

You need to correctly process all commands and, for each `size` command, print the corresponding result.

### Input Format:

*   The first line contains two integers, `n` and `m` — the number of residents and the number of commands, respectively.
*   The next `m` lines contain the commands, one per line. The format of the commands matches the description above.

### Output Format:

*   For each `size` command, print a single integer (the size of the corresponding group) on a new line.

### Constraints

*   `1 ≤ n ≤ 200,000`
*   `1 ≤ m ≤ 200,000`
*   `1 ≤ u, v ≤ n`
*   In `contact u v` commands, it is guaranteed that `u ≠ v`.

### Example

**Input:**
```
10 12
contact 1 2
contact 3 4
size 1
contact 1 3
size 4
undo
size 4
contact 5 6
contact 1 5
size 1
undo
size 1
```

**Output:**
```
2
4
2
4
2
```