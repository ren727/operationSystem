//Kazu Ishihara CS344  Reference: class sample codes --following the example, used unbounded buffer 

#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
#include<pthread.h>
#include<stdbool.h>
#include<stdbool.h>


#define MAX_CHAR 1100
#define SIZE 5000


char charStore_1[MAX_CHAR];                    //store characters for data manipulations--either receives from buffers or put into buffers
char charStore_2[MAX_CHAR];
char charStore_3[MAX_CHAR];
char charStore_4[MAX_CHAR];

char tempChar_1[MAX_CHAR];                      //store 80 characters temporarily from buffer_3

char printBuffer[50000];                        //store accumulated strings before processing
char printBuffer_1[50000];

int index2 = 0;                                 //buffer2 index
int index1 = 0;

char* temp_buff2;                                //to store buffer_2 content for data manupilation
char* userInput1;                                //temporarily holds user inputs

char buffer_1[MAX_CHAR];                         //buffer 1 between input thread and line separator

int count_1 = 0;                                 //the number of characters in the buffer_1
int prod_idx_1 = 0;                              //producer index for Input Thread
int con_idx_1 = 0;                               //consumer index for Line Separator Thread
bool flag1 = false;                               //to indicate stop

pthread_mutex_t mutex_1 = PTHREAD_MUTEX_INITIALIZER;   //mutex for buffer 1
pthread_cond_t full_1 = PTHREAD_COND_INITIALIZER;      //condition variable for buffer 1

char userInput[MAX_CHAR];                              //hold user input

char buffer_2[MAX_CHAR];

int count_2 = 0;                                   //the number of characters in the buffer 2
int prod_idx_2 = 0;                                //producer index for Line Separator thread
int con_idx_2 = 0;                                 //consumer index for Plus sign thread

pthread_mutex_t mutex_2 = PTHREAD_MUTEX_INITIALIZER;    //mutex for buffer 2
pthread_cond_t full_2 = PTHREAD_COND_INITIALIZER;       //condition variable for buffer 2 

char buffer_3[MAX_CHAR];                                //buffer 3 between Plus Sign thread and Output thread

int count_3 = 0;                                       //the number of characters in the buffer 3
int prod_idx_3 = 0;                                    //producer index for Plus Sign thread
int con_idx_3 = 0;                                     //consumer index for Output thread


pthread_mutex_t mutex_3 = PTHREAD_MUTEX_INITIALIZER;    //mutex for buffer 3
pthread_cond_t full_3 = PTHREAD_COND_INITIALIZER;       //condition variable for buffer 3


char plusChar[MAX_CHAR];                                 //hold string with second + removed  (for ++ replacement)
char plusIndex[MAX_CHAR];                                //track the position of first +

//int indexP = 0;
bool stop_flag = false;                                  //indicates if the user enter "STOP" or no
bool stdinFile = false;


char *get_userInput()                                   //for keyboard entry in the terminal
{
	
		fgets(userInput, MAX_CHAR, stdin);               //user input from terminal

		if (strncmp(userInput, "STOP\n", 5) == 0)        //bool will be true when STOP entered
		{
			stop_flag = true;
		}
	
		return userInput;
}

/*char* get_userInput_file(char* filePath)           //for reading file and get user inputs
{
		
		 FILE* textFile = fopen(filePath, "r");
		 fgets(userInput, sizeof(userInput), textFile);    //get inputs from the file -- put it into userInput

		 if (strncmp(userInput, "STOP\n", 5) == 0)        //bool will be true when STOP entered
		 {
			  stop_flag = true;
		 }

		fclose(textFile);                                 //close file

		return userInput;
	}*/

char* put_buff_1(char* array)                               //put userInput characters into the buffer_1
{
	pthread_mutex_lock(&mutex_1);
	for (int i = 0; i < strlen(array) + 1; i++)
	{
		buffer_1[prod_idx_1] = array[i];                    //prod_idx_1 for producer (input thread) -- track an input of items to the buffer_1
		prod_idx_1 = prod_idx_1 + 1;                        //increment prod_idx_1
			
    }
	count_1++;                                               //increment item in the buffer1
	pthread_cond_signal(&full_1);
	pthread_mutex_unlock(&mutex_1);                           //mutex1 unlock
	
	return buffer_1;
}

