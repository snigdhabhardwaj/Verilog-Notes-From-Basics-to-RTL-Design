# Thought Experiment #4: "But My FSM Will Never Reach That State..."

Take a look at this code:

always @(*) begin
    case(state)
        2'b00: out = 0;
        2'b01: out = 1;
        2'b10: out = 0;
    endcase
end
 Before You Read Further

At first glance, this looks perfectly fine.

But here's the question:

**What should `out` become if `state = 2'b11`?**

Don't answer with *"That will never happen."*

Pretend you're the synthesizer. What would you do?

## The Problem Isn't the Code You Wrote...

It's the code you **didn't** write.

You've explained what happens for:

- `00`
- `01`
- `10`

But you've said absolutely nothing about `11`.****

Hardware doesn't know that your FSM is "supposed" to stay in valid states.

It only knows the behavior you've described.
So What Does the Hardware Do?

Imagine your circuit somehow enters `2'b11`.

Maybe it's because of:

- a reset glitch,
- an initialization issue,
- or simply an unknown (`X`) during simulation.

Now the hardware asks a simple question:

> **"What should I drive on `out`?"**

The RTL has no answer.

One possible outcome is that `out` simply holds its previous value, which can lead to latch inference or unintended behavior depending on the surrounding logic.

None of those outcomes are usually what we intended.

---

## The Safer Way

Always tell the hardware what to do when none of the listed cases match.

always @(*) begin
    case(state)
        2'b00: out = 0;
        2'b01: out = 1;
        2'b10: out = 0;
        default: out = 0;
    endcase
end

Now every possible value of `state` has a defined behavior.

The hardware never has to guess.
 Build the Hardware Intuition

One of the biggest mindset shifts in RTL is this:

> **Hardware isn't optimistic.**

It doesn't think,

> "The designer probably meant this."

It only implements exactly what you've described.

If there's a missing possibility, the hardware won't invent the right answer for you.

## Mental Model to Remember

> **Whenever you write a `case` statement, imagine a skeptical engineer asking, "Okay... but what if none of these cases happen?" If your RTL doesn't answer that question, it probably isn't finished yet.**

Question I Now Ask Myself 💭
"Have I told the hardware what to do in every possible situation?"
