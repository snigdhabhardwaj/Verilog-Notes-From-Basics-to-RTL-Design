# Thought Experiment #1: Does `c` Get the New Value of `a`?

Consider the following RTL:

```verilog
always @(posedge clk) begin
    a <= b;
    c <= a;
end
```

## Before You Read Further...

**Question:** After the next rising edge of the clock, what value does `c` capture?

* The **new** value of `a`
* The **old** value of `a`

Take a moment to make a prediction before reading the explanation.

---

## Let's Test It

Assume the registers contain the following values just **before** the rising edge of the clock:

| Register | Value |
| -------- | ----: |
| `a`      |   `0` |
| `b`      |   `1` |
| `c`      |   `0` |

When the clock rises, the hardware behaves as follows:

* `a` samples the current value of `b` (`1`).
* `c` samples the current value of `a` (`0`).

Only after **both** registers have sampled their inputs do their outputs update.

Therefore, immediately after the clock edge:

| Register | Value |
| -------- | ----: |
| `a`      |   `1` |
| `b`      |   `1` |
| `c`      |   `0` |

Notice that `c` captured the **old** value of `a`, not the new one.

---

## Why Does This Happen?

Many beginners read this code exactly as they would read C or Python:

```verilog
a <= b;
c <= a;
```

Since the first line appears before the second, it's natural to assume that `a` changes first and `c` reads the updated value.

That isn't how synchronous hardware works.

A clocked `always` block does **not** describe a sequence of operations. Instead, it describes what each register should capture **at the same clock edge**.

At every rising edge:

1. Every flip-flop samples its input **simultaneously**.
2. Each sampled value is temporarily held.
3. After all sampling is complete, every register updates its output together.

Because `c` samples `a` **before** `a`'s output changes, it receives the previous value of `a`.

---

## Build the Hardware Intuition

Imagine every register in your design has a camera.

When the clock edge arrives, every camera takes a snapshot at the exact same instant.

No register can see another register's newly updated value during that snapshot.

Only after every snapshot has been taken do all the registers update their outputs together.

Once you start thinking of a clock edge as a **simultaneous snapshot**, non-blocking assignments (`<=`) stop feeling like a syntax rule and start making intuitive sense.

---

## Mental Model to Remember

> **A clocked `always` block is not a list of instructions executed one after another. It is a description of multiple flip-flops that all sample their inputs simultaneously on the same clock edge.**

