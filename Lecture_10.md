# Virtual Memory

1. Writing to a virtual memory 
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/mman.h>

int main() {
  size_t pagesize = sysconf(_SC_PAGE_SIZE);

  // mmap
  void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
  void *ptr = mmap(NULL, pagesize, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);

  if (ptr == MAP_FAILED) {
    perror("mmap");
    return EXIT_FAILURE;
  }

  printf("Accessing virtual memory at address: %p\n", ptr);
  *(int *)ptr = 42;
  printf("Value at address %p is: %d\n", ptr, *(int *)ptr);

  munmap(ptr, pagesize);
  return EXIT_SUCCESS;
}
```

2. Writing character array to a virtual memory
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/mman.h>
#include <string.h>

int main()
{
	char *str = "Hello Virtual Memory";
	int len = strlen(str) + 1;

	void *ptr = mmap(NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);

	printf("Accessing Virtual Memory: %p\n", ptr);
	sprintf((char *)ptr,"%s", str);
	printf("Write: %s\n", (char*)ptr);

	munmap(ptr, len);
	return 0;
}
```
3. Two virtual memories, one each of even and odd
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/mman.h>

int main() {
  int numbers[] = {1, 2, 3, 4, 5};
  int even_count = 0, odd_count = 0;
  size_t len = sizeof(numbers);
  void *even_ptr = mmap(NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
  void *odd_ptr = mmap(NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);

  if (even_ptr == MAP_FAILED || odd_ptr == MAP_FAILED) {
    perror("mmap");
    return EXIT_FAILURE;
  }

  for (int i = 0; i < sizeof(numbers) / sizeof(numbers[0]); i++) {
    if (numbers[i] % 2 == 0) {
      ((int *)even_ptr)[even_count++] = numbers[i];
    } else {
      ((int *)odd_ptr)[odd_count++] = numbers[i];
    }
  }

  printf("Even numbers: ");
  for (int i = 0; i < even_count; i++) {
    printf("%d ", ((int *)even_ptr)[i]);
  }
  printf("\n");

  printf("Odd numbers: ");
  for (int i = 0; i < odd_count; i++) {
    printf("%d ", ((int *)odd_ptr)[i]);
  }
  printf("\n");

  munmap(even_ptr, len);
  munmap(odd_ptr, len);
  return EXIT_SUCCESS;
}
```