void* inputThread(void* args)                               //input thread funciton---Producer to Line Separator 
{  
		char* temp_Buffer = put_buff_1(userInput1);         //get user input and put it into buffer 1
}


char* get_buff_1()                                          //Give items in buff_1 to Line Separatior Thread
{
	pthread_mutex_lock(&mutex_1);                            //mutex_1 lock
	
	while (count_1 == 0)                                    //no item in the buffer_1 -- then wait
	{
		pthread_cond_wait(&full_1, &mutex_1);
	}
	
	char* tempBuffer = buffer_1 + con_idx_1;                //transfer items from buffer 1 for data manipulation. Use consumer index
	con_idx_1 += strlen(tempBuffer) + 1;
	
	count_1--;                                              //decrement item in the buffer (consumed)
	pthread_mutex_unlock(&mutex_1);                         //mutex1 unlocked
	
	return tempBuffer;

}

void* line_separator(void* args)                             //Line separator thread function  Replace the new lines and put the result to buffer_2
{
		char* string1 = get_buff_1();                         //As a consumer, get buffer 1 items
		
		pthread_mutex_lock(&mutex_2);                          //mutex2 locked
		for (int i = 0; i < strlen(string1) + 1; i++)
		{
			if (string1[i] == '\r' || string1[i] == '\n')       //whenever \n found in charStore, replace it with space
			{
				buffer_2[index2] = ' ';

			}

			else 
			{
				buffer_2[index2] = string1[i];                 //other items in buffer 1 will be transferred to buffer 2
			}

			index2 = index2 + 1;
		                                                       //increment the count for buffer_2
			
		}
		count_2++;
		pthread_cond_signal(&full_2);                          //conditional signal to indicate buffer_2 has data
		pthread_mutex_unlock(&mutex_2);                        //unlock mutex 2
}

char* get_buff2()                                            //for receive items from buffer2 for consumer (Plus Sign thread)
{
	pthread_mutex_lock(&mutex_2);                           //mutex2 locked
	
	while (count_2 == 0)                                    //no item in the buffer_1 ----wait
	{
		pthread_cond_wait(&full_2, &mutex_2);
	}
	
	char* tempBuffer1 = buffer_2 + con_idx_2;              //transfer items in buffer2 for data manipulation
	con_idx_2 += strlen(tempBuffer1) + 1;                  //use consumer index

     count_2--;                                              //decrement item in the buffer 2
		
	pthread_mutex_unlock(&mutex_2);                          //unlock mutex2
	
	return tempBuffer1;
}
void put_buffer_3();

void *plus_sign(void *args)                                //Plus Sign thread --- replace ++ with ^
{
	
		int i = 0;
		int j = 0;                                            //track plusChar array
		int k = 0;
		int indexP = 0;
		
		temp_buff2 = get_buff2();                             //get items in buffer 2 as consumer
		
		
		while (1)
		{
			if (i == strlen(temp_buff2)+1)                    //loop through each of the items in temp_buffs (items in buffer2)
			{
				break;
			}

			if (temp_buff2[i] == '+' && temp_buff2[i + 1] == '+')                  //two successive ++ found
			{
				plusChar[j] = temp_buff2[i];                                       //store only the first + to plusChar array

				plusIndex[indexP] = j;                                            //indicates a position of one +
				indexP += 1;

				j += 1;
				i += 2;
			}
			else
			{
				plusChar[j] = temp_buff2[i];                                      //no ++ found  -- just transfer the items to plusChar
				j++;
				i++;

			}
		}

		memset(temp_buff2, '\0', sizeof(temp_buff2));                          //copy plusChar items to temp_buff2
		strcpy(temp_buff2, plusChar);
		
        for (k = 0; k < indexP; k++)
		{
			temp_buff2[plusIndex[k]] = '^';                                    //change the initial +  to ^
		}
		
		put_buffer_3();                                                        //as a producer, put items to buffer 3
}

