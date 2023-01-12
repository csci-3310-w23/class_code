
# Basic Linux Commands

1. Getting current working directory: pwd

2. cd is used to change director: cd Documents/

3. In order to go to home direction: cd ~

4. To go to parent direction: cd ..

5. To find current logged in user: whoami

6. To list files and folders in a directory: ls

7. To see all the hidden files and folders: ls -la

8. Programs can be opened from Terminal: firefox

9. Text files can be created with GUI based GEdit: gedit file.txt

10. Files can be copied using cp: cp file.txt ~/Desktop

11. Files can be moved from one directory to another: mv ~/Desktop/file.txt ~/Documents/temp/

12. Nano can be used to edit files directly from Terminal: nano file.txt

Hello World C Program

#include<stdio.h>

int main()
{
	printf("Hello World\n");
	return 0;
}

In order to compile we use: gcc hello.c -o hello.o

In order to execute: ./hello.o

