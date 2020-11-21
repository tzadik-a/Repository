#include "stdafx.h"
#include "afxdialogex.h"
#include "cx.h"
#include<string>
#include<iostream>
#include<fstream>
#include<cstring>
#include<cstdio>
#include<conio.h>
#include<vector>
using namespace std;
Driver drivers[100]; 

int num=0;                                                      //定义程序需要用到的一些全局变量
int ave_age = 0;
int ave_d_age = 0;
int age_ett = 0;
int age_ttf = 0;
int age_ftf = 0;
int age_fts = 0;
int age_sts = 0;
int f_sex_num = 0;
int m_sex_num = 0;
int sor[80]={1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40};

int nian(CString p)                              //判断身份证中的出生年月日是否符合实际情况 
{
   int year,month,day;
   int mon[12]={31,28,31,30,31,30,31,31,30,31,30,31};
   year = 1000*(p[6]-48) + 100*(p[7]-48) + 10*(p[8]-48) + (p[9]-48);
   month = 10*(p[10]-48) + (p[11]-48);
   day = 10*(p[12]-48) +(p[13]-48);
   mon[1] = (year%4==0 && year%100!=0) || year%400==0 ? 29 : 28;
   if ( year > 1900 && year < 2002 && month > 0 && month < 12 )
   {
	   if ( day > 0 && mon[month-1] >= day)
	   {
	   return 1;
	   }
	   else return 0;
   }
   else return 0;
}

int judge(CString d)                           //判断身份证是否符合实际情况
{
    int i;
    if(d.GetLength()==18)
	{
        for(i=0;i<17;i++)
        {
			if(!(47<d[i]&&d[i]<58))
            {
                return 1;
            }
        }                  /*判断前面的十七位是否是数字*/
		if( ( (47<d[17] && d[17]<58) || d[17]=='X' ) && ( nian(d) == 1 ) )   /*判断最后一位是否是X或者是数字*/
        {
			  return 0;    
         }
		else return 1;
	}
	else return 1;
}
BOOL delete_driver(string temp)                                            //删除函数
{
	int m;
	for (int i = 0; i <= num; i++)
	{
		m = i;
		if (strcmp(temp.c_str(), drivers[i].card_n.c_str()) == 0)
		{
			m = i;
			break;
		}
		
	}
	if (m == num)
	{
		return FALSE;
	}
	else
	{
		for (int i = m; i < num-1; i++)
			drivers[i] = drivers[i+1];
		num--;
		return TRUE;
	}
}
BOOL lead_to_file(Driver d[])                                     
{
	FILE* in = fopen(f_name.c_str(), "w+");
	if (in == 0) 
	{  
		return FALSE;
	}
	for (int i = 0; i < num; i++)
	{
		fprintf(in, "%s %s %s %d %d %s %s %s %s", d[i].name.c_str(), d[i].sex.c_str(), d[i].profession.c_str(), d[i].d_age, d[i].age, d[i].card_n.c_str(),d[i].c.brand.c_str(), d[i].c.car_type.c_str(), d[i].c.color.c_str());
		fputc(' ', in);
		cout << endl;
	}
	fclose(in);
	FILE *fp = fopen(m_name.c_str(), "wb+");
	if (fp == 0) { return FALSE; }

	fwrite(&num, 4, 1, fp);
	fread(&num, 4, 1, fp);
	fclose(fp);
	return TRUE;
}
BOOL read_data()                                                     //读取库
{
	FILE *fp = fopen(m_name.c_str(), "rb");
	if (fp == 0) {  return FALSE; }

	fread(&num, 4, 1, fp);
	fclose(fp);
	return TRUE;
}

BOOL read_admin(Driver d[])
{
	ifstream inf(f_name.c_str());
	if (!inf) {  return FALSE; }

	for (int i = 0; i < num; i++)
	{
		inf >> d[i].name >> d[i].sex >> d[i].profession >> d[i].d_age >> d[i].age  >>d[i].card_n>>d[i].c.brand>>d[i].c.car_type >> d[i].c.color;
		cout << d[i].name << d[i].sex << d[i].profession << d[i].d_age << d[i].age << d[i].card_n << d[i].c.brand << d[i].c.car_type << d[i].c.color;
	}
	return TRUE;
}

BOOL add_to_file(Driver d, Car c)                                           //增加车友信息到data中
{
	FILE* in = fopen(f_name.c_str(), "a+");
	if (in == 0) { return FALSE; }

	fprintf(in, "%s %s %s %d %d %s %s %s %s", d.name.c_str(), d.sex.c_str(), d.profession.c_str(), d.d_age, d.age, d.card_n.c_str(),c.brand.c_str(), c.car_type.c_str(), c.color.c_str());
	fputc(' ', in);
	cout << endl;
	printf("%s %s %s %d %d %s %s %s %s", d.name.c_str(), d.sex.c_str(), d.profession.c_str(), d.d_age, d.age, d.card_n.c_str(), c.brand.c_str(), c.car_type.c_str(), c.color.c_str());
	fclose(in);

	FILE *fp = fopen(m_name.c_str(), "wb+");
	if (fp == 0) { return FALSE; }
	fread(&num, 4, 1, fp);
	num++;
	fseek(fp, 0, SEEK_SET);             //找到初始位置 为写入num做准备
	fwrite(&num, 4, 1, fp);             //更新总个数
	fclose(fp);
	return TRUE;
}
void sortadd(Driver d[])                //年龄升序
{
	
	int a;
    for(int i=1; i<=num-1; i++)                
	{
		for(int j=1;j<=num-i;j++)
		{
			if(d[sor[j-1]].age>d[sor[j]].age)
			{
		        a=sor[j];
				sor[j]=sor[j-1];
				sor[j-1]=a;  
			}
		}
	}	
}
void sortjadd(Driver d[])                //驾龄升序
{
	int a;
    for(int i=1; i<=num-1; i++)                
	{
		for(int j=1;j<=num-i;j++)
		{
			if(d[sor[j-1]].d_age>d[sor[j]].d_age)
			{
		        a=sor[j];
				sor[j]=sor[j-1];
				sor[j-1]=a;  
			}
		}
	}	
}
void sortjdec(Driver d[])                //驾龄降序
{
	
	int a;
    for(int i=1; i<=num-1; i++)                
	{
		for(int j=1;j<=num-i;j++)
		{
			if(d[sor[j-1]].d_age<d[sor[j]].d_age)
			{
		        a=sor[j];
				sor[j]=sor[j-1];
				sor[j-1]=a;  
			}
		}
	}	
}
void sortdec(Driver d[])                //年龄降序
{
	
	int a;
    for(int i=1; i<=num-1; i++)                
	{
		for(int j=1;j<=num-i;j++)
		{
			if(d[sor[j-1]].age<d[sor[j]].age)
			{
		        a=sor[j];
				sor[j]=sor[j-1];
				sor[j-1]=a;  
			}
		}
	}
}

