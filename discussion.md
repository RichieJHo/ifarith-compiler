# Discussion and Reflection


The bulk of this project consists of a collection of five
questions. You are to answer these questions after spending some
amount of time looking over the code together to gather answers for
your questions. Try to seriously dig into the project together--it is
of course understood that you may not grasp every detail, but put
forth serious effort to spend several hours reading and discussing the
code, along with anything you have taken from it.

Questions will largely be graded on completion and maturity, but the
instructors do reserve the right to take off for technical
inaccuracies (i.e., if you say something factually wrong).

Each of these (six, five main followed by last) questions should take
roughly at least a paragraph or two. Try to aim for between 1-500
words per question. You may divide up the work, but each of you should
collectively read and agree to each other's answers.

[ Question 1 ] 

For this task, you will three new .irv programs. These are
`ir-virtual?` programs in a pseudo-assembly format. Try to compile the
program. Here, you should briefly explain the purpose of ir-virtual,
especially how it is different than x86: what are the pros and cons of
using ir-virtual as a representation? You can get the compiler to to
compile ir-virtual files like so: 

racket compiler.rkt -v test-programs/sum1.irv 

(Also pass in -m for Mac)

Answer: 
The purpose of ir-virtual is to make unique machine-independent assembly representation of racket code which can make development and optimization easier. What makes x86 and ir-virtual different is that ir-virtual is not tied to any particular hardware platform and allows for higher level of abstraction, focusing on program structure and semantics rather than low-level details like specific CPU instructions. One advantage of ir-virtual is the hardware independence, making it a more available and accessible option. One con of ir-virtual is the learning curve, since ir-virtual introduces new layers of abstraction it could be harder for one to perform optimizations. 


[ Question 2 ] 

For this task, you will write three new .ifa programs. Your programs
must be correct, in the sense that they are valid. There are a set of
starter programs in the test-programs directory now. Your job is to
create three new `.ifa` programs and compile and run each of them. It
is very much possible that the compiler will be broken: part of your
exercise is identifying if you can find any possible bugs in the
compiler.

For each of your exercises, write here what the input and output was
from the compiler. Read through each of the phases, by passing in the
`-v` flag to the compiler. For at least one of the programs, explain
carefully the relevance of each of the intermediate representations.

