# Thought Experiment #5: Why Does This Comparison Suddenly Fail?

Consider this piece of code:

reg [1:0] a = 2'b0x;
reg [1:0] b = 2'b0x;

if (a == b)
    $display("Equal");
else
    $display("Not Equal");


What gets printed?

- `Equal`
- `Not Equal`

Don't run the simulation yet.

## Most Beginners Think...

Both variables contain exactly the same value:


a = 0x
b = 0x

So naturally,

> "Of course they're equal."

It seems obvious.

But that's not what Verilog thinks.


## What Actually Happens?

Nothing gets printed.

but whyyyy
The comparison

a == b

doesn't return `1`.

It also doesn't return `0`.

It returns **`X` (unknown).**

And in an `if` statement, an `X` is treated as **false**.

That's why the `else` branch executes.


## Now Change Just One Character

Replace `==` with `===`.

if (a === b)
    $display("Equal");


Now the output becomes:

Equal


Why?

Because `===` compares **all four logic states**:

- `0`
- `1`
- `X`
- `Z`

It doesn't treat `X` as uncertainty.

It compares it as an actual value.

## Build the Hardware Intuition

Think of `==` as asking:

> "Are these values definitely equal?"

The moment an unknown (`X`) appears, Verilog honestly says,

> "I can't tell."

So the result itself becomes **unknown**.

On the other hand, `===` asks a different question:

> "Do these bits match exactly, including any unknowns?"

That's why it's called **case equality**.


## Where Is Each One Used?

In synthesizable RTL,

==

is almost always the right choice because real hardware doesn't contain `X` values.

In simulation and verification, however,

===

is incredibly useful.

Verification engineers often want to know whether two signals match **exactly**, including any unknown (`X`) or high-impedance (`Z`) states.

## Question I Now Ask Myself 💭

> **Am I comparing real hardware values, or am I comparing simulation values?**

If you're writing RTL, `==` is usually what you want.

If you're debugging a simulation or writing a testbench, `===` might save you hours of confusion.
