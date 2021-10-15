# count occurrences of an event

One of the use cases of HyperDbg is counting the occurrence of different events. However, incrementing and decrementing value from different cores are problematic as you can't add everything by using mathematical operators as the result might not be accurate.

The problem arises from the fact that if  
