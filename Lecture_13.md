# File Systems

`stat` library can be used to get info about the file system
```
#include <stdio.h>
#include<sys/stat.h>

int main(int args, char *argv[])
{
	struct stat file_stat;

	stat(argv[1], &file_stat);

	printf("\nDevice ID:%lu\n", file_stat.st_dev);
	printf("\nInode: :%lu\n", file_stat.st_ino);
	printf("\nMode:%u\n", file_stat.st_mode);
	printf("\nUser ID:%u\n", file_stat.st_uid);
	printf("\nGroup ID:%u\n", file_stat.st_gid);
	printf("\nFile Size:%lu\n", file_stat.st_size);

	getUserName(file_stat.st_uid);
	
}
```

This uid can be used to get the username using `pwd` library
```
#include <stdio.h>
#include<sys/stat.h>
#include<pwd.h>

void getUserName(int uid)
{
	struct passwd *pwd_stat;
	pwd_stat = getpwuid(uid);

	printf("User Name: %s\n", pwd_stat->pw_name);
}

int main(int args, char *argv[])
{
	struct stat file_stat;

	stat(argv[1], &file_stat);

	printf("\nDevice ID:%lu\n", file_stat.st_dev);
	printf("\nInode: :%lu\n", file_stat.st_ino);
	printf("\nMode:%u\n", file_stat.st_mode);
	printf("\nUser ID:%u\n", file_stat.st_uid);
	printf("\nGroup ID:%u\n", file_stat.st_gid);
	printf("\nFile Size:%lu\n", file_stat.st_size);

	getUserName(file_stat.st_uid);
	
}
```
Group name can be obtained from `grp` library
```
#include <stdio.h>
#include<sys/stat.h>
#include<pwd.h>
#include <grp.h>


void getGroupName(int gid)
{
	struct group *grp_stat;
	grp_stat = getgrgid(gid);

	printf("Group Name: %s\n", grp_stat->gr_name);
}

void getUserName(int uid)
{
	struct passwd *pwd_stat;
	pwd_stat = getpwuid(uid);

	printf("User Name: %s\n", pwd_stat->pw_name);
}

int main(int args, char *argv[])
{
	struct stat file_stat;

	stat(argv[1], &file_stat);

	printf("\nDevice ID:%lu\n", file_stat.st_dev);
	printf("\nInode: :%lu\n", file_stat.st_ino);
	printf("\nMode:%u\n", file_stat.st_mode);
	printf("\nUser ID:%u\n", file_stat.st_uid);
	printf("\nGroup ID:%u\n", file_stat.st_gid);
	printf("\nFile Size:%lu\n", file_stat.st_size);

	getUserName(file_stat.st_uid);
	getGroupName(file_stat.st_gid);
	
}
```
`unistd` library has a `getcwd()` function to get the current working directory
```
#include <stdio.h>
#include<unistd.h>

int main(int args, char *argv[])
{
	char workingDir[FILENAME_MAX];

	getcwd(workingDir, sizeof(workingDir));

	printf("%s\n", workingDir);
}
```

We can get all the files from the directory
```
#include <stdio.h>
#include<dirent.h>
#include<string.h>

int main(int args, char *argv[])
{
	struct dirent *entry;

	DIR *dr = opendir("/home/raziiqbal");

	while ((entry = readdir(dr)) != NULL )
	{
		if (!strncmp(entry->d_name, ".", strlen("."))==0)
		{
			printf("%s\n", entry->d_name);
		}
		
	}
	closedir(dr);
	return 0;
}
```
Gettting only directory names
```
#include <stdio.h>
#include<dirent.h>
#include<string.h>

int main(int args, char *argv[])
{
	struct dirent *entry;

	DIR *dr = opendir("/home/raziiqbal");

	while ((entry = readdir(dr)) != NULL )
	{
		if ((!strncmp(entry->d_name, ".", strlen("."))==0) && (entry->d_type == DT_DIR))
		{
			printf("%s\n", entry->d_name);
		}
		
	}
	closedir(dr);
	return 0;
}
```
