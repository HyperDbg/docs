# count occurrences of an event

One of the use cases of HyperDbg is counting the occurrence of different events. However, incrementing and decrementing value from different cores are problematic as you can't add everything by using mathematical operators as the result might not be accurate.

The problem arises from the fact that if you want to access a value to add or subtract the value, other cores might do the same operation simaltaneously and each of the cores are computing the new values at the same time so we might (and will) lose some of the operations as the current core's computation might be replaced by other core's results.

In order to prevent this problem, you can 
