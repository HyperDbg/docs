# patch the normal sequence of execution

One of the powerful features of HyperDbg's script engine is its ability to change the target program's normal execution flow. These changes are applied from the hypervisor so it's hidden from the view of the application.

To demonstrate an example of this method, we compiled the following c++ program, which is a simple infinite loop that always shows a message (`"test_bool is TRUE !\n"`). It's because the `test_bool` variable is filled with `ture`.

```clike
#include <iostream>
#include <Windows.h>

int main()
{
    bool test_bool = true;

	while (true)
	{
		if (test_bool == true)
		{
			std::cout << "test_bool is TRUE !\n";
		}
		else
		{
			std::cout << "test_bool is FALSE !\n";
		}

		Sleep(2000);
	}
}
```

You can use your favorite static analyzer like IDA Pro, or Ghidra to see the source code, but in the example, I used [x64dbg](https://x64dbg.com) to show you what exactly we want to patch

![](../../../.gitbook/assets/find-the-target-patch-address-x64dbg.PNG)

```clike
!epthook 004C5A1C pid 225c script {
	@zf = 0;
}
```

![](../../../.gitbook/assets/patch-the-target-address.PNG)

![](../../../.gitbook/assets/clearing-the-patch-events.PNG)
