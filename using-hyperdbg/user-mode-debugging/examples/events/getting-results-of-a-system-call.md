# Getting Results of a System-call

```clike
? .thread_id = 0;
? .thread_id_lock = 0;

!sysret script {

	if (.thread_id == $tid) {
		
		printf("[%llx] result of syscall: %llx\n", $tid, @rax);
		
		//
		// Reset the thread id holder
		//
		.thread_id = 0;
	}
}

!syscall script { 
	
	if (@rax == $arg1) {
		
		spinlock_lock(&.thread_id_lock);
		
		if (.thread_id == 0) {
			
			//
			// Save the thread id for the SYSRET event
			//
			.thread_id = $tid;
			
			//
			// Show the parameters
			//
			printf("[%llx] syscall num: %llx, arg1: %llx, arg2: %llx, arg3: %llx, arg4: %llx\n", $tid, @rax, @rcx, @rdx, @r8, @r9);
			
		}
		
		spinlock_unlock(&.thread_id_lock);
	}
}
```
