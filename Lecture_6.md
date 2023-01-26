# Process Synchronization

This program is for reader and writer problem that might occur in OS. Using Semaphores we are solving the problem so that a shared variable `data` is not written with conflicts.

```
// including required libraries
#include <stdlib.h>
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

//setting up global variables
int data = 0, read_count = 0;
//semaphores for locking critical sections
sem_t mutex, rw_mutex;

//reader code
void *reader(void *thread)
{
  // value of i from the for loop in main method is stored in t to show in printf
	int t = (int)(intptr_t)thread;
	
	// Entry Section code
  
  // locking the semaphore to enter the critical section for readers which is to avoid writing read_count by multiple readers
	sem_wait(&mutex);
	read_count++;
  // check to see if there is any reader in the critical section
	if (read_count == 1)
	{
		// lock the reader writer semaphore so that no writer can enter the critical section while reader is in critical section
		sem_wait(&rw_mutex);
	}
	// releasing the lock for readers so that more readers can writer to read_count++ as this reader is done incrementing read_count
	sem_post(&mutex);
	
	// Critical Section code
	printf("Data read by thread %d is %d\n", t, data);
	sleep(1);
	
	//Exit Section code
  
  // In order to decrement read_count, locking the mutex to avoid multiple readers decrementing this variable
	sem_wait(&mutex);
	read_count--;
  
  // if there is no more reader, release the lock for writer
	if (read_count == 0)
	{
		sem_post(&rw_mutex);
	}
  
  // release the lock for readers so they can decrement read_count
	sem_post(&mutex);
}

//writer code
void *writer(void *thread)
{
	int t  = (int)(intptr_t)thread;
	
	// Entry Section code
  // lock the reader writer semaphore so that no writer or reader can enter the critical section while writer is in critical section
	sem_wait(&rw_mutex);
	
	// Critical Section code
	data++;
	printf("Data written by thread %d is %d\n", t, data);
	sleep(1);
	
	//Exit Section code
  // releasing the lock so that other writers or readers can enter their critical section
	sem_post(&rw_mutex);
}

int main()
{
	// creating 5 readers and 5 writers
	pthread_t read[5], write[5];
	
	//initializing the semaphores; 
	// in sem_init, 0 is to tell the value of this semaphore is shared between the thread and 1 is the value of this semaphore;
	sem_init(&mutex, 0, 1);
	sem_init(&rw_mutex, 0, 1);

	//creating threads 
	for (int i=0; i<5; i++)
	{
  // intptr_t is used to make sure the memory space is used appropriately across different hardware architectures
		pthread_create(&write[i], NULL, writer, (void *)(intptr_t)i);
		pthread_create(&read[i], NULL, reader, (void *)(intptr_t)i);
	}
	
	//joining the threads
	for (int i=0; i<5; i++)
	{
		pthread_join(write[i], NULL);
		pthread_join(read[i], NULL);
	}
	
	return 0;
}






