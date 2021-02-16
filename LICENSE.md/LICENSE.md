#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <getopt.h>
#include<string.h>
int showAll()
{
    char **p;
    system("clear");
    extern char **environ;
    printf("Environment Varibles");
    for(p = environ; *p != NULL; p++)
        printf("%s\n", *p);
}
int updateEnv(char **args,int argc)
{
	char *arg=optarg;
	putenv(arg);
	 if(optind<argc)
	 {
	 	for(;optind<argc;optind++)
			putenv(args[optind]);
	 }
	printf("Modified Environment\n");
	showAll();
}
int allocateEnv(char **args,int argc)
{
	char **newptr,*str;	
	int bal=0;
	int i=0;	
	char **p;
	extern char **environ;	
	for(p = environ; *p != NULL; p++)
		bal++;
	bal+=argc-optind+1;
	*newptr=(char*)malloc(2*bal);
	str=optarg;
	//putenv(str);
	newptr[i]=str;
	if(optind<argc)
	{
		for(i=1;optind<argc;optind++,i++)
		{
		
			newptr[i]=args[i];	
			//putenv(args[optind]);
		}
	}
	environ=newptr;
	printf(" Replacement of Environment\n");
	showAll();
}

int main(int argc, char *argv[]) 
{
	 int ch=0;
	 char *args,*toks;
 	 printf("total args:%d\n",argc);
	 if(argc==1)
	 {
		showAll();		
		printf("Usage: doenv -i [[name=value]...] -f [[name=value]...] -u [command]\n");
		return 0;
	 }
	 while ((ch = getopt(argc, argv,"i:f:u:")) != -1) 
	 {
	 	switch(ch)
	 	{
	 		case 'i':
				updateEnv(argv,argc);				
	 		break;
	 		case 'f':
				allocateEnv(argv,argc);				
	 		break;

	 		case 'u':
	 			args=optarg;
	 			if(system(args)==-1)
	 			{
	 				perror("Error=");
	 				return 0;
	 			}
	 		break;
	 		default:
	 			showAll();
	 	}
	 }
	 if(optind<argc)
	 {
	 	printf("Bal vars\n");
	 	for(;optind<argc;optind++)
	 		printf("%s\n",argv[optind]);
	 }
}
