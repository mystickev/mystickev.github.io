
## CPU ARCH AND ASSEMBLY REFRESHER

An assembly language comprises:

- constants
- Expressions
- literals
- Reserved Words
- Mnemonics
- Identifiers
- Directives
- Instructions
- comments

## Radix suffices:

1. -d: decimal
2. -h: hexadecimal
3. -q or o: octal
4. -b: binary

Examples of integer constants

- 26 Decimal
- 1Ah hexadecimal
- 1101b binary
- 42Q octal

# Directives

Commands for the assembler

They are case-insensitive

Examples

- ```
  .code
    
    main PROC
  
    mov     eax, 10000h ;Copies 10000h into EAX
    
    add      eax, 40000h ;Adds 40000h to EAX
  
    sub      eax, 20000h ; Subtracts 20000h from EAX
  
    call      DumpRegs ; Call the procedure DumpRegs
  
    exit                         ; Call Windows procedure Exit ; to halt the program
    
    main    ENDP ; marks the end of main
  
    end      main ; last line to be assembled
    ```

- ```
  .data
    
    val1    db     255 ; unsigned byte
  ```

- ```
  name        PROC
  ```  
    

db is the older directive for assigning 8-bit data to a storage area. i.e

.data

val1    db     255 ; unsigned byte

## **Addressing Modes in Assembly**

### **1. Register Addressing Mode:**

- **What it is**: Using the value directly from a register.
- **Simple Explanation**: Imagine your register as a small box that holds a number. When you use register addressing, you're telling the computer, "Hey, use the number in this box!"
- **Example**: If you have **`MOV AX, BX`**, it means "Copy the value from the **`BX`** box to the **`AX`** box."

### **2. Memory Addressing Mode:**

- **What it is**: Using a value stored somewhere in memory (RAM).
- **Simple Explanation**: Think of your computer memory as a massive shelf with many boxes. Some boxes have labels, some don't. With this mode, you're saying, "Go to this specific box on the shelf and use the number inside it."
- **Variants**:
    - **Direct Memory Addressing**: You give an exact location (like "Box #5").
    - **Indirect Memory Addressing**: You give a formula to find the location (like "Go 3 boxes after Box #5").
- **Example**: **`MOV AX, [1234h]`** means "Go to the memory location **`1234h`** and get the value for **`AX`**."

### **3. Immediate Addressing Mode:**

- **What it is**: Using a value directly in the instruction.
- **Simple Explanation**: Instead of pointing to a box or giving a formula, you directly say, "Use this number right here."
- **Example**: **`MOV AX, 5`** means "Put the number 5 into the **`AX`** box."

### **4. Implicit Addressing Mode:**

- **What it is**: The instruction already knows where to get the value, so you don't specify it.
- **Simple Explanation**: Think of a robot that always checks the same box without being told. You just say, "Do the action," and the robot knows which box to check.
- **Examples**:
    - **SCASB**: This instruction automatically uses the **`AL`** box (register) to compare with a value in memory at the location pointed by the **`EDI`** box.
    - **Stack operations**: Instructions like **`PUSH`** and **`POP`** automatically use the **`ESP`** box to decide where to place or take values.

## The X64 architecture General Purpose registers

### **1. RAX (EAX in x86):**

- **Purpose**: Originally designed for arithmetic operations.
- **In x64**: General-purpose, but frequently used to store the return value of a function.
- **Special Note**: Can still use **`EAX`** to refer to the lower 32-bits.

### **2. RCX (ECX in x86):**

- **Purpose**: Historically used as a loop counter (hence the "C" for counter).
- **In x64**: General-purpose, but still often used in loops and for certain string operations.
- **Special Note**: Can still use **`ECX`** to refer to the lower 32-bits.

### **3. RBP (EBP in x86):**

- **Purpose**: Known as the "Base Pointer". In x86, it was frequently used to point to the base of the current stack frame.
- **In x64**: Its traditional role has been somewhat reduced due to optimizations, but it can still be used to reference local variables and function arguments, especially in contexts where frame pointers are necessary or helpful.
- **Special Note**: Can still use **`EBP`** to refer to the lower 32-bits.

### **4. RSP (ESP in x86):**

- **Purpose**: The "Stack Pointer". Always points to the top of the current stack.
- **In x64**: Same purpose. It's critical for function call management, local variable storage, and more.
- **Special Note**: Can still use **`ESP`** to refer to the lower 32-bits.

### **5. RSI/RSI (ESI/EDI in x86):**

- **Purpose**: Historically used for string and memory operations. **`RSI`** often pointed to the source, and **`RDI`** pointed to the destination.
- **In x64**: Still frequently used in string and memory operations, but also serve as general-purpose registers.
- **Special Note**: Can still use **`ESI`** and **`EDI`** to refer to the lower 32-bits of **`RSI`** and **`RDI`** respectively.

## Segment Registers

Think of these as signposts in your computer memory, helping to section off and organize different types of data or code.

