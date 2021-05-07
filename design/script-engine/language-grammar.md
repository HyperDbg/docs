---
description: Language Grammar of Script Engine
---

# Language Grammar

HyperDbg operates based on the following grammar.

### Language Grammar

```text
# OneOpFunc1 input is a number and returns a number.
.OneOpFunc1->poi db dd dw dq neg hi low not

# OneOpFunc2 input is a number.
.OneOpFunc2->print formats disableevent enableevent

.ZeroOpFunc1->break

.VarArgFunc1->printf 

.OperatorsTwoOperand->or xor and asr asl add sub mul div mod gt lt egt elt eq neq 
.OperatorsOneOperand->inc dec 

.SemantiRules->start_of_if jmp jz jnz jmp_to_end_and_jzcompleted end_of_if start_of_while end_of_while vargstart mov start_of_do_while end_of_do_while start_of_for for_inc_dec start_of_for_ommands end_of_if

.Registers->rax rcx rdx rbx rsp rbp rsi rdi r8 r9 r10 r11 r12 r13 r14 r15 ds es fs gs cs ss rflags rip idtr gdtr cr0 cr2 cr3 cr4 cr8

.PseudoRegisters->pid tid proc thread peb teb ip buffer context
 
S->STATEMENT S
S->eps

STATEMENT->IF_STATEMENT
STATEMENT->WHILE_STATEMENT
STATEMENT->DO_WHILE_STATEMENT
STATEMENT->FOR_STATEMENT
STATEMENT->ASSIGN_STATEMENT ;
STATEMENT->CALL_FUNC_STATEMENT ;

ASSIGN_STATEMENT->L_VALUE = EXPRESSION @MOV NULL
CALL_FUNC_STATEMENT->.OneOpFunc2 ( EXPRESSION @.OneOpFunc2 )
CALL_FUNC_STATEMENT->.VarArgFunc1 ( STRING @VARGSTART VA @.VarArgFunc1 )
CALL_FUNC_STATEMENT->.ZeroOpFunc1 ( @.ZeroOpFunc1 )
VA->, EXPRESSION VA
VA->eps

IF_STATEMENT->if @START_OF_IF ( BOOLEAN_EXPRESSION ) @JZ { S } ELSIF_STATEMENT ELSE_STATEMENT @END_OF_IF END_OF_IF
ELSIF_STATEMENT->elsif @JMP_TO_END_AND_JZCOMPLETED ( BOOLEAN_EXPRESSION ) @JZ { S } @JMP_TO_END_AND_JZCOMPLETED ELSIF_STATEMENT
ELSIF_STATEMENT->@JMP_TO_END_AND_JZCOMPLETED eps
ELSE_STATEMENT->else { S } 
ELSE_STATEMENT->eps
END_OF_IF->eps

WHILE_STATEMENT->while @START_OF_WHILE ( BOOLEAN_EXPRESSION ) @JZ { S @END_OF_WHILE } 
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

