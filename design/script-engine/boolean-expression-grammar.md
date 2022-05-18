---
description: Boolean Expression Grammar of Script Engine
---

# Boolean expression grammar

HyperDbg operates based on the following boolean expression grammar.

### Language Grammar

```
# OneOpFunc1 input is a number and returns a number.
.OneOpFunc1->poi db dd dw dq neg hi low not check_address strlen wcslen interlocked_increment interlocked_decrement reference physical_to_virtual virtual_to_physical

# TwoOpFunc1 inputs are two numbers and returns a number.
.TwoOpFunc1->ed eb eq interlocked_exchange interlocked_exchange_add

# ThreeOpFunc1 inputs are three numbers and returns a number.
.ThreeOpFunc1->interlocked_compare_exchange

.OperatorsOneOperand->inc dec reference dereference

S->BE

BE->B1

B1->B2 B1'
B1'->|| B2 B1' @OR
B1'->eps

B2->B3 B2'
B2'->&& B3 B2' @AND
B2'->eps

B3->B4 B3'
B3'->| B4 B3' @OR
B3'->eps

B4->B5 B4'
B4'->^ B5 B4' @XOR
B4'->eps

B5->B6 B5'
B5'->& B6 B5' @AND
B5'->eps

B6->CMP

CMP->EXP > CMP @GT
CMP->EXP < CMP @LT
CMP->EXP >= CMP @EGT
CMP->EXP <= CMP @ELT
CMP->EXP == CMP @EQUAL
CMP->EXP != CMP @NEQ
CMP->EXP

EXP->E3

E3->E4 E3'
E3'->>> E4 E3' @ASR
E3'-><< E4 E3' @ASL
E3'->eps

E4->E5 E4'
E4'->+ E5 E4' @ADD
E4'->- E5 E4' @SUB
E4'->eps

E5->E10 E5'
E5'->/ E10 E5' @DIV
E5'->% E10 E5' @MOD
E5'->* E10 E5' @MUL
E5'->eps

E10->E12
E10->- E12 @NEG
E10->+ E12
E10->~ E12 @NOT
E10->* E12 @POI
E10->& E12 @REFERENCE

E12->.OneOpFunc1 ( EXP ) @.OneOpFunc1
E12->.TwoOpFunc1 ( EXP , EXP ) @.TwoOpFunc2
E12->.ThreeOpFunc1 ( EXP , EXP , EXP ) @.ThreeOpFunc1

E12->( BE )

# Types must have '_' at the first
E12->_register @PUSH
E12->_global_id @PUSH
E12->_local_id @PUSH

# numbers 
E12->_hex @PUSH
E12->_decimal @PUSH
E12->_octal @PUSH
E12->_binary @PUSH
E12->_pseudo_register @PUSH
```
