### Challenge: No Way OUT

### Description

We are given a Windows/Mac game and asked to find the flag.

After launching the game (pico.exe), we are placed inside a small map.
There is a message at the top: “Escape to find the flag.”

We can:

* Move using W, A, S, D
* Jump, run, crouch
* Climb ladders
* Switch weapons

There is a visible white flag outside the wall, which clearly looks like the goal.

However, even after reaching the top, there is an invisible wall blocking us. So normal gameplay will not work.

### Approach

Since this is a Unity game, the logic is stored in C# scripts.
I opened the following file:

/win/pico_Data/Managed/Assembly-CSharp.dll

using dnSpy, which is a .NET reverse engineering tool.

### Finding the Logic

Inside the code, I found the jump condition:

if (Input.GetButton("Jump") && this.canMove && this.characterController.isGrounded && !this.isClimbing)
{
this.moveDirection.y = this.jumpSpeed;
}
else
{
this.moveDirection.y = y;
}

---

### Understanding It

This means:

* You can only jump if:

  * You are allowed to move
  * You are on the ground
  * You are not climbing

So mid-air jumping is not allowed.

### Exploit

To bypass the invisible wall, I modified the condition.

I removed this part:

this.characterController.isGrounded

Now the condition allows jumping even in mid-air.

### Result

After saving the modified DLL and reopening the game:

* I was able to jump infinitely in the air
* I crossed the invisible wall
* I reached the flag

As soon as I touched the flag, the real flag appeared on the screen.

### Final Step

Wrap the flag like this before submitting:

picoCTF{REDACTED}

### Conclusion

This challenge looks difficult at first because of the invisible wall, but the solution is to modify the game logic instead of playing normally.

Instead of solving it as a player, we solve it as a reverse engineer.
