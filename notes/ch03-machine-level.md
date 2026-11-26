# Chapter 3: Machine-Level Representation of Programs

## Where I am
- My notes reach operand specifiers (3.4). I'm probably somewhere in 3.4–3.5.
- Next: the 15-213 Lecture 05 at 1.5× (skip the history at the start), then Lectures 06 (Control) and 07 (Procedures).
- At the desk: about half of PP 3.6–3.12 (3.5 Arithmetic).
- Not answered yet: the placement and Ch 2 check questions below.

## Ch 2 ideas that ch 3 uses
| Ch 2 | Ch 3 |
|---|---|
| Zero and sign extension | `movz`, `movs` |
| Logical and arithmetic right shift | `shr`, `sar` (`sal` is the left shift) |
| Multiplying with shifts and adds | `leaq` |
| Unsigned and signed overflow | condition codes CF and OF (3.6) |
| Signed and unsigned comparison | `setl`/`jl` (signed) vs `setb`/`jb` (unsigned) |

## Placement questions
Answer each one before you open it.

<details><summary>Q1 (3.5): <code>%rax</code> = x, then <code>salq $3, %rax</code>. What is in <code>%rax</code>?</summary>

`8x`. A left shift by 3 multiplies by 2³.
</details>

<details><summary>Q2 (3.6): <code>%rdi</code> = a, <code>%rsi</code> = b. After <code>cmpq %rsi, %rdi</code> and <code>setl %al</code>, when is <code>%al</code> = 1?</summary>

When `a < b`. AT&T syntax puts the operands in reverse order: `cmpq %rsi, %rdi` computes `a - b`, and `setl` means "set if less, signed".
</details>

<details><summary>Q3 (3.6.5): does the jump in a compiled <code>if (x &gt; y) {...} else {...}</code> go to the <code>if</code> branch or the <code>else</code> branch?</summary>

To the `else` branch. The compiler reverses the condition (`if (!(x > y)) goto else;`) and falls straight through into the `then` code. That is the `goto` template in 3.6.5.
</details>

<details><summary>Q4 (3.5.1): <code>%rdi</code> = x. What does <code>leaq (%rdi,%rdi,2), %rax</code> compute? Why would a compiler use <code>leaq</code> for arithmetic?</summary>

`3x` (x + x·2). `leaq` only calculates an address and never reads memory, so it can do an add plus a multiply by 1, 2, 4 or 8 in one instruction. It also leaves the condition codes alone.
</details>

<details><summary>Q5 (Lecture 05): <code>%rdx</code> = 0xf000, <code>%rcx</code> = 0x100. What address does <code>0x80(%rdx,%rcx,2)</code> refer to?</summary>

`0xf000 + 0x100·2 + 0x80 = 0xf280`.
</details>

## Ch 2 check questions

<details><summary>1. <code>%rax</code> = -8, then <code>sarq $1, %rax</code>. What is the result? What would <code>shrq</code> give instead?</summary>

`sarq` gives `-4`, because the arithmetic shift copies the sign bit. `shrq` gives `0x7FFFFFFFFFFFFFFC`, a huge positive number, because the logical shift fills with 0.
</details>

<details><summary>2. <code>leaq</code> can multiply by 1, 2, 3, 4, 5, 8 or 9 in one instruction. Why not 7?</summary>

`leaq` computes `base + index·scale` with scale 1, 2, 4 or 8, and it can only add. 7 needs `8x - x`, which is a subtraction, so it takes two instructions.
</details>

<details><summary>3. <code>int x = -1; unsigned u = 0;</code> Is <code>x &lt; u</code> true or false?</summary>

False. `x` is converted to unsigned and becomes `UINT_MAX`, which is not less than 0.
</details>
