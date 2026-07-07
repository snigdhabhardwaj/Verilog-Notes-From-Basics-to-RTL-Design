# Thought Experiment #3: Where Does `out` Get Its Value?

Consider the following combinational logic:

always @(*) begin
    if (enable)
        out = data;
end


Before You Read Further

Question: What happens to `out` when `enable` is `0`?


i used to think
 "`out` becomes 0."

but
Is there **any** line that assigns `0` to `out`?

No.

In fact, nothing assigns **any** value to `out` when `enable` is `0`.

 Think Like the Hardware

Suppose the inputs are:

| Signal | Value |
|--------|------:|
| `enable` | `1` |
| `data` | `8'hA5` |

The output becomes:

```text
out = 8'hA5
```

Now `enable` changes to `0`.

What should `out` become?

The RTL never tells us.

Should it become:

- `0`?
- `8'hFF`?
- A random value?

The code doesn't specify.

The only way for hardware to satisfy this behavior is to **remember the previous value of `out`** until a new value is assigned.

Memory has just appeared in your design.

---

## What Hardware Gets Built?

Because `out` must remember its previous value whenever `enable` is `0`, the synthesizer infers a **level-sensitive latch**.

That isn't because Verilog has a "latch keyword."

It's because your RTL describes behavior that **requires memory**.

## How Do We Fix It?

Every possible execution path inside a combinational block must assign every output.


always @(*) begin
    if (enable)
        out = data;
    else
        out = 8'b0;
end


Now there is no ambiguity.

Whether `enable` is `1` or `0`, `out` always receives a value.

Since the output no longer needs to remember anything, no latch is required.



## Build the Hardware Intuition

Whenever you're writing combinational logic, ask yourself one question:

> **"If this condition is false, where does my output get its value?"**

If the answer is:

> "It keeps whatever value it already had."

You've just described memory.

And memory inside combinational logic means you've unintentionally designed a latch.

## Mental Model to Remember

> **Combinational logic computes outputs. It should never need to remember them. If an output isn't assigned on every possible path, the hardware has no choice but to store its previous value—and that storage is a latch.**

Engineering Habit 💡
Before saving any combinational always @(*) block, ask yourself: "Is every output assigned on every possible execution path?" If the answer isn't an immediate "yes," you're probably about to infer a latch.
