---
description: Using the '.process', and the '.thread' commands
---

# Switching to a Specific Process or Thread

Switching to new processes/threads is useful when debugging a special process or a user-mode application. These switchings are possible through the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' and '[.thread](https://docs.hyperdbg.org/commands/meta-commands/.thread)' commands.

In HyperDbg, there are different implementations of these switchings. You can read more about these differences [here](https://docs.hyperdbg.org/tips-and-tricks/considerations/difference-between-process-and-thread-switching-commands).

In this example, we walk through an example to show how to use these commands.

```clike
#include <Windows.h>
#include <conio.h>
#include <iostream>

int main() {

	bool Test = true;
	UINT64 Counter = 0;

	while (Test) {

		if (Counter % 1000000000 == 0) {
			printf("Thread is running (%lld)...\n", Counter);
		}
		Counter++;
	}

	printf("Thread is closed!\n");
	_getch();
}
```

![](../../../.gitbook/assets/1-process-list.png)

![](../../../.gitbook/assets/2-find-threads-of-test-process.png)

![](../../../.gitbook/assets/3-switch-to-the-target-thread.png)

![](../../../.gitbook/assets/4-disassembling-and-finding-jumps.png)

![](../../../.gitbook/assets/5-stepping-and-investigate-the-test-program.png)

![](../../../.gitbook/assets/6-patch-the-target-jump.png)

![](../../../.gitbook/assets/7-result-of-patching-target-program.png)
