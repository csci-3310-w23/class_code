# Thread Programming

At the creation of a process, there will be a default thread in it. Lets get its ID
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

int main()
{
	
	printf("The ID Of the main thread: %lu \n", pthread_self());

	// can also convert into int and then show
	printf("The ID of the main thread: %d\n", (int)pthread_self());

	printf("The ID of the process: %d\n", getpid());
	return 0;
}
```

Threads can be created using `pthread`
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>


void *thread_function(void *args)
{
	printf("The ID of the child thread: %d\n", (int)pthread_self());
}

int main()
{
	printf("The ID of the main thread: %d\n", (int)pthread_self());

	pthread_t tid;

	pthread_create(&tid, NULL, &thread_function, NULL);

	pthread_join(tid, NULL);

	return 0;
}
```

Status of the thread can be checked to see if it is still running or has been terminated
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

void * thread_function(void *args)
{
	printf("I am a new thread with ID:%lu,\n", pthread_self());
}

int main()
{

	printf("The ID Of the main thread: %lu \n", pthread_self());
	
	pthread_t tid;
	int thread_status;

	pthread_create(&tid, NULL, &thread_function, NULL);
	pthread_join(tid, (void *)&thread_status);

	if (thread_status == 0) 
	{
    	printf("Thread is still running\n");
	} 
	else 
	{
    	printf("Thread has terminated\n");
	}

	return 0;
}
```

Threads can be cancelled as well by forcing them to not complete their execution
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

void * thread_function(void *args)
{
	printf("I am a new thread with ID:%lu,\n", pthread_self());
	sleep(10);
}

int main()
{

	printf("The ID Of the main thread: %lu \n", pthread_self());
	
	pthread_t tid;
	int thread_status;

	pthread_create(&tid, NULL, &thread_function, NULL);
	pthread_cancel(tid);
	pthread_join(tid, (void *)&thread_status);

	if (thread_status == 0) 
	{
    	printf("Thread is still running\n");
	} 
	else 
	{
    	printf("Thread has terminated\n");
	}

	return 0;
}
```

Arguments can be passed to a function that thread is going to work on
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

void * thread_function(void *args)
{
	int *end = args;
	for (int i=0; i<*end; i++)
	{
		if (i%2 == 0)
		{
			printf("i: %d\n", i);
		}
		
	}
}

int main(int args, char* argv[])
{

	printf("The ID Of the main thread: %lu \n", pthread_self());
	
	pthread_t tid;
	int thread_status;
	int end = atoi(argv[1]);

	pthread_create(&tid, NULL, &thread_function, &end);

	pthread_join(tid, (void *)&thread_status);

	if (thread_status == 0) 
	{
    	printf("Thread is still running\n");
	} 
	else 
	{
    	printf("Thread has terminated\n");
	}

	return 0;
}

// run this program as ./hello_thread.o 20
```

Multiple threads can be created as well that can do different things
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

void * even_function(void *args)
{
	int *end = args;
	for (int i=0; i<*end; i++)
	{
		if (i%2 == 0)
		{
			printf("i: %d\n", i);
		}
		
	}
}

void * odd_function(void *args)
{
	int *end = args;
	for (int i=0; i<*end; i++)
	{
		if (i%2 != 0)
		{
			printf("i: %d\n", i);
		}
		
	}
}

int main(int args, char* argv[])
{

	printf("The ID Of the main thread: %lu \n", pthread_self());
	
	pthread_t tid[2];
	int end = atoi(argv[1]);

	pthread_create(&tid[0], NULL, &even_function, &end);
	pthread_create(&tid[1], NULL, &odd_function, &end);

	pthread_join(tid[0], NULL);
	pthread_join(tid[1], NULL);

	return 0;
}
```

Threads can use attributes as well 
```
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>

void * thread_function(void * args)
{
	printf("I am the deteched thread\n");
}



int main(int args, char* argv[])
{

	printf("The ID Of the main thread: %lu \n", pthread_self());

	pthread_t tid;
	pthread_attr_t attr;

	pthread_attr_init(&attr);

	pthread_attr_setstacksize(&attr, 16384);
	//pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);

	pthread_create(&tid, &attr, &thread_function, NULL);
	
	pthread_attr_destroy(&attr);

	return 0;
}

// Above program might not show output from thread_function as main thread might complete before thread can be executed.
```
