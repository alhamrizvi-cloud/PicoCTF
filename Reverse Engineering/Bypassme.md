# Bypass Me — picoCTF 2026

A password-protected ELF binary that decodes its password at runtime, making static analysis ineffective.

## Reconnaissance
```bash
file bypassme.bin
# ELF 64-bit LSB pie executable, x86-64, not stripped
strings bypassme.bin | grep pico
# nothing — password is runtime-decoded
```

Opening in GDB and disassembling `main()` revealed the key flow:

1. `decode_password()` is called first — password is built in memory at runtime
2. User input is sanitized via `sanitize()`
3. `strcmp()` compares sanitized input against the decoded password
4. On success, it opens a flag file and prints


Set a breakpoint at the `strcmp` call and inspect registers at the moment of comparison:

```gdb
gdb bypassme.bin
b *0x0000555555555759
run
# enter any input when prompted: test
x/s $rdi   # → "test"       (our input)
x/s $rsi   # → "SuperSecure" (decoded password)
```

Since `strcmp(rdi, rsi)`, `$rsi` holds the expected password.

Running on the challenge server with password `SuperSecure`:
```
picoCTF{...}
```

When `strings` finds nothing, the secret is decoded at runtime. A breakpoint at `strcmp` before the comparison executes lets you read both arguments directly from registers — no need for angr or patching.
