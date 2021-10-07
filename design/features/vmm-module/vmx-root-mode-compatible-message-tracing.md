---
description: >-
  How we transfer the buffers safely from the kernel and vmx-root to the
  debugger
---

# VMX root-mode Compatible Message Tracing

If you need to change and customize message tracing options, please visit [here](https://docs.hyperdbg.org/tips-and-tricks/misc/customize-build).

The message tracing is derived from our [Hypervisor From Scratch 8](https://rayanfam.com/topics/hypervisor-from-scratch-part-8/) :

Without any doubt, one of the hardest parts of designing a hypervisor is sending a message from Vmx root-mode to Vmx non-root mode. This is because you have lots of limitations like you can’t access non-paged buffer, and of course, most of the NT functions are not \(ANY IRQL\) compatible as they might access the buffers that reside in paged pool.

The things are ending here, there are plenty of other limitation to deal with.

This section is inspired by Chapter 6: Kernel Mechanisms \(High IRQL Synchronization\) from the Windows Kernel Programming book by Pavel Yosifovich which is a really amazing book if you want to start with kernel programming.

### **Concepts**

This section describes some of the Operating System concepts, you should know before starting.

### **What’s a spinlock?**

The Spin Lock is a bit in memory that provides atomic test and modify operations. When a CPU tries to acquire a spinlock, and it’s not currently free, the CPU keeps spinning on the spinlock, busy waiting for it to be released by another CPU means that it keeps checking until another thread which acquired it first release it.

### **Test-and-Set**

You probably read about Test and Set in university. Still, in case you didn’t, in computer science, the test-and-set instruction is an instruction used to write 1 \(set\) to a memory location and return its old value as a single atomic \(i.e., non-interruptible\) operation. If multiple processes may access the same memory location, and if a process is currently performing a test-and-set, no other process may begin another test-and-set until the first process’s test-and-set is finished.

### **What do we mean by “Safe”?**

The “safe” is used a lot in hypervisors. By “safe,” we mean something that works all the time and won’t cause system crash or system halt. It’s because it’s so tricky to manage codes in vmx root-mode. After all, interrupts are masked \(disabled\), or transfer buffer from vmx root-mode to vmx non-root mode needs extra effort, and we should be cautious and avoid executing some APIs to be safe.

### **What is DPC?**

A **Deferred Procedure Call** \(**DPC**\) is a Windows mechanism that allows high-priority tasks \(e.g., an interrupt handler\) to defer required but lower-priority tasks for later execution. This permits device drivers and other low-level event consumers to perform the high-priority part of their processing quickly and schedule non-critical additional processing for execution at a lower priority.

DPCs are implemented by DPC objects which are created and initialized by the kernel when a device driver or some other kernel-mode program issues requests for DPC. The DPC request is then added to the end of a DPC queue. Each processor has a separate DPC queue. DPCs have three priority levels: low, medium, and high. By default, all DPCs are set to medium priority. When Windows drops to an IRQL of Dispatch/DPC level, it checks the DPC queue for any pending DPCs and executes them until the queue is empty or some other interrupt with a higher IRQL occurs.

This is the description of DPCs from MSDN:

_Because ISRs must execute as quickly as possible, drivers must usually postpone the completion of servicing an interrupt until after the ISR returns. Therefore, the system provides support for deferred procedure calls \(DPCs\), which can be queued from ISRs and which are executed at a later time and at a lower IRQL than the ISR._

There are two posts about DPCs [here ](https://repnz.github.io/posts/practical-reverse-engineering/reversing-dpc-keinsertqueuedpc/)and [here](https://repnz.github.io/posts/practical-reverse-engineering/dumping-dpc-queues/), you can read them for more information.

### **Challenges**

For example, Vmx-root mode is not a HIGH\_IRQL interrupt \(with discussing it in **Discussion** Section\), but as it disables all of the interrupts, we can think like it’s a HIGH\_IRQL state. The problem is that must of synchronization functions are designed to be worked on IRQL less than DISPATCH\_LEVEL.

Why is it problematic? Imagine you have a one-core processor, and your function requires a spinlock \(let say it’s merely a buffer that needs to be accessed\). The function raises the IRQL to **DISPATCH\_LEVEL**. Now the Windows Scheduler can’t interrupt the function until it releases the spinlock and lowers the IRQL to **PASSIVE\_LEVEL** or **APC\_LEVEL**. During the execution of the function, a vm-exit occurs; thus, we’re in vmx root-mode now. It’s because, as I told you, vm-exit happens as if it’s a HIGH\_IRQL interrupt.

Now, what if we want to access that buffer in vmx root mode? Two scenarios might occur.

* We wait on a spinlock that was previously acquired by a thread in vmx non-root mode and this we have to wait forever. A deadlock occurs.
* We enter the function without looking at the lock \(while there is another thread that enters the function at the same time.\) so it results in a corrupted buffer and invalid data.

The other limitation is in Windows design putting the thread into a waiting state cannot be done at IRQL **DISPATCH\_LEVEL** or higher. It’s because in Windows when you acquire a spinlock it raises the IRQL to 2 – **DISPATCH\_LEVEL** \(if not already there\), acquire the spinlock, perform the work and finally release the spinlock and lower IRQL back.

If you look at a function like **KeAcquireSpinLock** and **KeReleaseSpinLock**, they get an IRQL in their arguments. First, **KeAcquireSpinLock** saves current IRQL to the parameter supplied by the user then raises the IRQL to **DISPATCH\_LEVEL** and sets a bit. When the function finished its works with shared data, then it calls **KeReleaseSpinLock** and passes that old IRQL parameter so this function unsets the bit and restore the old IRQL \(lowers the IRQL\).

Windows has 4 kinds of Spinlocks,

1. KeAcquireSpinLock – KeReleaseSpinLock : This pair can be called at IRQL &lt;= DISPATCH\_LEVEL.
2. KeAcquireSpinLockAtDpcLevel – KeReleaseSpinLockFromDpcLevel : This pair can be call at IRQL = DISPATCH\_LEVEL only, it’s more optimized if you are already in IRQL 2 as it doesn’t saves the old IRQL and it’s specially designed to work on DPC routine.
3. KeAcquireInterruptSpinLock – KeReleaseInterruptSpinLock: Hardware based use this pair e.g in Interrupt Service Routine \(ISR\) or it used by drivers with an interrupt source.
4. ExInterlockedXxx : This function raises the IRQL to HIGH\_LEVEL and perform it’s task, it doesn’t need a release function as no one interrupt us on HIGH\_IRQL.

But unfortunately, things are more complicated when it comes to vmx root-mode. We don’t have IRQL in the vmx root-mode. It’s an operating system thing, so we can’t use any of the above functions, and things are getting worst if we want to use our message tracing mechanism between multiple cores!

For these reasons, we have to design our custom spinlock.

### **Designing A Spinlock**

Designing spinlock in a multi-core system by its nature needs the hardware support for atomic operation means that hardware \(most of the time processor\) should guarantee that an operation is performed just by logical \(hyper-threaded\) core and it’s non-interruptible.

There is an article [here](https://locklessinc.com/articles/locks/) that describes different kinds of spinlock with different optimizations, also it’s implemented [here](https://github.com/cyfdecyf/spinlock).

The design of this mechanism in the processor is beyond the scope of this article. We simply use an intrinsic function provided by Windows called “[**\_interlockedbittestandset**](https://docs.microsoft.com/en-us/cpp/intrinsics/interlockedbittestandset-intrinsic-functions?view=vs-2019)“.

This makes our implementation super simple. We just need to use the following function, and it’s the responsibility of the processor to take care of everything.

We should use volatile keyword in parameters too, otherwise, it’s like un-volatiling.

```c
inline BOOLEAN SpinlockTryLock(volatile LONG* Lock)
{
	return (!(*Lock) && !_interlockedbittestandset(Lock, 0));
}
```

Now we need to spin! If the above function was not successful, then we have to keep CPU checking to see when another processor releases the lock.

```c
void SpinlockLock(volatile LONG* Lock)
{
	unsigned wait = 1;

	while (!SpinlockTryLock(Lock))
	{
		for (unsigned i = 0; i < wait; ++i)
		{
			_mm_pause();
		}

		// Don't call "pause" too many times. If the wait becomes too big,
		// clamp it to the max_wait.

		if (wait * 2 > max_wait)
		{
			wait = max_wait;
		}
		else
		{
			wait = wait * 2;
		}
	}
}
```

If you wonder what is the **\_mm\_pause\(\)** then it’s equal to **PAUSE** instruction in x86.

Pause instruction is commonly used in the loop of testing spinlock, when some other thread owns the spinlock, to mitigate the tight loop.

[PAUSE](http://felixcloutier.com/x86/PAUSE.html) notifies the CPU that this is a spinlock wait loop, so memory and cache accesses may be optimized. See also [pause instruction in x86](https://stackoverflow.com/questions/12894078/pause-instruction-in-x86) for some more details about avoiding the memory-order mis-speculation when leaving the spin-loop. PAUSE may stop CPU for some time to save power. Older CPUs decode it as REP NOP, so you don’t have to check if it’s supported. Older CPUs will simply do nothing \(NOP\) as fast as possible.

For releasing the lock, there is nothing special to do, so simply unset it without caring for any other processor as there is no other processor that wants to unset it.

```c
void SpinlockUnlock(volatile LONG* Lock)
{
 *Lock = 0;
}
```

The “**volatile**” keyword tells the compiler that the value of the variable may change at any time without any action being taken by the code the compiler finds nearby. The implications of this are quite serious. There are lots of examples here if you have a problem with understanding “**volatile**“.

### **Message Tracer Design**

For solving the above the challenge about deadlock, I create two message pools for saving messages. The first pool is designed to be used as storage for vmx non-root messages \(buffers\) and the second pool is used for vmx-root messages.

We have the following structure that describes the state of each of these two pools.

```c
typedef struct _LOG_BUFFER_INFORMATION {

	UINT64 BufferStartAddress;						// Start address of the buffer
	UINT64 BufferEndAddress;						// End address of the buffer

	UINT64 BufferForMultipleNonImmediateMessage;	// Start address of the buffer for accumulating non-immadiate messages
	UINT32 CurrentLengthOfNonImmBuffer;				// the current size of the buffer for accumulating non-immadiate messages


	KSPIN_LOCK BufferLock;							// SpinLock to protect access to the queue
	KSPIN_LOCK BufferLockForNonImmMessage;			// SpinLock to protect access to the queue of non-imm messages

	UINT32 CurrentIndexToSend;						// Current buffer index to send to user-mode
	UINT32 CurrentIndexToWrite;						// Current buffer index to write new messages

} LOG_BUFFER_INFORMATION, * PLOG_BUFFER_INFORMATION;
```

Generally, we’ll save the buffer as illustrated below, each chunk of the message came with **BUFFER\_HEADER** that describes that chunk.

Other information for the buffer like **Current Index to Write** and **Current to Send** is saved in the above structure.

```c
A core buffer is like this , it's divided into MaximumPacketsCapacity chucks,
each chunk has PacketChunkSize + sizeof(BUFFER_HEADER) size

			 __________________________
			|      BUFFER_HEADER      |
			|_________________________|
			|                         |
			|           BODY          |
			|         (Buffer)        |
			| size = PacketChunkSize  |
			|                         |
			|_________________________|
			|      BUFFER_HEADER      |
			|_________________________|
			|                         |
			|           BODY          |
			|         (Buffer)        |
			| size = PacketChunkSize  |
			|                         |
			|_________________________|
			|                         |
			|                         |
			|                         |
			|                         |
			|           .             |
			|           .             |
			|           .             |
			|                         |
			|                         |
			|                         |
			|                         |
			|_________________________|
			|      BUFFER_HEADER      |
			|_________________________|
			|                         |
			|           BODY          |
			|         (Buffer)        |
			| size = PacketChunkSize  |
			|                         |
			|_________________________|

```

The BUFFER\_HEADER is defined like this,

```c
// Message buffer structure
typedef struct _BUFFER_HEADER {
	UINT32 OpeationNumber;	// Operation ID to user-mode
	UINT32 BufferLength;	// The actual length
	BOOLEAN Valid;			// Determine whether the buffer was valid to send or not
} BUFFER_HEADER, * PBUFFER_HEADER;
```

We save the length of used length of the chunk and a bit which determine whether we sent it before or not.

Operation Number is number, which will be sent to the user-mode to show the type of the buffer that came from the kernel. In other words, it’s a number that indicates the intention \(and structure\) of the buffer, so the user-mode application will know what to do with this buffer.

The following Operation Numbers are currently defined :

```c
// Message buffer structure
typedef struct _BUFFER_HEADER {
	UINT32 OpeationNumber;	// Operation ID to user-mode
	UINT32 BufferLength;	// The actual length
	BOOLEAN Valid;			// Determine whether the buffer was valid to send or not
} BUFFER_HEADER, * PBUFFER_HEADER;
```

Each of them shows a different type of message, and the last one shows that a bunch buffer is accumulated in this buffer. This message tracing is designed to send any kind of the buffer from both vmx root and OS to the user-mode, so it’s not limited just to sending messages, we can send buffers with custom structures and different Operation Numbers.

The last thing about our message tracing is, it can be configured with the following constants, you can change them in order to have a better performance for your exclusive use.

```c
// Default buffer size
#define MaximumPacketsCapacity 1000 // number of packets
#define PacketChunkSize		1000 // NOTE : REMEMBER TO CHANGE IT IN USER-MODE APP TOO
#define UsermodeBufferSize  sizeof(UINT32) + PacketChunkSize + 1 /* Becausee of Opeation code at the start of the buffer + 1 for null-termminating */
#define LogBufferSize MaximumPacketsCapacity * (PacketChunkSize + sizeof(BUFFER_HEADER))
```

You can configure things like the maximum number of chunks in a buffer and also the size of each chunk. Setting the above variables is necessary in some cases if there is no thread to consume \(read\) these chunks and pools are full; it replaces the previous unread buffer. Hence, if you can’t frequently consume the pools, then it’s better to specify a higher number for **MaximumPacketsCapacity** so that you won’t lose anything.

### **Initialization Phase**

In the initialization phase, we allocate space for the above structure \(2 times, one for vmx non-root and one for vmx-root\) and then allocate the buffers to be used as the storage for saving our messages.

We have to zero them all and also **KeInitializeSpinLock** to initialize the spinlock. We use this spinlock only for vmx non-root, and this function makes sure that the value for the lock is unset. We do the same for our custom spinlock \(**VmxRootLoggingLock**\), just unset it.

You might ask, what is the “**BufferLockForNonImmMessage**“, it’s another lock that will use it as optimization \(see later\).

All in all, we have the following code.

```c
/* Initialize the buffer relating to log message tracing */
BOOLEAN LogInitialize() {


	// Initialize buffers for trace message and data messages (wee have two buffers one for vmx root and one for vmx non-root)
	MessageBufferInformation = ExAllocatePoolWithTag(NonPagedPool, sizeof(LOG_BUFFER_INFORMATION) * 2, POOLTAG);

	if (!MessageBufferInformation)
	{
		return FALSE; //STATUS_INSUFFICIENT_RESOURCES
	}

	// Zeroing the memory
	RtlZeroMemory(MessageBufferInformation, sizeof(LOG_BUFFER_INFORMATION) * 2);

	// Initialize the lock for Vmx-root mode (HIGH_IRQL Spinlock)
	VmxRootLoggingLock = 0;

	// Allocate buffer for messages and initialize the core buffer information 
	for (int i = 0; i < 2; i++)
	{

		// initialize the lock
		// Actually, only the 0th buffer use this spinlock but let initialize it for both but the second buffer spinlock is useless 
		// as we use our custom spinlock.
		KeInitializeSpinLock(&MessageBufferInformation[i].BufferLock);
		KeInitializeSpinLock(&MessageBufferInformation[i].BufferLockForNonImmMessage);

		// allocate the buffer
		MessageBufferInformation[i].BufferStartAddress = ExAllocatePoolWithTag(NonPagedPool, LogBufferSize, POOLTAG);
		MessageBufferInformation[i].BufferForMultipleNonImmediateMessage = ExAllocatePoolWithTag(NonPagedPool, PacketChunkSize, POOLTAG);

		if (!MessageBufferInformation[i].BufferStartAddress)
		{
			return FALSE; // STATUS_INSUFFICIENT_RESOURCES
		}

		// Zeroing the buffer
		RtlZeroMemory(MessageBufferInformation[i].BufferStartAddress, LogBufferSize);

		// Set the end address
		MessageBufferInformation[i].BufferEndAddress = (UINT64)MessageBufferInformation[i].BufferStartAddress + LogBufferSize;
	}
}

```

### **Sending Phase \(Saving Buffer and adding them to pools\)**

In a regular Windows routine generally, we shouldn’t be on IRQL more than Dispatch Level. There is no case that our log manager needs to be used in higher IRQLs, so we don’t care about them; thus, we have two different approaches here. First, we acquire the lock \(spinlock\) using **KeAcquireSpinLock** in vmx non-root as it’s a Windows optimized way to acquire a lock and for vmx-root mode, we acquire the lock using our previously designed spinlock.

As I told you above, we want to fix this problem that might a vmx-exit occurs when we acquired a lock, so it’s not possible to use the same spinlock as deadlock might happen.

Now we have to see whether we are operating from vmx non-root or vmx root, based on this condition, we select our lock and the index of the buffer that we want to put our message into it.

I’m not gonna explain each step, as it’s easy, it’s just managing buffer and copying data from a buffer to another buffer and also the code is well commented so you can read the code, instead, I explain tricky parts of our message tracing.

After creating a header for our new message buffer, we will copy the bytes and change the information about buffer’s indexes. The last step here is to see whether any thread is waiting to receive our message or not.

If there is no thread waiting for our message then nothing more to do here but if there is a thread which is IRP Pending state \(I explain about it later\), then we use **KeInsertQueueDpc** so that it will be added to our DPC Queue which will be subsequently executed by Windows in IRQL == **DISPATCH\_LEVEL**.

It means that our callback function will execute by Windows later and of course, Windows execute our function in vmx non-root so it’s safe. I’ll describe this callback and how we create a DPC later.

Finally, we have to release the locks so that other threads can enter.

```c
/* Save buffer to the pool */
BOOLEAN LogSendBuffer(UINT32 OperationCode, PVOID Buffer, UINT32 BufferLength)
{
	KIRQL OldIRQL;
	UINT32 Index;
	BOOLEAN IsVmxRoot;

	if (BufferLength > PacketChunkSize - 1 || BufferLength == 0)
	{
		// We can't save this huge buffer
		return FALSE;
	}

	// Check that if we're in vmx root-mode
	IsVmxRoot = GuestState[KeGetCurrentProcessorNumber()].IsOnVmxRootMode;

	// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
	if (IsVmxRoot)
	{
		// Set the index
		Index = 1;
		SpinlockLock(&VmxRootLoggingLock);
	}
	else
	{
		// Set the index
		Index = 0;
		// Acquire the lock 
		KeAcquireSpinLock(&MessageBufferInformation[Index].BufferLock, &OldIRQL);
	}

	// check if the buffer is filled to it's maximum index or not
	if (MessageBufferInformation[Index].CurrentIndexToWrite > MaximumPacketsCapacity - 1)
	{
		// start from the begining
		MessageBufferInformation[Index].CurrentIndexToWrite = 0;
	}

	// Compute the start of the buffer header
	BUFFER_HEADER* Header = (BUFFER_HEADER*)((UINT64)MessageBufferInformation[Index].BufferStartAddress + (MessageBufferInformation[Index].CurrentIndexToWrite * (PacketChunkSize + sizeof(BUFFER_HEADER))));

	// Set the header
	Header->OpeationNumber = OperationCode;
	Header->BufferLength = BufferLength;
	Header->Valid = TRUE;

	/* Now it's time to fill the buffer */

	// compute the saving index
	PVOID SavingBuffer = ((UINT64)MessageBufferInformation[Index].BufferStartAddress + (MessageBufferInformation[Index].CurrentIndexToWrite * (PacketChunkSize + sizeof(BUFFER_HEADER))) + sizeof(BUFFER_HEADER));

	// Copy the buffer
	RtlCopyBytes(SavingBuffer, Buffer, BufferLength);

	// Increment the next index to write
	MessageBufferInformation[Index].CurrentIndexToWrite = MessageBufferInformation[Index].CurrentIndexToWrite + 1;

	// check if there is any thread in IRP Pending state, so we can complete their request
	if (GlobalNotifyRecord != NULL)
	{
		/* there is some threads that needs to be completed */
		// set the target pool
		GlobalNotifyRecord->CheckVmxRootMessagePool = IsVmxRoot;
		// Insert dpc to queue
		KeInsertQueueDpc(&GlobalNotifyRecord->Dpc, GlobalNotifyRecord, NULL);

		// set notify routine to null
		GlobalNotifyRecord = NULL;
	}

	// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
	if (IsVmxRoot)
	{
		SpinlockUnlock(&VmxRootLoggingLock);
	}
	else
	{
		// Release the lock
		KeReleaseSpinLock(&MessageBufferInformation[Index].BufferLock, OldIRQL);
	}
}
```

### **Reading Phase \(Read buffers and send them to user-mode\)**

It’s time to read the previously filled buffer! The fact that we add a DPC in the previous function “**LogSendBuffer**” shows that the “**LogReadBuffer**” is executed in vmx non-root mode so we can freely use most of the APIs \(not all of them\).

Theoretically, we have a problem here, if we want to read a buffer from the vmx root-mode pool, then it might cause a deadlock as we acquired a vmx root-mode lock and might a vm-exit occur. Hence, we spin on this lock in vmx root mode forever, but practically there is no deadlock here. Can you guess why?

It’s because our **LogReadBuffer** executes in **DISPATCH\_LEVEL** so the Windows scheduler won’t interrupt us, and our function is executed without any interruption and the fact that we’re not doing anything fancy here. I mean, we’re not performing anything \(like CPUID\) that causes a vm-exit in our code, so practically there is nothing to cause deadlock here, but we should keep in mind that we’re not allowed to run codes that cause vmx-exit.

We compute the header address based on previous information and also set the valid bit to zero so that it shows that this buffer is previously used.

Then we copy the buffer to the buffer that specified in arguments also put the Operation Number on the top of the target buffer so that the future functions will know about the intention of this buffer. We can also use DbgPrint to show the messages to the kernel debugger. Using **DbgPrint** in **DISPATCH\_LEVEL** \(vmx non-root mode\) is safe. We might need to use DbgPrint multiple times as this function has a maximum of 512 bytes by default. Even though you can change the limit number but we assume the default size is selected.

Finally, we have to reset some of the information regarding buffer, clear the buffer messages \(it’s not necessary to zero the buffer, but for making debug process easier, I prefer to zero the buffer\), and release the locks.

```c
/* return of this function shows whether the read was successfull or not (e.g FALSE shows there's no new buffer available.)*/
BOOLEAN LogReadBuffer(BOOLEAN IsVmxRoot, PVOID BufferToSaveMessage, UINT32* ReturnedLength) {

	KIRQL OldIRQL;
	UINT32 Index;

	// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
	if (IsVmxRoot)
	{
		// Set the index
		Index = 1;

		// Acquire the lock 
		SpinlockLock(&VmxRootLoggingLock);
	}
	else
	{
		// Set the index
		Index = 0;

		// Acquire the lock 
		KeAcquireSpinLock(&MessageBufferInformation[Index].BufferLock, &OldIRQL);
	}

	// Compute the current buffer to read
	BUFFER_HEADER* Header = (BUFFER_HEADER*)((UINT64)MessageBufferInformation[Index].BufferStartAddress + (MessageBufferInformation[Index].CurrentIndexToSend * (PacketChunkSize + sizeof(BUFFER_HEADER))));

	if (!Header->Valid)
	{
		// there is nothing to send
		return FALSE;
	}

	/* If we reached here, means that there is sth to send  */
	// First copy the header 
	RtlCopyBytes(BufferToSaveMessage, &Header->OpeationNumber, sizeof(UINT32));


	// Second, save the buffer contents
	PVOID SendingBuffer = ((UINT64)MessageBufferInformation[Index].BufferStartAddress + (MessageBufferInformation[Index].CurrentIndexToSend * (PacketChunkSize + sizeof(BUFFER_HEADER))) + sizeof(BUFFER_HEADER));
	PVOID SavingAddress = ((UINT64)BufferToSaveMessage + sizeof(UINT32)); // Because we want to pass the header of usermode header
	RtlCopyBytes(SavingAddress, SendingBuffer, Header->BufferLength);


#if ShowMessagesOnDebugger

	// Means that show just messages
	if (Header->OpeationNumber <= OPERATION_LOG_NON_IMMEDIATE_MESSAGE)
	{
		/* We're in Dpc level here so it's safe to use DbgPrint*/
		// DbgPrint limitation is 512 Byte
		if (Header->BufferLength > DbgPrintLimitation)
		{
			for (size_t i = 0; i <= Header->BufferLength / DbgPrintLimitation; i++)
			{
				if (i != 0)
				{
					DbgPrint("%s", (char*)((UINT64)SendingBuffer + (DbgPrintLimitation * i) - 2));
				}
				else
				{
					DbgPrint("%s", (char*)((UINT64)SendingBuffer + (DbgPrintLimitation * i)));
				}
			}
		}
		else
		{
			DbgPrint("%s", (char*)SendingBuffer);
		}

	}
#endif

	// Finally, set the current index to invalid as we sent it
	Header->Valid = FALSE;

	// Set the length to show as the ReturnedByted in usermode ioctl funtion + size of header
	*ReturnedLength = Header->BufferLength + sizeof(UINT32);


	// Last step is to clear the current buffer (we can't do it once when CurrentIndexToSend is zero because
	// there might be multiple messages on the start of the queue that didn't read yet)
	// we don't free the header
	RtlZeroMemory(SendingBuffer, Header->BufferLength);

	// Check to see whether we passed the index or not
	if (MessageBufferInformation[Index].CurrentIndexToSend > MaximumPacketsCapacity - 2)
	{
		MessageBufferInformation[Index].CurrentIndexToSend = 0;
	}
	else
	{
		// Increment the next index to read
		MessageBufferInformation[Index].CurrentIndexToSend = MessageBufferInformation[Index].CurrentIndexToSend + 1;
	}

	// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
	if (IsVmxRoot)
	{
		SpinlockUnlock(&VmxRootLoggingLock);
	}
	else
	{
		// Release the lock
		KeReleaseSpinLock(&MessageBufferInformation[Index].BufferLock, OldIRQL);
	}
}
```

### **Checking for new messages**

Checking for the new message is simple; we just need to check the current message index based on previous information and see if its header is valid or not. If it’s valid then it shows that we have a new message, but if it’s not valid, then some function reads the message previously, and there is no new message.

For checking the new message, we even don’t need to acquire a lock because basically we don’t write anything and in our case reading doesn’t need a lock.

```c
/* return of this function shows whether the read was successfull or not (e.g FALSE shows there's no new buffer available.)*/
BOOLEAN LogCheckForNewMessage(BOOLEAN IsVmxRoot) {

	KIRQL OldIRQL;
	UINT32 Index;

	if (IsVmxRoot)
	{
		Index = 1;
	}
	else
	{
		Index = 0;
	}
	// Compute the current buffer to read
	BUFFER_HEADER* Header = (BUFFER_HEADER*)((UINT64)MessageBufferInformation[Index].BufferStartAddress + (MessageBufferInformation[Index].CurrentIndexToSend * (PacketChunkSize + sizeof(BUFFER_HEADER))));

	if (!Header->Valid)
	{
		// there is nothing to send
		return FALSE;
	}

	/* If we reached here, means that there is sth to send  */
	return TRUE;
}
```

### **Sending messages to pools**

Previously, we see how to save \(send\) buffers and read them. Each message is a buffer of strings, so finally, we have to use “**LogSendBuffer**” to send our buffer, but we need to consider extra effort to send a well-formed message.

**va\_start** and **va\_end** are used to support multiple arguments to one function, e.g like **DbgPrint** or **printf**.

You can use a combination of **KeQuerySystemTime**, **ExSystemTimeToLocalTime,** and **RtlTimeToTimeFields** to get the current system time \(see the example\) then putting them together with **sprintf\_s**.

There is a particular reason why we use the sprintf-like function instead of **RtlString\*** functions; the reason is described in the **Discussion** section. The next step is computing length using **strnlen\_s**.

Finally, we have a vital optimization here; logically we create two kinds of messages, one called “Immediate Message” which we will directly send it into the pool and another type is “Non-Immediate Message” which we gather the messages in another buffer and append new messages in that buffer until its capacity is full \(we shouldn’t pass the **PacketChunkSize** limit\).

Using this way, we don’t send each message to the user-mode separately but instead, we send multiple messages in one buffer to the user-mode. We will gain visible performance improvement. For example with a configuration with **PacketChunkSize == 1000 bytes** we send 6 messages on a buffer \(it’s average basically it depends on each message size\) because you probably know that CPU has to do a lot to change its state from kernel-mode to user-mode and also creating new IRP Packet is a heavy task.

You can also change the configuration, e.g., increase the **PacketChunkSize** so that more messages will hold on the temporary buffer, but generally, it delays the time you see the message.

Also, we work on a buffer so we need another spinlock here.

Putting it all together we have the following code :

```c
// Send string messages and tracing for logging and monitoring
BOOLEAN LogSendMessageToQueue(UINT32 OperationCode, BOOLEAN IsImmediateMessage, BOOLEAN ShowCurrentSystemTime, const char* Fmt, ...)
{
	BOOLEAN Result;
	va_list ArgList;
	size_t WrittenSize;
	UINT32 Index;
	KIRQL OldIRQL;
	BOOLEAN IsVmxRootMode;
	int SprintfResult;
	char LogMessage[PacketChunkSize];
	char TempMessage[PacketChunkSize];
	char TimeBuffer[20] = { 0 };

	// Set Vmx State
	IsVmxRootMode = GuestState[KeGetCurrentProcessorNumber()].IsOnVmxRootMode;

	if (ShowCurrentSystemTime)
	{
		// It's actually not necessary to use -1 but because user-mode code might assume a null-terminated buffer so
		// it's better to use - 1
		va_start(ArgList, Fmt);
		// We won't use this because we can't use in any IRQL
		/*Status = RtlStringCchVPrintfA(TempMessage, PacketChunkSize - 1, Fmt, ArgList);*/
		SprintfResult = vsprintf_s(TempMessage, PacketChunkSize - 1, Fmt, ArgList);
		va_end(ArgList);

		// Check if the buffer passed the limit
		if (SprintfResult == -1)
		{
			// Probably the buffer is large that we can't store it
			return FALSE;
		}

		// Fill the above with timer
		TIME_FIELDS TimeFields;
		LARGE_INTEGER SystemTime, LocalTime;
		KeQuerySystemTime(&SystemTime);
		ExSystemTimeToLocalTime(&SystemTime, &LocalTime);
		RtlTimeToTimeFields(&LocalTime, &TimeFields);

		// We won't use this because we can't use in any IRQL
		/*Status = RtlStringCchPrintfA(TimeBuffer, RTL_NUMBER_OF(TimeBuffer),
			"%02hd:%02hd:%02hd.%03hd", TimeFields.Hour,
			TimeFields.Minute, TimeFields.Second,
			TimeFields.Milliseconds);

		// Append time with previous message
		Status = RtlStringCchPrintfA(LogMessage, PacketChunkSize - 1, "(%s)\t %s", TimeBuffer, TempMessage);*/

		// this function probably run without error, so there is no need to check the return value
		sprintf_s(TimeBuffer, RTL_NUMBER_OF(TimeBuffer), "%02hd:%02hd:%02hd.%03hd", TimeFields.Hour,
			TimeFields.Minute, TimeFields.Second,
			TimeFields.Milliseconds);

		// Append time with previous message
		SprintfResult = sprintf_s(LogMessage, PacketChunkSize - 1, "(%s - core : %d - vmx-root? %s)\t %s", TimeBuffer, KeGetCurrentProcessorNumberEx(0), IsVmxRootMode ? "yes" : "no", TempMessage);

		// Check if the buffer passed the limit
		if (SprintfResult == -1)
		{
			// Probably the buffer is large that we can't store it
			return FALSE;
		}


	}
	else
	{
		// It's actually not necessary to use -1 but because user-mode code might assume a null-terminated buffer so
		// it's better to use - 1
		va_start(ArgList, Fmt);
		// We won't use this because we can't use in any IRQL
		/* Status = RtlStringCchVPrintfA(LogMessage, PacketChunkSize - 1, Fmt, ArgList); */
		SprintfResult = vsprintf_s(LogMessage, PacketChunkSize - 1, Fmt, ArgList);
		va_end(ArgList);

		// Check if the buffer passed the limit
		if (SprintfResult == -1)
		{
			// Probably the buffer is large that we can't store it
			return FALSE;
		}

	}
	// Use std function because they can be run in any IRQL
	// RtlStringCchLengthA(LogMessage, PacketChunkSize - 1, &WrittenSize);
	WrittenSize = strnlen_s(LogMessage, PacketChunkSize - 1);

	if (LogMessage[0] == '\0') {

		// nothing to write
		DbgBreakPoint();
		return FALSE;
	}

	if (IsImmediateMessage)
	{
		return LogSendBuffer(OperationCode, LogMessage, WrittenSize);
	}
	else
	{
		// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
		if (IsVmxRootMode)
		{
			// Set the index
			Index = 1;
			SpinlockLock(&VmxRootLoggingLockForNonImmBuffers);
		}
		else
		{
			// Set the index
			Index = 0;
			// Acquire the lock 
			KeAcquireSpinLock(&MessageBufferInformation[Index].BufferLockForNonImmMessage, &OldIRQL);
		}
		//Set the result to True
		Result = TRUE;

		// If log message WrittenSize is above the buffer then we have to send the previous buffer
		if ((MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer + WrittenSize) > PacketChunkSize - 1 && MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer != 0)
		{

			// Send the previous buffer (non-immediate message)
			Result = LogSendBuffer(OPERATION_LOG_NON_IMMEDIATE_MESSAGE,
				MessageBufferInformation[Index].BufferForMultipleNonImmediateMessage,
				MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer);

			// Free the immediate buffer
			MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer = 0;
			RtlZeroMemory(MessageBufferInformation[Index].BufferForMultipleNonImmediateMessage, PacketChunkSize);
		}

		// We have to save the message
		RtlCopyBytes(MessageBufferInformation[Index].BufferForMultipleNonImmediateMessage +
			MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer, LogMessage, WrittenSize);

		// add the length 
		MessageBufferInformation[Index].CurrentLengthOfNonImmBuffer += WrittenSize;


		// Check if we're in Vmx-root, if it is then we use our customized HIGH_IRQL Spinlock, if not we use the windows spinlock
		if (IsVmxRootMode)
		{
			SpinlockUnlock(&VmxRootLoggingLockForNonImmBuffers);
		}
		else
		{
			// Release the lock
			KeReleaseSpinLock(&MessageBufferInformation[Index].BufferLockForNonImmMessage, OldIRQL);
		}

		return Result;
	}
}
```

### **Receiving buffers and messages in user-mode**

Receiving buffers from the user-mode is done by using an IOCTL. First, we create another thread in our user-mode application. This thread is responsible for bringing the kernel-mode buffers to the user-mode and then operate based on Operation Number.

```c
		HANDLE Thread = CreateThread(NULL, 0, ThreadFunc, Handle, 0, NULL);
		if (Thread) {
			printf("[*] Thread Created successfully !!!");
		}
```

This thread executes the following function. We use **IRP Pending** for transferring data from kernel-mode to user-mode. IRP Pending is primarily used for transferring a packet. For example, you send an IRP packet to the kernel, and kernel marks this packet as **Pending**. Whenever the user-mode buffer is available to send to the user-mode, the kernel completes the IRP request, and the IOCTL function returns to the user-mode and continues the execution.

It’s somehow like when you use Wait for an object. We can also use **events** in Windows and whenever the buffer is available the event is triggered but IRP Pending is better as it designed for the purpose of sending messages to user-mode.

What we have to do is allocating a buffer for kernel-mode code and using **DeviceIoControl** to request the packet. When the packet from the kernel received, we process the packet and switch through the Operation Number.

```c
void ReadIrpBasedBuffer(HANDLE  Device) {

	BOOL    Status;
	ULONG   ReturnedLength;
	REGISTER_EVENT RegisterEvent;
	UINT32 OperationCode;

	printf(" =============================== Kernel-Mode Logs (Driver) ===============================\n");
	RegisterEvent.hEvent = NULL;
	RegisterEvent.Type = IRP_BASED;
	char OutputBuffer[UsermodeBufferSize + 100] = { 0 };

	try
	{

		while (TRUE) {

			ZeroMemory(OutputBuffer, UsermodeBufferSize);

			Sleep(200);							// we're not trying to eat all of the CPU ;)

			Status = DeviceIoControl(
				Device,							// Handle to device
				IOCTL_REGISTER_EVENT,			// IO Control code
				&RegisterEvent,					// Input Buffer to driver.
				SIZEOF_REGISTER_EVENT * 2,		// Length of input buffer in bytes. (x 2 is bcuz as the driver is x64 and has 64 bit values)
				OutputBuffer,					// Output Buffer from driver.
				sizeof(OutputBuffer),			// Length of output buffer in bytes.
				&ReturnedLength,				// Bytes placed in buffer.
				NULL							// synchronous call
			);

			if (!Status) {
				printf("Ioctl failed with code %d\n", GetLastError());
				break;
			}
			printf("\n========================= Kernel Mode (Buffer) =========================\n");

			OperationCode = 0;
			memcpy(&OperationCode, OutputBuffer, sizeof(UINT32));

			printf("Returned Length : 0x%x \n", ReturnedLength);
			printf("Operation Code : 0x%x \n", OperationCode);

			switch (OperationCode)
			{
			case OPERATION_LOG_NON_IMMEDIATE_MESSAGE:
				printf("A buffer of messages (OPERATION_LOG_NON_IMMEDIATE_MESSAGE) :\n");
				printf("%s", OutputBuffer + sizeof(UINT32));
				break;
			case OPERATION_LOG_INFO_MESSAGE:
				printf("Information log (OPERATION_LOG_INFO_MESSAGE) :\n");
				printf("%s", OutputBuffer + sizeof(UINT32));
				break;
			case OPERATION_LOG_ERROR_MESSAGE:
				printf("Error log (OPERATION_LOG_ERROR_MESSAGE) :\n");
				printf("%s", OutputBuffer + sizeof(UINT32));
				break;
			case OPERATION_LOG_WARNING_MESSAGE:
				printf("Warning log (OPERATION_LOG_WARNING_MESSAGE) :\n");
				printf("%s", OutputBuffer + sizeof(UINT32));
				break;

			default:
				break;
			}


			printf("\n========================================================================\n");
		}
	}
	catch (const std::exception&)
	{
		printf("\n Exception !\n");
	}
}

```

### **IOCTL and managing user-mode requests**

When the IOCTL arrived on the kernel side, **DrvDispatchIoControl** from major functions is called. This function returns a pointer to the caller’s I/O stack location in the specified IRP.

From the IRP Stack we can read the IOCTL code and buffers address, this time we perform necessary checks and pass the arguments to **LogRegisterIrpBasedNotification**.

```c

/* Driver IOCTL Dispatcher*/
NTSTATUS DrvDispatchIoControl(PDEVICE_OBJECT DeviceObject, PIRP Irp)
{
	PIO_STACK_LOCATION  IrpStack;
	PREGISTER_EVENT RegisterEvent;
	NTSTATUS    Status;

	IrpStack = IoGetCurrentIrpStackLocation(Irp);

	switch (IrpStack->Parameters.DeviceIoControl.IoControlCode)
	{
	case IOCTL_REGISTER_EVENT:

		// First validate the parameters.
		if (IrpStack->Parameters.DeviceIoControl.InputBufferLength < SIZEOF_REGISTER_EVENT || Irp->AssociatedIrp.SystemBuffer == NULL) {
			Status = STATUS_INVALID_PARAMETER;
			DbgBreakPoint();
			break;
		}
		
		RegisterEvent = (PREGISTER_EVENT)Irp->AssociatedIrp.SystemBuffer;

		switch (RegisterEvent->Type) {
		case IRP_BASED:
			Status = LogRegisterIrpBasedNotification(DeviceObject, Irp);
			break;
		case EVENT_BASED:
			Status = LogRegisterEventBasedNotification(DeviceObject, Irp);
			break;
		default:
			ASSERTMSG("\tUnknow notification type from user-mode\n", FALSE);
			Status = STATUS_INVALID_PARAMETER;
			break;
		}
		break;

	default:
		ASSERT(FALSE);  // should never hit this
		Status = STATUS_NOT_IMPLEMENTED;
		break;
	}

	if (Status != STATUS_PENDING) {
		Irp->IoStatus.Status = Status;
		Irp->IoStatus.Information = 0;
		IoCompleteRequest(Irp, IO_NO_INCREMENT);
	}

	return Status;
}

```

To register an IRP notification, first, we check whether any other thread is pending by checking **GlobalNotifyRecord** if there is any thread we complete the IRP and return to the user-mode because in our design we ignore multiple threads that request the buffers means that only one thread can read the kernel-mode buffer.

Second, we initialize a custom structure that describes the state. The following structure is responsible for saving Type, DPC Object, and target buffer.

```c
typedef struct _NOTIFY_RECORD {
	NOTIFY_TYPE     Type;
	union {
		PKEVENT     Event;
		PIRP        PendingIrp;
	} Message;
	KDPC            Dpc;
	BOOLEAN			CheckVmxRootMessagePool; // Set so that notify callback can understand where to check (Vmx root or Vmx non-root)
} NOTIFY_RECORD, * PNOTIFY_RECORD;
```

In order to fill this structure, we initialize a DPC object by calling **KeInitializeDpc**, this function gets the function callback that should be called later \(**LogNotifyUsermodeCallback**\) and the parameter\(s\) to this function \(**NotifyRecord**\).

We first check the vmx non-root pools to see if anything new is available. Otherwise, we check the vmx-root mode buffer. This precedence is because vmx non-root buffers are more important. After all, we spent must of the time in VMX Root-mode, so we might see thousands of messages from vmx-root while we have fewer messages from vmx non-root. If we check the vmx root message buffer first, then we might lose some messages from vmx non-root or never find a time to process them.

If any new message is available then we directly add a DPC to the queue \(**KeInsertQueueDpc**\).

If there isn’t any new message available, then we simply save our Notify Record for future use, and also we mark IRP to pending state using **IoMarkIrpPending** and return **STATUS\_PENDING**.

```c
/* Register a new IRP Pending thread which listens for new buffers */
NTSTATUS LogRegisterIrpBasedNotification(PDEVICE_OBJECT DeviceObject, PIRP Irp)
{
	PNOTIFY_RECORD NotifyRecord;
	PIO_STACK_LOCATION IrpStack;
	KIRQL   OOldIrql;
	PREGISTER_EVENT RegisterEvent;

	// check if current core has another thread with pending IRP, if no then put the current thread to pending
	// otherwise return and complete thread with STATUS_SUCCESS as there is another thread waiting for message

	if (GlobalNotifyRecord == NULL)
	{
		IrpStack = IoGetCurrentIrpStackLocation(Irp);
		RegisterEvent = (PREGISTER_EVENT)Irp->AssociatedIrp.SystemBuffer;

		// Allocate a record and save all the event context.
		NotifyRecord = ExAllocatePoolWithQuotaTag(NonPagedPool, sizeof(NOTIFY_RECORD), POOLTAG);

		if (NULL == NotifyRecord) {
			return  STATUS_INSUFFICIENT_RESOURCES;
		}

		NotifyRecord->Type = IRP_BASED;
		NotifyRecord->Message.PendingIrp = Irp;

		KeInitializeDpc(&NotifyRecord->Dpc, // Dpc
			LogNotifyUsermodeCallback,     // DeferredRoutine
			NotifyRecord        // DeferredContext
		);

		IoMarkIrpPending(Irp);

		// check for new message (for both Vmx-root mode or Vmx non root-mode)
		if (LogCheckForNewMessage(FALSE))
		{
			// check vmx root
			NotifyRecord->CheckVmxRootMessagePool = FALSE;

			// Insert dpc to queue
			KeInsertQueueDpc(&NotifyRecord->Dpc, NotifyRecord, NULL);
		}
		else if (LogCheckForNewMessage(TRUE))
		{
			// check vmx non-root
			NotifyRecord->CheckVmxRootMessagePool = TRUE;

			// Insert dpc to queue
			KeInsertQueueDpc(&NotifyRecord->Dpc, NotifyRecord, NULL);
		}
		else
		{
			// Set the notify routine to the global structure
			GlobalNotifyRecord = NotifyRecord;
		}

		// We will return pending as we have marked the IRP pending.
		return STATUS_PENDING;
	}
	else
	{
		return STATUS_SUCCESS;
	}
}
```

### **Usermode notify callback**

As you see in the above codes, we add DPCs to queue in two functions \(**LogRegisterIrpBasedNotification and LogSendBuffer**\). This way, we won’t miss anything, and everything is processed as a message is generated. For example, if there is any thread waiting for the message then **LogSendBuffer** notifies it about the new message, if there isn’t any thread waiting for the message then **LogSendBuffer** can’t do anything, as long as a new thread comes to the kernel then it checks for the new message. Think about it one more time. It’s beautiful.

Now it’s time to read the packets from kernel pools and send them to the user-mode.

When **LogNotifyUsermodeCallback** is called then we sure that we’re in **DISPATCH\_LEVEL** and vmx non-root mode.

In this function, we check if the parameters sent to the kernel are valid or not. It’s because the user-mode provides them. For example, we check the IRP stack’s **Parameters. DeviceIoControl. InputBufferLength** and **Parameters. DeviceIoControl. OutputBufferLength** to make sure they are not null or check whether the **SystemBuffer** is null or not.

Then we call **LogReadBuffer** with user-mode buffers, so this function will fill the user-mode buffer and adds the Operation Number in a suitable place. Also, **Irp-&gt;IoStatus.Information** provides the buffer length to the user-mode.

The last step here is to complete the IRP, so I/O Manager sends the results to the user-mode, and the thread can continue to its normal life.

The reason why we access the user-mode buffer in all processes \(because DPCs might run on the random user-mode process\) and why we use DPCs and don’t use other things like APCs is discussed in the **Discussion** section.

The following code demonstrates what we talked about it above.

```c
/* Complete the IRP in IRP Pending state and fill the usermode buffers with pool data */
VOID LogNotifyUsermodeCallback(PKDPC Dpc, PVOID DeferredContext, PVOID SystemArgument1, PVOID SystemArgument2)
{

	PNOTIFY_RECORD NotifyRecord;
	PIRP Irp;
	UINT32 Length;

	UNREFERENCED_PARAMETER(Dpc);
	UNREFERENCED_PARAMETER(SystemArgument1);
	UNREFERENCED_PARAMETER(SystemArgument2);

	NotifyRecord = DeferredContext;

	ASSERT(NotifyRecord != NULL); // can't be NULL
	_Analysis_assume_(NotifyRecord != NULL);

	switch (NotifyRecord->Type)
	{

	case IRP_BASED:
		Irp = NotifyRecord->Message.PendingIrp;

		if (Irp != NULL) {

			PCHAR OutBuff; // pointer to output buffer
			ULONG InBuffLength; // Input buffer length
			ULONG OutBuffLength; // Output buffer length
			PIO_STACK_LOCATION IrpSp;

			// Make suree that concurrent calls to notify function never occurs
			if (!(Irp->CurrentLocation <= Irp->StackCount + 1))
			{
				DbgBreakPoint();
				return;
			}

			IrpSp = IoGetCurrentIrpStackLocation(Irp);
			InBuffLength = IrpSp->Parameters.DeviceIoControl.InputBufferLength;
			OutBuffLength = IrpSp->Parameters.DeviceIoControl.OutputBufferLength;

			if (!InBuffLength || !OutBuffLength)
			{
				Irp->IoStatus.Status = STATUS_INVALID_PARAMETER;
				IoCompleteRequest(Irp, IO_NO_INCREMENT);
				break;
			}

			// Check again that SystemBuffer is not null
			if (!Irp->AssociatedIrp.SystemBuffer)
			{
				// Buffer is invalid
				return;
			}

			OutBuff = Irp->AssociatedIrp.SystemBuffer;
			Length = 0;

			// Read Buffer might be empty (nothing to send)
			if (!LogReadBuffer(NotifyRecord->CheckVmxRootMessagePool, OutBuff, &Length))
			{
				// we have to return here as there is nothing to send here
				return;
			}

			Irp->IoStatus.Information = Length;


			Irp->IoStatus.Status = STATUS_SUCCESS;
			IoCompleteRequest(Irp, IO_NO_INCREMENT);
		}
		break;

	case EVENT_BASED:

		// Signal the Event created in user-mode.
		KeSetEvent(NotifyRecord->Message.Event, 0, FALSE);

		// Dereference the object as we are done with it.
		ObDereferenceObject(NotifyRecord->Message.Event);

		break;

	default:
		ASSERT(FALSE);
		break;
	}

	if (NotifyRecord != NULL) {
		ExFreePoolWithTag(NotifyRecord, POOLTAG);
	}
}
```

### **Uninitialization Phase**

Nothing special, we just de-allocate the previously allocated buffers. Keep in mind that we should initialize the message tracer at the very first function of our driver so we can use it and, of course, uninitialize it at the end when we don’t have any message anymore.

```c
/* Uninitialize the buffer relating to log message tracing */
VOID LogUnInitialize()
{

	// de-allocate buffer for messages and initialize the core buffer information (for vmx-root core)
	for (int i = 0; i < 2; i++)
	{
		// Free each buffers
		ExFreePoolWithTag(MessageBufferInformation[i].BufferStartAddress, POOLTAG);
		ExFreePoolWithTag(MessageBufferInformation[i].BufferForMultipleNonImmediateMessage, POOLTAG);
	}

	// de-allocate buffers for trace message and data messages
	ExFreePoolWithTag(MessageBufferInformation, POOLTAG);
}
```