- **CS (Code Segment)**: This is where your actual code lives.
- **DS (Data Segment)**: The default storage area for most data operations.
- **ES (Extra Segment)**: An additional segment to aid with data operations. It’s like a backup shelf when you run out of space on the main shelf.
- **FS & GS (More Extra Segments)**: More of these 'backup shelves'. Introduced for more specialized tasks and more storage options.
- **SS (Stack Segment)**: This is where the stack lives, a special place in memory where data is stored temporarily during program execution.

## Pointer and Index Registers

These are like pointers or arrows indicating specific spots in the computer’s memory or giving directions for specific tasks.

- **RIP (Instruction Pointer)**: Always points to the next instruction to be executed. It's like a reading pointer, showing where you are in the code.
- **RSI & RDI**: Think of these as specialized tools for string operations, like a pair of hands transferring letters between two words.

## Floating Point Unit (FPU) Registers

These are specialized registers for decimal numbers.

- **ST0 to ST7**: Think of a stack of trays, where you place or pick up decimal numbers. Useful when doing calculations with decimals.

## MMX Registers

Specialized registers for multimedia tasks like image or video processing.

- **MM0 to MM7**: Think of these as workstations where your computer processes multimedia tasks.

## XMM Registers

Used for SIMD (Single Instruction, Multiple Data) operations which let your computer do many similar tasks at once, like applying a filter to all pixels in an image simultaneously.

- **XMM0 to XMM15**: These are lanes on a highway. The more lanes, the more data you can process at once.

## Control Registers

The control center of the CPU.

- **CR0 to CR4, CR8**: These are like switches and dials on a control panel, influencing how the CPU operates.

## Debug Registers

Helpful tools when you want to monitor or troubleshoot code.

- **DR0 to DR7**: Think of these as watchtowers. Set them up, and they'll notify you when specific spots in your code are accessed, useful for catching bugs or issues.
  
## POINTS

A single operand to a PUSH instruction will often be a pointer.

A pointer is a value of an address(an address points to a value stored in that address/memory location). i.e

PUSH offset loc_46780;char softwareKey - points to a reg key stored in this address..double click on the address in ida to access string. 

Let's look at a simple assembly instruction: **`mov   eax, [0x410230]`**.

- The square brackets (**`[]`**) here are like telling someone, "Go fetch the book from this shelf." It's a way of saying we want the *data* at that address, not the address itself. In tech terms, we call this **dereferencing**.
- When we see an instruction like the one above, with a specific address inside the brackets, it's called **direct addressing**. Why? Because we're directly specifying the address we want to access.

In this example, the result is that the 4 bytes of data located at **`0x410230`** will be moved to the **`EAX`** register.

## Indirect Memory Addressing?

Picture a sprawling city with many streets and addresses. Now, some addresses are straightforward – "123 Main St." But others might be a bit more complex like, "The third house after the big oak tree on Elm St." This latter way of describing addresses is how you can think of **indirect memory addressing**.

In assembly, instead of always accessing a specific memory address directly, we can use combinations of registers and values to dynamically compute the address. It's a powerful method but can also be a bit tricky to decipher. Let’s break down some examples:

## Indirect Addressing Examples

1. **[EAX]**: This is like saying, "Access the house (or memory) that the EAX register points to." In our city analogy, EAX holds the address of a particular house, and we want to go there.
2. **[EBP + 0x10]**: This one's a bit more nuanced. If EBP is a starting point, we're moving a bit further to access data. In our city, it's like saying, "Go to the address in EBP and then move 16 houses down."
3. **[EAX + EBX * 8]**: Think of this as accessing an apartment complex. EAX points to the start of the complex, and each building (or memory structure) is 8 units apart. EBX tells us which building to access. So if EBX is 2, we'd skip the first two buildings and head to the third one.
4. **[EAX + EBX + 0xC]**: This is like navigating a condo with different wings and floors. EAX might point to the start of a wing, EBX might indicate which floor to go to, and 0xC is a specific condo on that floor.

## Jump Instructions: BASICS

Imagine you're reading a book, and suddenly, a note tells you to skip ahead to another chapter or perhaps go back to a previous page. This sudden shift is precisely how "jump instructions" work in assembly.

A jump instruction gives a directive to the CPU to cease its current operation and start executing code from an entirely new location in memory. It's like a crossroad that determines the flow of our program. In simpler terms, think of it as a detour sign on a road trip.

## Unconditional Jumps

As the name suggests, **unconditional jumps** are like those detour signs that you must follow, no matter what. They always redirect the execution to a new memory location, with no strings attached.

**Examples**:

1. **JMP**: This is the basic jump instruction. It tells the CPU, "Hey, forget what you're doing right now, and start from this new address."
2. **CALL**: A bit more sophisticated, this not only jumps to a new location but also remembers where it came from. This is useful for procedures or functions, ensuring we can return once the function is done.
3. **RET**: Speaking of returning, RET does just that. After a CALL, the RET instruction ensures we go back to where we were before the jump.

## Conditional Jumps in Assembly

Conditional jumps in assembly direct the CPU to shift its execution to another part of the program based on specific conditions. These jumps rely on the state of the flags register, which gets updated after arithmetic or Boolean operations.

