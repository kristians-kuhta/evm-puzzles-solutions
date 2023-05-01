# Solutions for the EVM puzzles game

## Puzzle #1

Op-codes:

```
[00]	CALLVALUE
[01]	JUMP
[02]	REVERT
[03]	REVERT
[04]	REVERT
[05]	REVERT
[06]	REVERT
[07]	REVERT
[08]	JUMPDEST
[09]	STOP
```

Steps:

Let's use reverse engineering to resolve this issue.

We start of by figuring out that we want to end up on a `JUMPDEST` after the revert instructions.
In this case that is instruction where the program counter is `0x08`.

In this list of instructions we have only one `JUMP` operation and it takes a single stack argument which is the program counter where we should jump.

The new program counter must be a `JUMPDEST` operation, otherwise the EVM will abort with an error.

There is only one operation before the `JUMP` instruction and that is `CALLVALUE`.

The `CALLVALUE` instruction copies the call value to the stack as the first element.


Therefore it makes sense that one must provide `0x08` as the value for the call to ensure that the code reaches the `STOP` instruction without reverting.

## Puzzle #2

Op-codes:

```
[00]	CALLVALUE
[01]	CODESIZE
[02]	SUB
[03]	JUMP
[04]	REVERT
[05]	REVERT
[06]	JUMPDEST
[07]	STOP
[08]	REVERT
[09]	REVERT
```

Steps:

Let's use reverse engineering to resolve this issue.

We start of by figuring out that we want to end up on the only `JUMPDEST` operation that is followed by a `STOP` instruction.
In this case that is instruction where the program counter is `0x06`.

In this list of instructions we have only one `JUMP` operation and it takes a single stack argument which is the program counter where we should jump.

The new program counter must be a `JUMPDEST` operation, otherwise the EVM will abort with an error.

The operation before `JUMP` is `SUB`.

The `SUB` operation takes two stack arguments the value and the subtracted value.

Since the two preceding operations are `CODESIZE` and `CALLVALUE` we can conclude that the value is being subtracted from the code size.

If we take a look at the code, there are 10 instructions, none of whom take inline arguments.

So the total size of code is 10 bytes.

Therefore it makes sense that one must provide `0x04` as the value for the call to ensure that the code reaches the `STOP` instruction without reverting.

```
0x0a - 0x04 = 0x06 (prog. counter we are jumping to)
```
