# First Fit Memory Management Algorithm

Below is a C program for First Fit Memory Management Algorithm
```
#include<stdio.h>

#define BLOCK_SIZE 5
#define PROCESS_SIZE 5

int main()

{
	int blocks[BLOCK_SIZE] = {400, 200, 300, 500, 100};
	int processes[PROCESS_SIZE] = {100, 50, 550, 150, 480};
	int allocations[PROCESS_SIZE] = {-1, -1, -1, -1, -1};

	for (int i=0; i<PROCESS_SIZE; i++)
	{
		for (int j=0; j<BLOCK_SIZE; j++)
		{
			if (blocks[j] >= processes[i])
			{
				allocations[i] = j;
				blocks[j] = blocks[j] - processes[i];
				break;
			}
		}
	}

	printf("Process \t Process Size \t Allocations\n");

	for (int i=0; i<PROCESS_SIZE; i++)
	{
		printf("P%d \t\t %d\t\t\t", i, processes[i]);
		if (allocations[i] == -1)
		{
			printf("Unallocated\n");
		}
		else
		{
			printf("%d\n", allocations[i]);
		}
	}
	return 0;
}
```
