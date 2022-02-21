# Joshua's Anger Challenge

### Problem 

A very obscure problem for lost souls. (and masochists)

### Solution

Running the app brings us to a login page asking for username and password and telling us that we are not admin. 

After extracting the apk, we can easily identify that this app is a React Native app. 

This means that all the JS code is inside the `/assets/index.android.bundle` file. 

Upon opening it, we discover that it is bytecode. This is a result of the Hermes compiler, and is a significant roadblock. 

Fortunately, Hermes provides an `hbcdump` tool and there is also an `hbctool` tool to disassemble and reassemble the bundle. 

Disassembling the hbctool, we are greeted with a gigantic file. However near the bottom, we can see a function called `checkPassword`. This must be the function that handles the login. Then we can simply invert the `JmpTrue` instruction to `JmpFalse` and reassemble it. Before attempting to install it, it must be repackaged and signed (a rather painful process). 

Going back to our login page, we discover that it was all a scam. The alert from logging in as admin literally displays your login password. Very uncool Joshua. 

Anyways, now we return to the disassembled file and scroll up to find an interesting function called `encode`. This is about the closest we can get so we decided to reverse engineer this with the help of some [obscure documentation](https://github.com/facebook/hermes/blob/c2cd9e385a922f486a55e6bd70db2032f78379ed/include/hermes/BCGen/HBC/BytecodeList.def). We also notice that there is a XOR function with a constant value of 56 and that there are some suspicious array buffer values. (BattleMage_ did this part)

Our flag is then `CTF{I_am_the_only_one_that_is_allowed_to_have_eggs_right?}`