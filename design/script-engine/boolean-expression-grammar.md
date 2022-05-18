---
description: Boolean Expression Grammar of Script Engine
---

# Boolean Expression Grammar

HyperDbg operates based on the following boolean expression grammar.

### Language Grammar

```text
# OneOpFunc1 input is a number and returns a number.
.OneOpFunc1->poi db dd dw dq neg hi low not

S->BE

BE->B1

B1->B2 B1'
B1'->&& B2 B1' @AND
B1'->eps

B2->B3 B2'
B2'->|| B3 B2' @OR
B2'->eps

B3->CMP
B3->EXP

CMP->EXP > EXP @GT
CMP->EXP < EXP @LT
CMP->EXP >= EXP @EGT
CMP->EXP <= EXP @ELT
CMP->EXP == EXP @EQ
CMP->EXP != EXP @NEQ

CMP->( CMP )

EXP->E1 E0'
E0'->| E1 E0' @OR
E0'->eps

E1->E2 E1'
E1'->^ E2 E1' @XOR 
E1'->eps

E2->E3 E2'
E2'->& E3 E2' @AND 
E2'->eps  

E3->E4 E3'
E3'->>> E4 E3' @ASR
E3'->eps

E4->E5 E4'
E4'-><< E5 E4' @ASL 
E4'->eps

E5->E6 E5'
E5'->+ E6 E5' @ADD
E5'->eps

E6->E7 E6'
E6'->- E7 E6' @SUB
E6'->eps

E7->E8 E7'
E7'->* E8 E7' @MUL 
E7'->eps

E8->E9 E8'
E8'->/ E9 E8' @DIV
E8'->eps 


E9->E10 E9' 
E9'->% E10 E9' @MOD
E9'->eps 

E10->E12

E12->.OneOpFunc1 ( EXP ) @.OneOpFunc1
E12->( EXP )


# Types must have '_' at the first 
E12->_register @PUSH
E12->_id @PUSH

# numbers 
E12->_hex @PUSH
E12->_decimal @PUSH
E12->_octal @PUSH
E12->_binary @PUSH

E12->_pseudo_register @PUSH

E12->- E12 @NEG
E12->+ E12
E12->~ E12 @NEG

E13->eps
```