void put_buffer_3()                                                            //put items to buffer 3 --by a producer (plus sign thread)
{
	  pthread_mutex_lock(&mutex_3);
    
	for (int i = 0; i < strlen(temp_buff2) + 1; i++)
	{                                                                           //transfer items in buffer 2 to buffer 3
		 buffer_3[prod_idx_3] = temp_buff2[i];                                   //Use producer index to track buffer 3
         prod_idx_3 = prod_idx_3 + 1;                                           //increment the producer index
	}
	
	    count_3++;                                                              //increment the counter for buffer 3

		pthread_cond_signal(&full_3);                                           //conditional signal to indicates the buffer3 has data
        pthread_mutex_unlock(&mutex_3);                                         //unlock mutex 3
}

char* get_buffer_3()                                                            //For consumer (output thread) -- get buffer 3
{
	pthread_mutex_lock(&mutex_3);

	while (count_3 == 0)                                                        //no item in the buffer_1 wait
	{
		pthread_cond_wait(&full_3, &mutex_3);
	}
	
	char* tempBuffer3 = buffer_3 + con_idx_3;                                   //track with consumer index -- transfer buffer 3 to tempbuffer for data manipulation
	con_idx_3 += strlen(tempBuffer3) + 1;
	
	count_3--;                                                                //decrement item in the buffer 3
	
	pthread_mutex_unlock(&mutex_3);                                           //unlock mutex3

	return tempBuffer3;
}

void *outputThread(void *args)                                                //Output thread
{
	int index1 = 0;                                                            //track the temp array
	int charNum_inLine = 0;                                                    //count the number of characters in one line
	
		int i = 0;
		
		char* temp_buff3 = get_buffer_3();                                     //get buffer3 as consumer
		
		strcat(printBuffer, temp_buff3);                                       //accumulate incoming buffer3 items for display
		 
		while (i != strlen(printBuffer))                                        //loop through each of the item
		{

			if (charNum_inLine == 80)                                          //if the number of characters is 80
			{
				charNum_inLine = 0;                                             //reset the count
				tempChar_1[index1] = '\n';                                      //place \n in the temp array
				index1 += 1;

			}
			else
			{
				tempChar_1[index1] = printBuffer[i];                            //otherwise, put each item to the temp array
				index1 += 1;
				i++;
				charNum_inLine++;                                               //increment the number of characters in a line
			}
		}

		memset(printBuffer_1, '\0', sizeof(tempChar_1));
		strcpy(printBuffer_1, tempChar_1 );                                     //put the items to printBuffer 
		
	    char nl[2] = "\n";
		char* token = strtok(printBuffer_1, nl);                                 //tokenize it with delimiter \n
		
		while(token != NULL)
		{
			if (strlen(token) == 80)                                             //if the token length is 80  
			{
				 printf("%s\n", token);                                          //print it out
				 token = strtok(NULL, nl);
			}
			else                                                                 //not to print out any if it is less than 80
			{
				break;
			}
		 }
		
	
}

int main(int argc, char *argv[])          
{
	pthread_t input_t, line_sepa_t, plusSign_t, output_t;

	printf("\n");
	do
	{
		userInput1 = get_userInput();	
	    if (stop_flag == true)
		{
				break;
		}

		pthread_create(&input_t, NULL, inputThread, NULL);           //input thread
		pthread_create(&line_sepa_t, NULL, line_separator, NULL);     //line separator thread
	    pthread_create(&plusSign_t, NULL, plus_sign, NULL);           //plus sign thread
		pthread_create(&output_t, NULL, outputThread, NULL);          //output thread
	
		
		 pthread_join(input_t, NULL);
		 pthread_join(line_sepa_t, NULL);
		 pthread_join(plusSign_t, NULL);
		 pthread_join(output_t, NULL);
		 printf("\n");

	}while(stop_flag == false);

	
	return 0;
}








