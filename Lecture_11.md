# Detecting Page Faults

The code below detects and counts pagefaults
```
#include<stdio.h>
#include<string.h>

#define SIZE 3

int pageFaults = 0;
int frameIndex = 0;

int isPageHit(int *pageFrames, int page)
{
    for (int i = 0; i<SIZE; i++)
    {
        if (pageFrames[i] == page)
        {
            return 1;
        }
    }
    return 0;
}

int main()
{
    int pageFrames[SIZE];
    int pages[SIZE] = {1, 2, 1};

    memset(pageFrames, -1, sizeof(pageFrames));

    for (int i = 0; i<SIZE; i++)
    {
        if (!isPageHit(pageFrames, pages[i]))
        {
            pageFaults++;
            printf("Page Fault occurred. Page %d not found in the memory. Adding it to the memory\n", pages[i]);
            pageFrames[frameIndex] = pages[i];
            frameIndex++;
        }
        else
        {
            printf("No Page Fault. Page %d found in the memory.\n", pages[i]);
        }
    }

    printf("Total Page Faults: %d\n", pageFaults);

    return 0;
}
```
