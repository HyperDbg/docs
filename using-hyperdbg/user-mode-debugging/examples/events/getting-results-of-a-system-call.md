---
description: Intercepting a SYSCALL and SYSRET result
---

# Getting Results of a System-call

One of the exciting features of HyperDbg is its ability to trace and trigger events in the case of both SYSCALLs and SYSRETs. It's a straightforward task to intercept, monitor, or change syscall parameters; however, it's a bit challenging when it comes to SYSRETs.

In this example, we're going to create an automated script to intercept both system calls and then intercept the result of the system call when it returns from the kernel to the user mode.

Based on our Windows internals knowledge, we know that a thread might be either in user-mode or in the kernel mode, so if we intercept a thread turns to the kernel, by executing the SYSCALL instruction, we can expect that the target thread will perform an SYSRET after finishing its execution in the kernel.

It's clear that if we save the Thread Id of our target thread from the syscall event, we can check it later in the SYSRET event.

There is another problem here. Two threads might simultaneously trigger the same syscall; thus, we need to synchronize somehow. To overcome this issue, we use spinlocks.

First, we create two global variables, one to hold the spinlocks' lock, and the second global variable stores the Thread Id that executed our target system call.

```clike
? .thread_id = 0;
? .thread_id_lock = 0;
```

Next, in the SYSRET event, we check whether the current thread is stored at the `.thread_id` global variable or not. If it is stored, then it's the thread that we want to see its results, and if not, there is no interest for interception. At last, we set the `.thread_id` to `0` to indicate that we finish investigating this thread.

```clike
!sysret script {

	if (.thread_id == $tid) {
		
		printf("[%llx] result of syscall: %llx\n", $tid, @rax);
		
		//
		// Reset the thread id holder
		//
		.thread_id = 0;
	}
}
```

As the next step, we check for the **Process Id** and the **syscall number**. If the process is our target process and the system call number stored at the **RAX** register is our target system call, then we keep the Thread Id into the `.thread_id` variable.&#x20;

There might be two or more threads that execute the system call. We only intercept one of them and ignore another one if both of them execute the same syscall at once. The check is performed in the critical section between spinlocks to avoid two threads serving the same check simultaneously.

```clike
!syscall script { 
	
	if ($pid == $arg1 && @rax == $arg2) {
		
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

As you might have noticed, we run the SYSRET event before running the syscall event. The reason for it came from the fact that if we set the SYSCALL event before the SYSRET event, a syscall might set the `.thread_id` to a Thread Id. As we're not configuring the SYSRET event yet, the thread might finish its execution (run SYSRET) before configuring the SYSCALL event, and thus, it never works properly.

All in all, putting everything together makes the following script, we'll create a file named `c:\users\sina\desktop\script.hds`.

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
	
	if ($pid == $arg1 && @rax == $arg2) {
		
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

After that, we run our script by using the below command.

```
? .script c:\users\sina\desktop\script.hds 1240 55
```
