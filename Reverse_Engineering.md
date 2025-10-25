
# What is reverse engineering?

It is to take a binary and reverse it to find out what it does. For example getting the source code from binary to see what the code is doing. 


# GDB Step 1:-

Can you figure out what is in the `eax` register at the end of the `main` function? Put your answer in the picoCTF flag format: `picoCTF{n}` where `n` is the contents of the `eax` register in the decimal number base. If the answer was `0x11` your flag would be `picoCTF{17}`.Disassemble [this](https://artifacts.picoctf.net/c/512/debugger0_a


## Solution:- 

```bash
gdb ./debugger0_a
disassemble main # this command to be ran inside gdb
```

The output of these commands is:-

```bash
Dump of assembler code for function main:
   0x0000000000001129 <+0>:	endbr64
   0x000000000000112d <+4>:	push   %rbp
   0x000000000000112e <+5>:	mov    %rsp,%rbp
   0x0000000000001131 <+8>:	mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:	mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:	mov    $0x86342,%eax
   0x000000000000113d <+20>:	pop    %rbp
   0x000000000000113e <+21>:	ret
End of assembler dump.

```

The hexadecimal no obtained is 0x86342

Which when converted to decimal is 549698
![Photo not loading](Screenshots/Pasted%20image%2020251024122433.png)



## Flag

picoCTF{549698}

## Concepts learned

What is assembly language
How to convert executable file into assembly
What are registers 

## Notes

Initially i was solving this problem by putting it directly into the dogbolt.org and then finding what the main function is returning but that is not the motive of the challenge as it requires me to use the GDB disassembler. 

## Resources

https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf 
https://www.rapidtables.com/convert/number/hex-to-decimal.htm


# ARMssembly 1

For what argument does this program print `win` with variables `79`, `7` and `3`? File: [chall_1.S](https://mercury.picoctf.net/static/eee77057c05086ff8bc47748cb1657ff/chall_1.S) Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})


## Solution

```bash
aarch64-linux-gnu-as chall_1.S -o chall_1.o
```

After this uploading the file on dogbolt.org in order to decompile the converted object file.

```c
uint64_t func(int32_t arg1) __pure
{
    int32_t var_10 = 0x4f;
    int32_t var_c = 7;
    int32_t var_8 = 3;
    int32_t var_4 = 0x2780;
    int32_t var_4_1 = 0xd2a;
    return 0xd2a - arg1;
}

int64_t main(int32_t arg1, void* arg2)
{
    int32_t var_14 = arg1;
    
    if (func(atoi(*(arg2 + 8))))
        return puts("You Lose :(");
    
    return puts("You win!");
}
```

From this we are able to see that 0xd2a is the hexadecimal number


## Flag

picoCTF{00000d2a}

## Concepts learned

Learnt how to convert an assembly file into object file. 
Usage of `aarch64-linux-gnu-as`

## Notes

I started to solve this question by trying to decompile assembly file. Then I understood that you cannot decompile an assembly then learnt how to convert assembly file to object file. However initially it wasn't working as the source file we were provided was or arm type so I had to use a different assembler for that than gcc. 

## Resources:-


dogbolt.org
https://www.rapidtables.com/convert/number/hex-to-decimal.htm


# Vault-door-3

This vault uses for-loops and byte arrays. The source code for this vault is here: [VaultDoor3.java](https://jupiter.challenges.picoctf.org/static/a4018cec1446761cb2e8cce05db925fa/VaultDoor3.java)

## Solution

I first analysed the .java file that was given for us to download

```java
import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        return s.equals("jU5t_a_sna_3lpm12g94c_u_4_m7ra41");
    }
}
```

From this I understood that what I need to do is to enter the correct input for which it gets converted to the given `"jU5t_a_sna_3lpm12g94c_u_4_m7ra41"` string. For this I created a python code which nothing more than just the opposite of what is in the given java code. Since I can't code in Java :(

```python
x = input("Enter string:")
y = [""] * 32

for i in range(0, 8):
    y[i] = x[i]
for i in range(8, 16):
    y[23 - i] = x[i]
for i in range(16, 32, 2):
    y[46 - i] = x[i]
for i in range(31, 16, -2):
    y[i] = x[i]
print("".join(y))
```

This output of this reverse engineering code was:-

```bash
Enter string:jU5t_a_sna_3lpm12g94c_u_4_m7ra41
jU5t_a_s1mpl3_an4gr4m_4_u_c79a21
```

## Flag

picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}


## Concepts learned

I learned how to reverse engineer the output to match the input

## Notes

I was initially making the mistake of trying to obstinately finding a way to print out the file directly without the use of list which wasted a lot of time. 
Also used an online java compiler since I don't know anything about java 
:(

## Resources

https://www.programiz.com/java-programming/online-compiler/

