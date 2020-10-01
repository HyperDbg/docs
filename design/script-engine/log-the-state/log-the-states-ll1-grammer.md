---
description: LL1 Grammar of Log the State
---

# LL1 Grammar

### LL1 Grammar

```text
S -> C ; S'
S' -> C ; S'
S' ->  '\n'
S' ->  '\r\n'
C -> id = E0 ;

E0 -> E1 E0'
E0' -> | E1 E0'
E0' -> eps

E1 -> E2 E1'
E1' -> ^ E2 E1' 
E1' -> eps

E2 -> E3 E2'
E2' -> & E3 E2' 
E2' -> eps  

E3 -> E4 E3'
E3' -> >> E4 E3'
E3' -> eps

E4 -> E5 E4'
E4' -> << E5 E4' 
E4' -> eps

E5 -> E6 E5'
E5' -> + E6 E5'
E5' -> eps

E6 -> E7 E6'
E6' -> - E7 E6' 
E6' -> eps

E7 -> E8 E7'
E7' -> * E8 E7' 
E7' -> eps

E8 -> E9 E8'
E8' -> / E9 E8'
E8' -> eps 

E9 -> poi ( E0 ) 
E9 -> db ( E0 ) 
E9 -> dd ( E0 ) 
E9 -> dw ( E0 ) 
E9 -> dq ( E0 ) 
E9 -> str ( E0 ) 
E9 -> wstr ( E0 ) 
E9 -> sizeof ( E0 ) 
E9 -> not ( E0 ) 
E9 -> neg ( E0 ) 

E9 ->  @reg 
E9 ->  num  
E9 ->  func 
E9 ->  $meta
```

### The Standard representation of LL1 Grammar

```text
S:C ; S'
S':C ; S'
S': '\n'
S': '\r\n'
C:id = E0 ;

E0:E1 E0'
E0':| E1 @OR E0'
E0':eps

E1:E2 E1'
E1':^ E2 @XOR E1' 
E1':eps

E2:E3 E2'
E2':& E3 @AND E2' 
E2':eps  

E3:E4 E3'
E3':>> E4 @ASR E3'
E3':eps

E4:E5 E4'
E4':<< E5 @ASL E4' 
E4':eps

E5:E6 E5'
E5':+ E6 @ADD E5'
E5':eps

E6:E7 E6'
E6':- E7 @SUB E6' 
E6':eps

E7:E8 E7'
E7':* E8 @MUL E7' 
E7':eps

E8:E9 E8'
E8':/ E9 @DIV E8'
E8':eps 

E9:poi ( E0 @POI ) 
E9:db ( E0 @DB ) 
E9:dd ( E0 @DD ) 
E9:dw ( E0 @DW ) 
E9:dq ( E0 @DQ ) 
E9:str ( E0 @STR ) 
E9:wstr ( E0 @WSTR ) 
E9:sizeof ( E0 @SIZEOF ) 
E9:not ( E0 @NOT ) 
E9:neg ( E0 @NEGs ) 

E9:@PUSH @reg 
E9:@PUSH num  
E9:@PUSH func 
E9:@PUSH $meta
```

