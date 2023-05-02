# Solutions for the EVM puzzles game

[EVM puzzles game](https://github.com/fvictorio/evm-puzzles)

## Puzzle #1

### Code
```
3456FDFDFDFDFDFD5B00
```

### Op-codes

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

### Steps

Let's use reverse engineering to resolve this issue.

We start of by figuring out that we want to end up on a `JUMPDEST` after the revert instructions.
In this case that is instruction where the program counter is `0x08`.

In this list of instructions we have only one `JUMP` operation and it takes a single stack argument which is the program counter where we should jump.

The new program counter must be a `JUMPDEST` operation, otherwise the EVM will abort with an error.

There is only one operation before the `JUMP` instruction and that is `CALLVALUE`.

The `CALLVALUE` instruction copies the call value to the stack as the first element.

### Answer
```
Value: 0x08
```

## Puzzle #2

### Code
```
34380356FDFD5B00FDFD
```

### Op-codes

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

### Steps

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

### Answer
```
Value: 0x04
```

## Puzzle #3

### Code
```
3656FDFD5B00
```

### Op-codes

```
[00]	CALLDATASIZE
[01]	JUMP
[02]	REVERT
[03]	REVERT
[04]	JUMPDEST
[05]	STOP
```

### Steps

Let's use reverse engineering to resolve this issue.

We start of by figuring out that we want to end up on the only `JUMPDEST` operation that is followed by a `STOP` instruction.
In this case that is instruction where the program counter is `0x04`.

In this list of instructions we have only one `JUMP` operation and it takes a single stack argument which is the program counter where we should jump.

The new program counter must be a `JUMPDEST` operation, otherwise the EVM will abort with an error.

The operation before `JUMP` is `CALLDATASIZE`.

This operation pushed the length of data (in bytes) to top of the stack.
The size of the data has to be 4 bytes to jump to `0x04` instruction.

Therefore we can provide any data that is 4 bytes long and this solves the puzzle.

### Answer
```
Data: 0x12345678
```

## Puzzle #4
### Code
```
34381856FDFDFDFDFDFD5B00
```
### Op-codes
[00]	CALLVALUE	
[01]	CODESIZE	
[02]	XOR	
[03]	JUMP	
[04]	REVERT	
[05]	REVERT	
[06]	REVERT	
[07]	REVERT	
[08]	REVERT	
[09]	REVERT	
[0a]	JUMPDEST	
[0b]	STOP
### Steps
Let's use reverse engineering to resolve this issue.

We start of by figuring out that we want to end up on the only `JUMPDEST` operation that is followed by a `STOP` instruction. In this case that is instruction where the program counter is `0x0a`.

In this list of instructions we have only one `JUMP` operation and it takes a single stack argument which is the program counter where we should jump.

The new program counter must be a `JUMPDEST` operation, otherwise the EVM will abort with an error.

The operation before `JUMP` is `XOR`.

This operation takes two stack arguments, performs exclusive or (XOR) operation on them and pushes the result to the top of the stack.
So we want the XOR operation on the two arguments `A` and `B` to result in the destination we are jumping to.

```
A ^ B = 0x0a
```

There are only two opcodes that are pushing something to the stack before the `XOR` and those are `CODESIZE` and `CALLVALUE`.

One of the easiest ways to figure out the needed value is to transform everything into binary.
If you take a look at the Op-codes for this puzzle you can see that the last program counter is `0x0b`.
Since the program counter starts from `0`, we add `1` to that to get the `CODESIZE`.

```
    1100
  ^
    value =
    1010
```

Looks like the value we need is `0110` or `6`.

### Answer
```
Value: 6
```
