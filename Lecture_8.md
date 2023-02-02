# Banker's Algorithm

Below is the implementation of Banker's algorithm using C program
```
#include<stdlib.h>
#include<stdio.h>

#define NO_OF_PROCESSES 5
#define NO_OF_RESOURCES 3

struct Process
{
	int pid;
	int allocations[10];
	int maximum[10];
	int need[10];
	int completed;
	int flag;	
};

int main()
{
	struct Process proc[NO_OF_PROCESSES] = {

		{0, {0, 1, 0}, {7, 5, 3}, {7, 4, 3}, 0, 0},
		{1, {2, 0, 0}, {3, 2, 2}, {1, 2, 2}, 0, 0}, 
		{2, {3, 0, 2}, {9, 0, 2}, {6, 0, 0}, 0, 0}, 
		{3, {2, 1, 1}, {2, 2, 2}, {0, 1, 1}, 0, 0}, 
		{4, {0, 0, 2}, {4, 3, 3}, {4, 3, 1}, 0, 0}

	};

	int available[NO_OF_RESOURCES] = {3, 3, 2};
	int sequence[NO_OF_PROCESSES];
	int index = 0;

	printf("\nAvailable Resources: R0=%d, R1=%d, R2=%d\n", available[0], available[1], available[2]);

	printf("\nProcess \t Allocations \t Maximum \t Need\n");

	for (int i=0; i<NO_OF_PROCESSES; i++)
	{
		for (int j=0; j<NO_OF_RESOURCES; j++)
		{
			printf("P%d \t\t %d \t\t %d \t\t %d\n", proc[i].pid, proc[i].allocations[j], proc[i].maximum[j], proc[i].need[j]);
		}
		printf("---------------------------------------------------------------------------\n");
	}

	// Bankers Algorithm

	for (int k = 0; k< NO_OF_PROCESSES; k++)
	{
			for (int i=0; i<NO_OF_PROCESSES; i++)
			{
				if (proc[i].completed == 0)
				{
					proc[i].flag = 0;
					for (int j=0; j<NO_OF_RESOURCES; j++)
					{
						if (proc[i].need[j] > available[j])
						{
							proc[i].flag = 1;
							break;
						}
					}

					if (proc[i].flag == 0)
					{
						sequence[index] = i;
						index++;
						for (int x = 0; x<NO_OF_RESOURCES; x++)
						{
							available[x] += proc[i].allocations[x];
						}
						proc[i].completed = 1;
					}
	
				}

			}

	}

	printf("Following is the Safe State Sequence\n");

   	for (int i = 0; i < index; i++) 
   	{
        	printf(" P%d ->", sequence[i]); 
    }

	return 0;
}
```
