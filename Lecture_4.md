# Process Programming

Fork command is used to create parent child processes. In below program Hello is printing only once.
```
#include<stdio.h>
#include<unistd.h>

int main()
{
	printf("Hello\n");
	fork();
	return 0;
}
```

In the below program, Hello is printed twice; one for parent and one for child
```
#include<stdio.h>
#include<unistd.h>

int main()
{
	fork();
	printf("Hello\n");
	return 0;
}
```

Similarly below program will print Hello 14 times
```
#include<stdio.h>
#include<unistd.h>

int main()
{
	fork();
	printf("Hello\n");
	fork();
	printf("Hello\n");
	fork();
	printf("Hello\n");
	return 0;
}
```
`getpid()` can be used get the ids for processes

```
#include<stdio.h>
#include<unistd.h>

int main()
{
	fork();
	printf("Process ID:%d\n", getpid());
	return 0;
}
```

`fork()` returns an integer that can help in distinguishing parent and child
```
#include<stdio.h>
#include<unistd.h>

int main()
{
	int i;
	i = fork();
	printf("Process ID:%d\n", i);

	return 0;
}

// if fork returns 0, its a child
// if fork returns <0, its an error
// if fork returns >0, its a Parent
```

Conditional statements can help in finding the parent and child
```
#include<stdio.h>
#include<unistd.h>

int main()
{
	int i;
	i = fork();

	if (i == 0)
	{
		printf("I am a child and my Process ID:%d\n", getpid());
	}
	else
	{
		printf("I am a parent and my Process ID:%d\n", getpid());
	}
	

	return 0;
}
```

Different methods can be processed using parent and child
```
#include<stdio.h>
#include<unistd.h>

void show_odd(void)
{
	for (int i=0; i<20; i++)
	{
		if (i%2 != 0)
		{
			printf("C - %d\n", i);
		}
	}
}
void show_even(void)
{
	for (int i=0; i<20; i++)
	{
		if (i%2 == 0)
		{
			printf("P - %d\n", i);
		}
	}
}

int main()
{
	int i;
	i = fork();

	if (i == 0)
	{
		show_odd();
	}
	else
	{
		show_even();
	}

	return 0;
}
```

Command line arguments can be used getting start and end ranges of showing numbers
```
#include<stdio.h>
#include<unistd.h>
#include <stdlib.h>

void show_odd(int start, int end)
{
	for (int i=start; i<end; i++)
	{
		if (i%2 != 0)
		{
			printf("C - %d\n", i);
		}
	}
}
void show_even(int start, int end)
{
	for (int i=start; i<end; i++)
	{
		if (i%2 == 0)
		{
			printf("P - %d\n", i);
		}
	}
}

int main(int args, char *argv[])
{
	int start = atoi(argv[1]);
	int end = atoi(argv[2]);

	if (fork() == 0)
	{
		show_odd(start, end);
	}
	else
	{
		show_even(start, end);
	}
	return 0;
}
```

You can run above program by providing arguments as below:
```./hello_fork.o 0 20```
