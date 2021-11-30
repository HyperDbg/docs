---
description: Using the .process, and the .thread commands
---

# Switching To A Specific Process Or Thread

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
