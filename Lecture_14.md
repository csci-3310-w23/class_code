# File Processing

`fopen` function with w+ can help creating a file for read and write if it already does not exists
```
#include<stdio.h>

int main()
{
	FILE *file;

	file = fopen("/home/raziiqbal/Documents/temp/Exercise8/test.txt", "w+");

	if (file == NULL)
	{
		printf("Error reading file\n");
	}
	else
	{
		printf("File created successful\n");
		fclose(file);
	}

	return 0;
}
```

In order to write to text files, we can use `fprintf` function
```
#include <stdio.h>

int main() {

	int data = 17;
    FILE *file = fopen("output.txt", "w");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
    	printf("File opened successfully!\n");
    	fprintf(file, "%d", data);
    	fclose(file);
    }

    return 0;
}
```
In order to write strings to text files, we can use `fprintf` as below
```
#include <stdio.h>

int main() {

	char data[] = "Ontario Tech University";
    FILE *file = fopen("output.txt", "w");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
    	printf("File opened successfully!\n");
    	fprintf(file, "%s", data);
    	fclose(file);
    }

    return 0;
}
```
Data can be written using `fwrite` function
```
#include <stdio.h>

int main() {

    char data[] = "Ontario Tech University";
    FILE *file = fopen("output.txt", "w");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
    	printf("File opened successfully!\n");
    	fwrite(data, sizeof(char), sizeof(data), file);
    	fclose(file);
    }

    return 0;
}
```
`fscanf` can be used to read the content of the text file
```
#include <stdio.h>

int main() {

	char buffer[100];
    FILE *file = fopen("output.txt", "r");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
    	printf("File opened successfully!\n");

    	while(fscanf(file, "%s", buffer) != EOF)
    	{
    		printf("%s",buffer);
    	}
    	fclose(file);
    }

    return 0;
}
```

In order to read line by line, `fgets` can be used as below:
```
#include <stdio.h>

int main() {

	char buffer[100];
    FILE *file = fopen("output.txt", "r");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
    	printf("File opened successfully!\n");

    	while(fgets(buffer, sizeof(buffer), file))
    	{
    		printf("%s",buffer);
    	}
    	fclose(file);
    }

    return 0;
}
```
`fseek()` is used to start reading after a specific position
```
#include<stdio.h>
#include<string.h>

int main()
{
	FILE *file;
	char buffer[256];

	file = fopen("output.txt", "r");

	if (file == NULL)
	{
		printf("Error reading file\n");
	}
	else
	{
		fseek(file, 8, SEEK_SET);
		fread(buffer, sizeof(char), sizeof(buffer), file);
		printf("%s\n", buffer);
		fclose(file);
	}

	return 0;
}
```
In order to remove extra blank line at the end, we can use a simple if condition to replace `\n` with `\0`
```
#include <stdio.h>

int main() {

    char buffer[256];
    FILE *file = fopen("output.txt", "r");

    if (file == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    else
    {
        printf("File opened successfully!\n");

        fseek(file, 8, SEEK_SET);
        size_t bytes_read = fread(buffer, sizeof(char), sizeof(buffer), file);
        if (bytes_read > 0 && buffer[bytes_read - 1] == '\n') {
            buffer[bytes_read - 1] = '\0';
        }
        else {
            buffer[bytes_read] = '\0';
        }
        printf("%s\n", buffer);

        fclose(file);
    }

    return 0;
}
```

