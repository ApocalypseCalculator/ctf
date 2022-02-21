# Joshua's Anger Challenge

### Problem 

A very obscure problem for lost souls. (and masochists)

### Solution

Running the app brings us to a login page asking for username and password and telling us that we are not admin. 

After extracting the apk, we can easily identify that this app is a React Native app. 

This means that all the JS code is inside the `/assets/index.android.bundle` file. 

Upon opening it, we discover that it is bytecode. This is a result of the Hermes compiler, and is a significant roadblock. 

Fortunately, Hermes provides an `hbcdump` tool and there is also an `hbctool` tool to disassemble and reassemble the bundle. 

Disassembling with hbctool, we are greeted with a gigantic file. However near the bottom, we can see a function called `checkPassword`. This must be the function that handles the login. 
```
Function<checkPassword>4073(1 params, 14 registers, 0 symbols):
	GetEnvironment      	Reg8:0, UInt8:0
	LoadFromEnvironment 	Reg8:2, Reg8:0, UInt8:0
	LoadConstString     	Reg8:1, UInt16:813
	; Oper[1]: String(813) 'admin'

	JStrictNotEqual     	Addr8:36, Reg8:2, Reg8:1
	LoadFromEnvironment 	Reg8:4, Reg8:0, UInt8:5
	LoadFromEnvironment 	Reg8:2, Reg8:0, UInt8:4
	LoadFromEnvironment 	Reg8:1, Reg8:0, UInt8:2
	LoadConstUndefined  	Reg8:3
	Call2               	Reg8:2, Reg8:2, Reg8:3, Reg8:1
	LoadFromEnvironment 	Reg8:1, Reg8:0, UInt8:6
	Call3               	Reg8:1, Reg8:4, Reg8:3, Reg8:2, Reg8:1
	JmpFalse             	Addr8:38, Reg8:1
	GetEnvironment      	Reg8:1, UInt8:1
	LoadFromEnvironment 	Reg8:1, Reg8:1, UInt8:2
	GetById             	Reg8:4, Reg8:1, UInt8:1, UInt16:3777
	; Oper[3]: String(3777) 'Alert'

	GetById             	Reg8:3, Reg8:4, UInt8:2, UInt16:4223
	; Oper[3]: String(4223) 'alert'

	LoadConstString     	Reg8:2, UInt16:929
	; Oper[1]: String(929) 'Login Failed'

	LoadConstString     	Reg8:1, UInt16:812
	; Oper[1]: String(812) "You're not admin!"

	Call3               	Reg8:1, Reg8:3, Reg8:4, Reg8:2, Reg8:1
	Jmp                 	Addr8:35
	GetEnvironment      	Reg8:1, UInt8:1
	LoadFromEnvironment 	Reg8:1, Reg8:1, UInt8:2
	GetById             	Reg8:3, Reg8:1, UInt8:1, UInt16:3777
	; Oper[3]: String(3777) 'Alert'

	GetById             	Reg8:2, Reg8:3, UInt8:2, UInt16:4223
	; Oper[3]: String(4223) 'alert'

	LoadFromEnvironment 	Reg8:1, Reg8:0, UInt8:2
	LoadConstString     	Reg8:0, UInt16:1539
	; Oper[1]: String(1539) 'Logged in'

	Call3               	Reg8:0, Reg8:2, Reg8:3, Reg8:0, Reg8:1
	LoadConstUndefined  	Reg8:0
	Ret                 	Reg8:0
EndFunction
```
Then we can simply invert the `JmpTrue` instruction to `JmpFalse` and reassemble it. Before attempting to install it, it must be repackaged and signed (a rather painful process). 

Going back to our login page, we discover that it was all a scam. The alert from logging in as admin literally displays your login password. Very uncool Joshua. 

Anyways, now we return to the disassembled file and scroll up to find an interesting function called `encode`. 
```
Function<encode>4071(2 params, 17 registers, 0 symbols):
	LoadConstZero       	Reg8:6
	LoadConstUInt8      	Reg8:5, UInt8:56
	LoadConstUndefined  	Reg8:4
	NewArray            	Reg8:0, UInt16:0
	GetEnvironment      	Reg8:1, UInt8:1
	LoadFromEnvironment 	Reg8:2, Reg8:1, UInt8:5
	LoadParam           	Reg8:1, UInt8:1
	Call2               	Reg8:3, Reg8:2, Reg8:4, Reg8:1
	Call1               	Reg8:2, Reg8:3, Reg8:4
	GetByIdShort        	Reg8:1, Reg8:2, UInt8:1, UInt8:163
	; Oper[3]: String(163) 'done'

	JmpTrue             	Addr8:48, Reg8:1
	GetByIdShort        	Reg8:8, Reg8:2, UInt8:2, UInt8:93
	; Oper[3]: String(93) 'value'

	GetByIdShort        	Reg8:7, Reg8:0, UInt8:3, UInt8:225
	; Oper[3]: String(225) 'push'

	GetById             	Reg8:1, Reg8:8, UInt8:4, UInt16:2886
	; Oper[3]: String(2886) 'charCodeAt'

	Call2               	Reg8:1, Reg8:1, Reg8:8, Reg8:6
	BitXor              	Reg8:1, Reg8:1, Reg8:5
	Call2               	Reg8:1, Reg8:7, Reg8:0, Reg8:1
	Call1               	Reg8:7, Reg8:3, Reg8:4
	GetByIdShort        	Reg8:1, Reg8:7, UInt8:1, UInt8:163
	; Oper[3]: String(163) 'done'

	Mov                 	Reg8:2, Reg8:7
	JmpFalse            	Addr8:-42, Reg8:1
	Ret                 	Reg8:0
EndFunction
```
This is about the closest we can get so we decided to reverse engineer this with the help of some [obscure documentation](https://github.com/facebook/hermes/blob/c2cd9e385a922f486a55e6bd70db2032f78379ed/include/hermes/BCGen/HBC/BytecodeList.def). We also notice that there is a XOR function with a constant value of 56 and that there are some suspicious array buffer values. (BattleMage_ did this part)

Our flag is then `CTF{I_am_the_only_one_that_is_allowed_to_have_eggs_right?}`