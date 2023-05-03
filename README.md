Download Link: https://assignmentchef.com/product/solved-cs2100-lab-5-making-function-calls
<br>
In this lab, you will use <strong>QtSpim </strong>to explore the idea of function calls in MIPS Assembly Code. This document and its associated files (<strong>sayHi.asm</strong> and <strong>arrayFunction.asm</strong>) can be downloaded from the CS2100 module website or found in the ZIP file.

<h2>Task 1: Getting started (sayHi.asm)</h2>

Just like any high level programming language, modularization (separating code into welldefined procedures/functions) is an important idea for assembly programming. Conceptually, making function call is actually simple: we need to “<em>jump</em>” to another portion of code (the function body) then start executing the instructions in the function body. When we reach the end of that function, another “<em>jump</em>” is needed to go back to the caller.

<table width="226">

 <tbody>

  <tr>

   <td width="76"><strong>0x3024 </strong></td>

   <td width="150">#Body of Function <strong>F</strong></td>

  </tr>

  <tr>

   <td width="76"><strong>0x3028 </strong></td>

   <td width="150">#Body of Function <strong>F</strong></td>

  </tr>

  <tr>

   <td width="76"><strong>0x302C </strong></td>

   <td width="150">#Done! Jump back to caller</td>

  </tr>

 </tbody>

</table>







So, the simplest kind of function call can be accomplished by just two “<em>jump</em>” instructions! To facilitate function calls, MIPS gives us two variants of the “<strong>j</strong>” instructions, the “<strong>jal</strong>” (jumpand-link) and the “<strong>jr</strong>” (jump by register). Don’t worry, they are much easier than the name suggested.

First, download and load the assembly program “<strong>sayHi.asm</strong>” in QtSpim. The original content of the file is given on the next page.

<table width="586">

 <tbody>

  <tr>

   <td width="586"><strong># sayHi.asm </strong><strong>      .data  </strong><strong>str1: .asciiz “Before function
” str2: .asciiz “After function
” </strong><strong>str3: .asciiz “Inside function: Say Hi!
” </strong><strong>      .text main:       li   $v0, 4    </strong><strong># system call code for print_string</strong> <strong>    la   $a0, str1 </strong><strong># address of string to print</strong><strong>     syscall        </strong><strong># print the string</strong><strong> </strong><strong>    </strong><strong>jal</strong><strong> sayHi      </strong><strong># Make a function call to sayHi()</strong><strong> </strong><strong>    li   $v0, 4    </strong><strong># system call code for print_string</strong><strong>     la   $a0, str2 </strong><strong># address of string to print</strong><strong>     syscall        </strong><strong># print the string</strong><strong> </strong><strong>    </strong><strong># End of main, make a syscall to “exit”</strong> <strong>    li   $v0, 10   </strong><strong># system call code for exit</strong><strong>     syscall        </strong><strong># terminate program</strong><strong> </strong><strong># start of function sayHi()</strong> <strong>sayHi:      li   $v0, 4    </strong><strong># system call code for print_string</strong><strong>     la   $a0, str3 </strong><strong># address of string to print</strong><strong>     syscall        </strong><strong># print the string</strong><strong>                   </strong><strong># Use “jr” to go back to caller </strong><strong> </strong></td>

  </tr>

 </tbody>

</table>




The intention of the program is to print 3 messages in the following order:







The first and third messages are printed in the “<strong>main</strong>” function while the second message is printed by the “<strong>sayHi</strong>” function. The given program is almost complete, with only one missing instruction. The purpose of this code is to demonstrate the necessary instructions needed for a making a function call.

Now, let us step through the program to make several observations. Use the “Single Step” button or press <strong>F10</strong> to go through the program line by line. Stop when you reach the instruction “<strong>jal sayHi</strong>“.




Answer: The instruction address of “<strong>jal sayHi</strong>” is at <strong>0x</strong><strong>_______________</strong>




Press <strong>F10 </strong>one more time to execute the “<strong>jal</strong>” instruction. Answer the following:




Answer: The <strong><sub>PC</sub></strong> is now at <strong><sub>0x</sub></strong><strong>_______________</strong>