**Types of Conditional Jumps**:

1. **CMP and TEST Instructions**:
    - **CMP**: This is like a subtraction (**`SUB`**) but doesn't change the destination value. Instead, it updates the flags as if a subtraction took place.
    - **TEST**: This is similar to an **`AND`** operation but doesn't alter the destination. It's essentially a shadow Boolean AND.
    - After a **`CMP`** or **`TEST`**, it's typical to see a conditional jump.
2. **Jump Conditions (Jcc)**:
    - **A**: Above (unsigned)
    - **B**: Below (unsigned)
    - **E/Z**: Equal/Zero
    - **G**: Greater than (signed)
    - **L**: Less than (signed)
    - **N**: Not condition, e.g., **`JNZ`** means jump if not zero.
3. **How are conditions evaluated?**
    - The evaluation relies on bits in the flags register.
    - For instance, **`JB`** (jump if below) is true if the Carry flag is set.
    - **`JG`** (jump if greater) is true if the Zero flag is unset, and the Sign flag equals the Overflow flag.

## OPERATIONS/INSTRUCTIONS

To better explain this, think of the value 0x20 being stored at the address 0x00830048: ebx points to the address 0x00830040. so the value 0x20 will be at ebx + 8.

- lea eax [ebx + 8] - this will move the **address 0x00830048** to eax.
- mov eax [ebx + 8] - this will move the contents of the address **ebx + 8, 0x20,** to eax.

To increment and decrement:

- inc     edx ; will increment edx by 1
- dec     ecx ; will decrement ecx by 1

Multiplication and division operations often operate on a predefined register. The command is therefore the instruction, plus the value that the predefined register will be mul or div with.

- mul     value
- div     value

The **mul** operation will multiply the predefined register(eax) with the value defined.

The return value of the operation will be stored across two registers, EDX and EAX with EDX storing most significant bits and EAX storing least significant bits.

**Div** will do the same operation as mul but will divide the 64-bits across EDX and EAX by value. The result will be stored in EAX and the remainder in EDX.
# REVERSING FUNCTIONS

## CALLING A FUNCTION

1. function format: return = function(arg0,arg1)
2. specific events occur when calling a function:
3. Pass in parameters(stack / register)
4. save return pointer
5. Transfer control to the function.

Specific events occur when returning from a function:

1. Set up a return value(typically EAX)
2. clean up stack and store registers
3. Transfer control to the saved return pointer

### F**unction** P**rologue actions**

```jsx
push ebp; save ebp

mov ebp, esp; create function stack

sub esp, 104h ; create space for variables/arguments

push edi; save registers to be used within the function.

push esi;
```

### **Function** E**pilogue actions**

```jsx
pop all pushed registers
retn = pop eip;
leave = mov esp, ebp; pop ebp;
add esp, 4 ; restore esp state.
```

## STACK

The stack is Last In, First Out (LIFO).

• PUSH adds an element, and POP removes one.

• ESP points to the next item on the stack and changes with
instructions like PUSH, POP, CALL, LEAVE, and RET.

• EBP (a.k.a. “frame pointer”) is an unchanging reference.

• EBP – value = local variable (registers may also be used)

![image_info](/assets/images/favicon/stack-image.png)

### Typical function Example

![image](/assets/images/favicon/typical-example.png)


### Corresponding Stack for the function above

![image](/assets/images/favicon/stack-image-for-function.png)


### ACTIONS FOR CLEAN-UP

`pop edx implies esp + 4`

`RET`

`retn = pop eip;
leave = mov esp,  ebp +  pop     ebp;
add esp,  value ; restore esp state.`

## FUNCTIONS CALLING CONVENTIONS

### cdecl convention (most common)

- Arguments placed on the stack from right to left
- The return value is placed in EAX
- The caller cleans up the stack(it removes the arguments)

EXAMPLE

```jsx
push        edx ; SubStr
push        eax ; str
call        strstr 
add         esp,     8;   clean up
```

### Stdcall convention

- Similar to cdecl but the callee cleans up the stack
- This is the convention used in WIN32 APIs

EXAMPLE

```jsx
push      offset LibFileName ; Kernel32.dll

call      ds:LoadLibraryA

mov       [ebp+hModule], eax; no stack clean up after the call.
```

### Fastcall convention

- Arguments are stored on the stack
- Any extra arguments placed on the stack
- The callee cleans up arguments on the stack

EXAMPLE

```jsx
mov        edx, offset  aCmdExe ; cmd.exe

lea        ecx,   [ebp+8]

call       sub_409008 ;    note the use of ecx and edx registers
```

### Thiscall convention

- Used in C++ code(member functions)
- this convention includes a reference to the ***“this”*** pointer.
- In Microsoft compilers, ECX holds the ***“this”***  pointer and the callee is responsible for cleaning up the stack
- in GNU compilers, the “this” pointer is pushed onto the stack last and the caller cleans up after.

EXAMPLE

```jsx
mov       ecx,     eax   ; ECX holds the address of self. 

call         sub_10001067
```