For this question, please add your `.ifa` programs either (a) here or
(b) to the repo and write where they are in this file.

 I added all 3 of our ifa to the test-programs directory, they are labeled test0.ifa, test1.ifa, test2.ifa respectively. For test0, the input was (* (+ 2 3) 5), and the output was 25. For test1, the input was (if (equal? (+ 2 2) 4) #t #f) and the output was
 an error. Finally for test2 the input was 289765, and the output was 289765.  If we examine test2 thoroughly we can see that when running the input starts at stage 1 and gets desugared to stage 2, ifarith-tiny. After going through irarith-tiny it gets sent
 to stage 3 which is ANF and it turns into a simple argument. It then gets sent to IR-Virtual which turns it into assembly using virtual registers. After becoming assembly it goes to x86 which is the stack allocation. Finally it goes down one more last layer
 and creates a nasm file which is the assembled file.  



[ Question 3 ] 

Describe each of the passes of the compiler in a slight degree of
detail, using specific examples to discuss what each pass does. The
compiler is designed in series of layers, with each higher-level IR
desugaring to a lower-level IR until ultimately arriving at x86-64
assembler. Do you think there are any redundant passes? Do you think
there could be more?

In answering this question, you must use specific examples that you
got from running the compiler and generating an output.

 In stage 1, IfArith this stage deals with the surface level syntax of if statements and arithmetic operations. It parses if statements and basic arithmetic expressions. For example when we ran test0: (* (+ 2 3) 5), this stage handled the arithmetic. Then stage
 2, IfArithTiny desugars high-level if statements and arithmetic expressions into a simpler form, preparing them for further transformations. Turning our test case into simpler conditional and arithmetic operations. Next is stage 3, ANF which is a transformational
 stage that converts complex expressions into a form where every function call is passed simple arguments. Stage 4, IR-Virtual Purpose happens next. IR-Virtual is an intermediate representation that abstracts away platform-specific details, focusing on virtual
 registers and simple assembly-like instructions. When running test0 this turned our expression into assembly-like code that looked like: ((mov-lit r0 8) (mov-lit r1 4) (mov-lit r2 3) (mul r2 r0) (sub r2 r1) (print r2)). Next stage 5  x86 occurs. This stage
 translates the IR-Virtual representation into x86 assembly language, which is specific to the x86 architecture. Our test case after reaching this part looked like: 

section
 .data 

int_format db "%ld", 10,0 

global _main 

extern printf 

section
 text 

_start:
 call main 

mov
 rax, 60 

xor rdi, rdi 

syscall 

main:
 push rbp 

mov rbp, rsp 

sub rsp, 48 

mov esi, 8

mov [rbp-16], esi

mov esi, 4

mov [rbp-8], esi

mov esi, 3

mov [rbp-24], esi

mov edi, [rbp-16]

mov eax, [rbp-24]

mul eax, edi

mov [rbp-24], eax

mov edi, [rbp-8]

mov eax, [rbp-24]

sub eax, edi

mov [rbp-24], eax

mov esi, [rbp-24]

lea rdi, [rel int_format]

mov eax, 0

call printf

finish_up:
 add rsp, 48

leave

ret

Finally
 stage 6, NASM generates NASM assembly code files from the x86 representation, preparing the code for assembly and linking. Overall, each pass of the compiler serves a specific purpose in transforming the code from a high-level representation to platform-specific
 machine code. While some stages might seem conceptually similar, they often perform distinct transformations or optimizations. It's possible that certain optimizations or transformations could be combined or reorganized to improve the efficiency or clarity
 of the compiler. However, introducing additional passes should be carefully considered to avoid unnecessary complexity or performance overhead.


[ Question 4 ] 

This is a larger project, compared to our previous projects. This
project uses a large combination of idioms: tail recursion, folds,
etc.. Discuss a few programming idioms that you can identify in the
project that we discussed in class this semester. There is no specific
definition of what an idiom is: think carefully about whether you see
any pattern in this code that resonates with you from earlier in the
semester.

[ Question 5 ] 

In this question, you will play the role of bug finder. I would like
you to be creative, adversarial, and exploratory. Spend an hour or two
looking throughout the code and try to break it. Try to see if you can
identify a buggy program: a program that should work, but does
not. This could either be that the compiler crashes, or it could be
that it produces code which will not assemble. Last, even if the code
assembles and links, its behavior could be incorrect.

To answer this question, I want you to summarize your discussion,
experiences, and findings by adversarily breaking the compiler. If
there is something you think should work (but does not), feel free to
ask me.

Your team will receive a small bonus for being the first team to
report a unique bug (unique determined by me).

[ High Level Reflection ] 

In roughly 100-500 words, write a summary of your findings in working
on this project: what did you learn, what did you find interesting,
what did you find challenging? As you progress in your career, it will
be increasingly important to have technical conversations about the
nuts and bolts of code, try to use this experience as a way to think
about how you would approach doing group code critique. What would you
do differently next time, what did you learn?

When we were compiling and testing the compiler.rkt we encountered a very strange error. It was that apparently on the Mac that we were running on, Racket was not on the path. It was a strange issue due to the fact that all the other previous projects were in the same location and ran just fine. We were able to figure it out by re-downloading Racket and adjusting the Racket path after some help from the professor. We found it quite interesting how the ifarith-tiny can cut down an expression to just the numbers. We had issues figuring out what the code did but after some research, we found that it is quite clever how they used “uop” and “bop” operations to check if they were valid ifarith expressions. As a group, we found it hard to schedule times where we could work together so we split up the project and attempted it individually. Ultimately, we found a time to work together and found that working together and throwing ideas at each other was the best way to work on this project. We immediately called out every error that we made as soon as we saw it and it was very helpful because we did not have to find the issue later on when we had more problems that stemmed from that same issue. Also, when writing up the discussion it was way better to have each other discuss the questions as we did not solve the issues individually but rather together with the help of discussion and some argumentation. 

