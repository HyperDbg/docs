---
description: Language Grammar of Script Engine
---

# Language Grammar

HyperDbg operates based on the following grammar.

### Language Grammar

```
# ThreeOpFunc1 inputs are three numbers and returns a number.
.ThreeOpFunc1->interlocked_compare_exchange


# TwoOpFunc1 inputs are two numbers and returns a number.
.TwoOpFunc1->ed eb eq interlocked_exchange interlocked_exchange_add

# TwoOpFunc2 inputs are two numbers and returns no value 
.TwoOpFunc2->spinlock_lock_custom_wait 



# OneOpFunc1 input is a number and returns a number.
.OneOpFunc1->poi db dd dw dq neg hi low not check_address strlen wcslen interlocked_exchange_increment interlocked_exchange_decrement 

# OneOpFunc2 input is a number.
.OneOpFunc2->print formats disable_event enable_event test_statement spinlock_lock spinlock_unlock

.ZeroOpFunc1->pause

.VarArgFunc1->printf 

.OperatorsTwoOperand->or xor and asr asl add sub mul div mod gt lt egt elt equal neq 
.OperatorsOneOperand->inc dec 

.SemantiRules->start_of_if jmp jz jnz jmp_to_end_and_jzcompleted end_of_if start_of_while end_of_while vargstart mov start_of_do_while  start_of_do_while_commands end_of_do_while start_of_for for_inc_dec start_of_for_ommands end_of_if ignore_lvalue

.Registers->rax eax ax ah al rcx ecx cx ch cl rdx edx dx dh dl rbx ebx bx bh bl rsp esp sp spl rbp ebp bp bpl rsi esi si sil rdi edi di dil r8 r8d r8w r8h r8l r9 r9d r9w r9h r9l r10 r10d r10w r10h r10l r11 r11d r11w r11h r11l r12 r12d r12w r12h r12l r13 r13d r13w r13h r13l r14 r14d r14w r14h r14l r15 r15d r15w r15h r15l ds es fs gs cs ss rflags eflags flags rip eip ip idtr ldtr gdtr tr cr0 cr2 cr3 cr4 cr8 dr0 dr1 dr2 dr3 dr6 dr7

.PseudoRegisters->pid tid proc thread peb teb ip buffer context
 
S->STATEMENT S
S->eps

STATEMENT->IF_STATEMENT
STATEMENT->WHILE_STATEMENT
STATEMENT->DO_WHILE_STATEMENT
STATEMENT->FOR_STATEMENT
STATEMENT->ASSIGN_STATEMENT ;
STATEMENT->CALL_FUNC_STATEMENT ;
STATEMENT->break @BREAK ;
STATEMENT->continue @CONTINUE ;



ASSIGN_STATEMENT->L_VALUE = EXPRESSION @MOV NULL
CALL_FUNC_STATEMENT->.OneOpFunc2 ( EXPRESSION @.OneOpFunc2 )
CALL_FUNC_STATEMENT->.VarArgFunc1 ( STRING @VARGSTART VA @.VarArgFunc1 )
CALL_FUNC_STATEMENT->.ZeroOpFunc1 ( @.ZeroOpFunc1 )
CALL_FUNC_STATEMENT->.TwoOpFunc2 ( EXPRESSION , EXPRESSION @.TwoOpFunc2 ) 
CALL_FUNC_STATEMENT->@IGNORE_LVALUE .TwoOpFunc1 ( EXPRESSION , EXPRESSION @.TwoOpFunc1 ) 
VA->, EXPRESSION VA
VA->eps

IF_STATEMENT->if @START_OF_IF ( BOOLEAN_EXPRESSION ) @JZ { S } ELSIF_STATEMENT ELSE_STATEMENT @END_OF_IF END_OF_IF
ELSIF_STATEMENT->elsif @JMP_TO_END_AND_JZCOMPLETED ( BOOLEAN_EXPRESSION ) @JZ { S } ELSIF_STATEMENT
ELSIF_STATEMENT->@JMP_TO_END_AND_JZCOMPLETED ELSIF_STATEMENT'
ELSIF_STATEMENT'->eps
ELSE_STATEMENT->else { S } 
ELSE_STATEMENT->eps
END_OF_IF->eps

    

WHILE_STATEMENT->while @START_OF_WHILE ( BOOLEAN_EXPRESSION ) @START_OF_WHILE_COMMANDS { S @END_OF_WHILE } 
DO_WHILE_STATEMENT->do @START_OF_DO_WHILE { S } while ( BOOLEAN_EXPRESSION ) @END_OF_DO_WHILE ; 

FOR_STATEMENT->for ( SIMPLE_ASSIGNMENT ; @START_OF_FOR BOOLEAN_EXPRESSION ; @FOR_INC_DEC INC_DEC ) { @START_OF_FOR_COMMANDS S @END_OF_FOR }
SIMPLE_ASSIGNMENT->L_VALUE = EXPRESSION @MOV SIMPLE_ASSIGNMENT'
SIMPLE_ASSIGNMENT->eps 
SIMPLE_ASSIGNMENT'->eps


INC_DEC->L_VALUE INC_DEC'
INC_DEC'->++ @INC INC'
INC_DEC'->-- @DEC DEC'
INC'->eps 
DEC'->eps
INC_DEC'->eps

BOOLEAN_EXPRESSION->eps


EXPRESSION->E1 E0'
E0'->| E1 @OR E0' 
E0'->eps


E1->E2 E1'
E1'->^ E2 @XOR E1' 
E1'->eps

E2->E3 E2'
E2'->& E3 @AND E2' 
E2'->eps  

E3->E4 E3'
E3'->>> E4 @ASR E3'
E3'->eps

E4->E5 E4'
E4'-><< E5 @ASL E4' 
E4'->eps

E5->E6 E5'
E5'->+ E6 @ADD E5'
E5'->eps

E6->E7 E6'
E6'->- E7 @SUB E6' 
E6'->eps

E7->E8 E7'
E7'->* E8 @MUL E7' 
E7'->eps

E8->E9 E8'
E8'->/ E9 @DIV E8'
E8'->eps 


E9->E10 E9' 
E9'->% E10 @MOD E9'
E9'->eps 




E10->E12



E12->.OneOpFunc1 ( EXPRESSION @.OneOpFunc1 ) 
E12->.TwoOpFunc1 ( EXPRESSION , EXPRESSION @.TwoOpFunc1 ) 
E12->.ThreeOpFunc1 ( EXPRESSION , EXPRESSION , EXPRESSION @.ThreeOpFunc1 ) 


E12->( EXPRESSION )


# Types must have '_' at the first 
E12->@PUSH _register
E12->@PUSH _id


# numbers 
E12->@PUSH _hex
E12->@PUSH _decimal
E12->@PUSH _octal
E12->@PUSH _binary


E12->@PUSH _pseudo_register

E12->- E12 @NEG E13
E12->+ E12 E13
E12->~ E12 @NOT E13

E13->eps


STRING->@PUSH _string
L_VALUE->@PUSH _id 
L_VALUE->@PUSH _register
NULL->eps


```
