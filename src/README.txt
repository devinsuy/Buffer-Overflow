-----------------------------------
Devin Suy
GitHub: https://github.com/devinsuy
LinkTree: http://devinsuy.com/
-----------------------------------

--------------
Included Files
--------------
	- "exploit.c"
	- "vuln.c"
	- "Demo.PNG"
	- "gdb_1.PNG"
	- "gdb_2.PNG"
	- "gdb_3.PNG"
	- "gdb_4.PNG"
	- "ADR_JMP_SIZE.PNG"


-----------------------
Development Enviornment
-----------------------
	- Oracle VM VirtualBox
	- Red Hat Linux release 6.2 (Zoot)
	- Kernel 2.2.14-5.0
	- i686


--------------
Implementation
--------------
	- As detailed in Aleph one's paper "Smashing The Stack For Fun And Profit" the structure of program execution uses a stack with the structure:		
		(low mem 0x00000) text ... data ... heap =>  ...    [vulnbuffer][ebp][sfp ][ret ][paramaters][function]   ...  <= stack ... kernel (high mem 0xFFFFF)
	
	- Here we can see that not too far from the end of the designated buffer[10] in vuln.c is the stored return which we exploit

	- This can be taken advantage of because vuln.c uses strcpy to copy the passed paramater into the buffer without any length checking, similar functions
	  in C that can be exploited are sprintf and gets
	
	- In the case of a larger size buffer (that exceeds the length of our payload) we would be able to simply build a payload with the following structure:
		[ NO-OP ... NO-OP ... LAUNCH SH INSTRUCTIONS ... RET Pointing back in ... RET Pointing back in ]
	  and pass this as an argument to the function, which will blindly copy it into the buffer, overflow it and launch our shell.
	  Leading the SH instructions with NO-OP decreases our need for high precision for addressing, so we can ideally place the SH instructions in the middle

	- Since the size of the buffer in vuln.c is notably smaller, we will build the same structure and use as a secondary payload and utilize environmental
	  variables instead. Modern OS has preventative measures that mitigate Buffer Overflow in general such as: adress randomization, stack canary, analysis, etc.

	- This alternate implementation for small size buffer instead builds an initial payload with reference addresses, overflow will transfer control
      to our secondary payload and launch our shell all the same. GDB can be used to see values in register to observe this taking place more closely.


------------------------
Reference Links Utilized
------------------------
	Resources:
		https://inst.eecs.berkeley.edu/~cs161/fa08/papers/stack_smashing.pdf
		http://www.cis.syr.edu/~wedu/seed/Book/book_sample_buffer.pdf
		https://payatu.com/blog/Siddharth-Bezalwar/understanding-stack-based-buffer-overflow#:~:text=EBP%20points%20to%20higher%20memory,next%20instruction%20to%20be%20executed.&text=When%20a%20function%20is%20executed,is%20pushed%20onto%20the%20stack.
		http://www.cis.syr.edu/~wedu/Teaching/CompSec/LectureNotes_New/Buffer_Overflow.pdf
		https://0xrick.github.io/binary-exploitation/bof5/
		https://www.tenouk.com/Bufferoverflowc/Bufferoverflow1.html
		https://owasp.org/www-community/attacks/Buffer_overflow_attack
		https://www.exploit-db.com/exploits/47008
		https://owasp.org/www-community/attacks/Buffer_Overflow_via_Environment_Variables#:~:text=This%20attack%20pattern%20involves%20causing,often%20placed%20in%20environment%20variables.


	Documentation:
		https://gcc.gnu.org/onlinedocs/gcc/Option-Summary.html
		https://linux.die.net/man/3/system
		https://man7.org/linux/man-pages/man2/execve.2.html
		https://linuxhint.com/exec_linux_system_call_c/#:~:text=In%20execl()%20system%20function,command%20and%20prints%20the%20output.
		https://visualgdb.com/gdbreference/commands/
		https://web.eecs.umich.edu/~sugih/pointers/summary.html
		http://kirste.userpage.fu-berlin.de/chemnet/use/info/gdb/gdb_7.html

	Other:
		https://defuse.ca/online-x86-assembler.htm
		https://godbolt.org/
		https://www.sitepoint.com/community/t/differece-between-machine-type-i686-and-i386/5666
		https://www.codetable.net/
		https://www.calculator.net/hex-calculator.html
		https://www.onlinegdb.com/
		http://archive.download.redhat.com/pub/redhat/linux/6.2/en/iso/i386/
		
		
		