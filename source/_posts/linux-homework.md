---
title: 多进程双buffer读取并处理
date: 2020-12-06 00:00:00
tags: 
	- linux
	- 多进程
---

## 题目要求
编写Linux平台下的两个C语言程序实现如下功能：
（1）X、Y两个进程相互配合实现对输入文件中数据的处理，并将处理结果写入输出文件。
（2）X进程负责读分块取输入文件，并将输入数据利用共享内存传输给Y进程。
（3）Y进程负责将读入的数据（假定皆为文本数据）全部处理成大写，然后写入输出文件。
（4）为提高并行效率，X、Y两个进程之间创建2个共享内存区A、B。X读入数据到A区，然后用Linux的信号或信号量机制通知Y进程进行处理；在Y处理A区数据时，X继续读入数据到B区；B区数据被填满之后，X进程通知Y进程处理，自己再继续向A区读入数据。如此循环直至全部数据处理完毕。

## 解题
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <ctype.h>
#include <semaphore.h>
#include <sys/ipc.h>
#include <sys/shm.h>

const int BUFFER_SIZE = 2048;

int main(int argc, char const *argv[])
{
argv[1];
    // 文件
    int inputFile, outputFile;
    inputFile = open(argv[1], O_RDONLY);
    if(inputFile <= 0 ){
        printf("no file %s \n", argv[1]);
        return 0;
    }
    outputFile = open("./output.txt", O_WRONLY | O_CREAT, 0666);

    // 信号量
    sem_t *sem1;
    sem_t *sem2;
    sem1 = sem_open("sem1", O_CREAT, 0666, 1);
    sem2 = sem_open("sem2", O_CREAT, 0666, 0);

    // 共享内存
    // 定义交换空间结构体
    struct switchBuffer
    {
        char buffer[BUFFER_SIZE];
        int flag;
    };
    struct switchBuffer *buffera; // 交换空间a，b
    struct switchBuffer *bufferb;
    int shmaId, shmbId;

    // 创建共享内存
    shmaId = shmget((key_t)11, sizeof(buffera), 0666 | IPC_CREAT);
    shmbId = shmget((key_t)22, sizeof(bufferb), 0666 | IPC_CREAT);

    // 主进程读取数据
    // 子进程处理后写入数据
    pid_t cpid;
    cpid = fork();
    if (cpid < 0)
    {
        printf("error in fork\n");
    }
    else if (cpid == 0)
    {
        // 子进程
        printf("child process pid: %d \t", getpid());

        // 共享内存映射到进程空间
        buffera = (struct switchBuffer *)shmat(shmaId, 0, 0);
        bufferb = (struct switchBuffer *)shmat(shmbId, 0, 0);
        int switchFlag = 1;
        while(1)
        {
            sem_wait(sem2);
            if (switchFlag)
            {
                for (int i = 0; i < buffera->flag; ++i)
                {
                    buffera->buffer[i] = toupper(buffera->buffer[i]);
                }
                write(outputFile, buffera->buffer, buffera->flag);
            }
            else
            {
                for (int i = 0; i < bufferb->flag; ++i)
                {
                    bufferb->buffer[i] = toupper(bufferb->buffer[i]);
                }
                write(outputFile, bufferb->buffer, bufferb->flag);
            }
            switchFlag = !switchFlag;
            sem_post(sem1);
        }
    }
    else
    {
        // 主进程
        // 共享内存映射到进程空间
        buffera = (struct switchBuffer *)shmat(shmaId, 0, 0);
        bufferb = (struct switchBuffer *)shmat(shmbId, 0, 0);

        int fileFlag = 0;
        int switchFlag = 1;

        // 1.读取文件->buffer a
        // 2.等待处理进程
        // 3.触发处理进程
        // 4.读取文件->buffer b
        // 5.等待处理进程
        // 6.触发处理进程

        // 开始时预先触发2,开始运行任务
        // 通过read文件为0判断终止条件
        // 终止后再次等待处理线程处理最后的一个buffer
        while (1)
        {
            if (switchFlag)
            {
                fileFlag = read(inputFile, buffera->buffer, BUFFER_SIZE);
                buffera->flag = fileFlag;
            }
            else
            {
                fileFlag = read(inputFile, bufferb->buffer, BUFFER_SIZE);
                bufferb->flag = fileFlag;
            }
            switchFlag = !switchFlag;
            if (fileFlag <= 0)
                break;
            sem_wait(sem1);
            sem_post(sem2);
        }
        sem_wait(sem1);
    }

    // destory
    close(inputFile);
    close(outputFile);
    sem_close(sem1);
    sem_close(sem2);
    sem_unlink("sem1");
    sem_unlink("sem2");
    shmdt(buffera);
    shmdt(bufferb);
    shmctl(shmaId, IPC_RMID, 0);
    shmctl(shmbId, IPC_RMID, 0);
    printf("over\n");
    return 0;
}

```