Answer: The register <strong>$31</strong> now contains <strong>0x</strong><strong>_______________</strong>

At this point, you should be able to see why the name of register <strong>$31</strong> is <strong>$ra</strong> (return address). Express the content of register <strong>$31</strong> with respect to the instruction address of the corresponding “<strong>jal</strong>” instruction. Use the notation <strong>Addr(jal)</strong> to indicate the instruction address of “<strong>jal</strong>” instruction.




<h2>Answer: <sub>$31</sub> = _______________</h2>




If you continue stepping through, we will reach the end of the “<strong>sayHi</strong>” function and get ‘stuck’. We need a way to go back to the main function <strong>and continue from where we left off</strong>. We can do this easily by the “<strong>jr</strong>” (jump by register) instruction which is missing in the program. This “<strong>jr</strong>” instruction takes a <strong>register number </strong>as operand. It will jump to the address stored in the specified register. For example,

<strong>jr</strong> <strong>$15</strong>

The content of register <strong>$15</strong> will be used as the target address. This is known as <strong>direct addressing</strong> (the address is directly specified in full).

What is the correct register number to be used in the “<strong>jr</strong>” instruction so that we can jump back to main?

Answer: <strong><sub>jr</sub></strong> <strong>______</strong>




Now, edit your code and insert the “<strong>jr</strong>” instruction accordingly. Run your program, you should see the 3 messages in the same order as shown in the earlier output screenshot.







<strong> </strong>




<h3>Task 2: Let’s share information (arrayFunction.asm)</h3>

We can now turn to other aspects of function call, namely function parameters (arguments) and function return value. Actually, we have encountered this idea in previous labs. Take a look at this very familiar sequence of using the system call <strong>read_int</strong>:

<strong>     li $v0, 5         </strong><strong># System call code for read_int</strong><strong>      syscall            </strong>

<strong>     sw $v0, 0($t1)    </strong><strong># “return result” is in $v0</strong>




You can see that there is an agreement to use the register <strong>$v0</strong> to store the system call code for the system call (a special kind of function call). Additionally, the return result (an integer read from user) is placed in register <strong>$v0</strong> when the system call is completed.

<strong>Key idea: we can pass information to the function by placing values in registers and retrieve the return result in the same way. </strong>

<strong> </strong>

Let us first attempt to pass information to a function. Download and load the <strong>arrayFunction.asm</strong> in QtSpim. The main function code is given below:

<table width="586">

 <tbody>

  <tr>

   <td width="586"><strong>       .data  </strong><strong>array: .word 8, 2, 1, 6, 9, 7, 3, 5, 0, 4 newl:  .asciiz “
” </strong><strong> </strong><strong>       .text </strong><strong>main:  </strong><strong>    </strong><strong># Print the original content of array </strong><strong>    # setup the parameter(s) </strong><strong>    # call the printArray function </strong><strong> </strong><strong>    </strong><strong># Ask the user for two indices</strong><strong>    li   $v0, 5          </strong><strong># System call code for read_int</strong><strong>     </strong><strong>syscall</strong> <strong>           </strong><strong>    addi $t0, $v0, 0     </strong><strong># first user input in $t0</strong><strong> </strong><strong> </strong><strong>    li   $v0, 5          </strong><strong># System call code for read_int</strong><strong>     </strong><strong>syscall</strong><strong>            </strong><strong>    addi $t1, $v0, 0       </strong><strong># second user input in $t1</strong><strong> </strong><strong>    # Call the findMin function </strong><strong>    # setup the parameter(s) </strong><strong>    # call the function </strong><strong> </strong><strong>    # Print the min item </strong><strong>    </strong><em>&lt;code not shown&gt;</em> <strong>    # Calculate and print the index of min item </strong><strong>    </strong><em>&lt;code not shown&gt;</em><strong>     </strong><strong> </strong><strong>    # End of main, make a syscall to “exit” </strong><strong>    li   $v0, 10        </strong><strong># system call code for exit</strong><strong>     syscall             </strong><strong># terminate program</strong></td>

  </tr>

 </tbody>

</table>







The basic flow of the program is as follows:

<ol>

 <li>Print the original content of array.</li>

 <li>Ask the user for two indices <strong>X</strong> and <strong>Y</strong>, where X </li>

 <li>Find the minimum item between A[X] and A[Y] (inclusive).</li>

 <li>Print the minimum item and the index of the minimum item.</li>

</ol>

You’ll need to code for parts 1, 3 and 4. Again, don’t panic as most of the code are already written! For part 1, the following function is already given in the program:

<table width="592">

 <tbody>

  <tr>

   <td width="592"><strong>###   Function printArray   ###  </strong><strong># Input: Array Address in $a0, Number of elements in $a1 </strong><strong># Output: None </strong><strong># Purpose: Print array elements </strong><strong># Registers used: $t0, $t1, $t2 </strong><strong># Assumption: Array element is word size (4-byte) </strong><strong>printArray:       addi $t1, $a0, 0    </strong><strong># $t1 is the pointer to the item</strong><strong>       sll  $t2, $a1, 2    </strong><strong># $t2 is the offset beyond the last item</strong><strong>       add  $t2, $a0, $t2  </strong><strong># $t2 is pointing beyond the last item</strong><strong> loop:        beq  $t1, $t2, end </strong><strong>      lw   $t3, 0($t1)    </strong><strong># $t3 is the current item</strong><strong>       li   $v0, 1         </strong><strong># system call code for print_int</strong><strong>       </strong><strong>addi $a0, $t3, 0    </strong><strong># integer to print</strong> <strong>      syscall             </strong><strong># print it </strong><strong>      addi $t1, $t1, 4 </strong><strong>      j loop              </strong><strong># Another iteration</strong><strong> end:       li   $v0, 4         </strong><strong># system call code for print_string</strong><strong>       la   $a0, newl      </strong><strong>#</strong><strong>  </strong><strong>      syscall             </strong><strong># print newline</strong><strong>      </strong><strong>jr $ra              </strong><strong># return from this function</strong></td>

  </tr>

 </tbody>

</table>

The comments at the beginning of the function give you a good idea of how to make use of this function. Pay special attention to the “input” information, which tells you where to place the expected parameters. <strong>Without</strong> changing this function, complete the first part of the main program. You only need to place the correct information in the registers <strong>$a0</strong> and <strong>$a1</strong> then make a function call. Test your program, and you should see the original content of array printed on screen. (Hint: Don’t forget the use of “<strong>li</strong>” and “<strong>la</strong>” instructions).

Now, let’s tackle something slightly more challenging. Let us now write a function to find the minimum element. The function header is given in the program as follows:

<strong>################################################################ ## </strong>

<strong>###   Function findMin                                         ###  </strong>

<strong># Input: Lower Array Pointer in $a0, Higher Array Pointer in $a1 </strong>

<strong># Output: $v0 contains the address of minimum item  </strong>

<strong># Purpose: Find and return the minimum item  </strong>

<strong>#              between $a0 and $a1 (inclusive) </strong>

<strong># Registers used: &lt;Fill in with your register usage&gt; # Assumption: Array element is word size (4-byte), $a0 &lt;= $a1 </strong><strong>findMin: </strong>

<strong>      </strong><strong># Your implementation here</strong>

<strong>      jr $ra                  </strong><strong># return from this function</strong>

Note that the function expects <strong>two addresses, </strong>i.e. the addresses of A[X] and A[Y] in registers <strong>$a0</strong> and <strong>$a1</strong> respectively. Once the minimum item is found, the <strong>address </strong>of the minimum item is returned to the caller. You are supposed to use the <strong>array pointer </strong>approach (Lecture #8, Slide 34 &amp; Tutorial #3, Q1b) to implement the <strong><em>findMin</em></strong> function.

Once you have written the findMin function, you are left with the last piece of puzzle to solve. How do you find out the <strong>index</strong> of an item from the <strong>address </strong>of the item? (Hint: think about how we calculate the address of an item given the index of the item.)

Complete the main function by calling the <strong><em>findMin</em></strong> function. Then print both the minimum item and the index of the minimum item.

Below are two separate test runs (user input circled):