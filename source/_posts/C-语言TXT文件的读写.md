---
title: C 语言TXT文件的读写
date: 2017-08-22 20:30:52
tags: 常用的C语言小程序代码
---
# C语言编写TXT文件的读写

本文针对的是对txt文件中数据的读写操作（非二进制的，二进制的可能后面会补充）,因为用到做个记录。

<!--more-->

## C语言编写TXT文件的读

```c
void Read_file(double **Matrix, char *FileName, int height, int wide)
{
	FILE *fp = fopen(FileName, "r");
	if (!fp)
	{
		printf("can't open file\n");
	}
	else
	{
		for (int i = 0; i < height; i++)
		{
			for (int j = 0; j < wide; j++)
			{
				fscanf(fp, "%lf", &Matrix[i][j]);
			}
		}
	}
	fclose(fp);
}
```

## C语言编写TXT文件的写

```c
FILE *fp = fopen(output, "w+");
if (!fp)
{
	printf("can't open file\n");
}
else
{
	for (int i = 0; i < FC_height; i++)
	{
		for (int j = 0; j < FC_wide; j++)
		{
			fprintf(fp, "%lf\t", FC_matrix[i][j]);
		}
		fprintf(fp, "\n");
	}
}
	fclose(fp);
```