### Application Introduction

novel-plus is a multi-terminal (PC, WAP) reading, full-featured original literature CMS system

### Vulnerability Introduction

Please refer to the official manual for the build environment

In the background, the parameters of sql execution are not handled, leading to sql injection vulnerability

### Code Audit Process

The vulnerability was found in the backend, and during the white-box audit, it was discovered that the backend password was weak by default, and the cause was the inability to pre-compile the orderby field using mybatis, and no filtering was done

There is a list method under the menu controller in the system module, which does not have any processing of the parameters to go to the next step in the process, and then we debug to follow up

![image-20230323150808326](../pics/image-20230323150808326.png)

Then call the list method of the MenuService interface class

![image-20230323150818396](../pics/image-20230323150818396.png)

Continue tracing back to the Dao interface layer

![image-20230323150827535](../pics/image-20230323150827535.png)

Finally locate the Menumapper.xml file

![image-20230323150840683](../pics/image-20230323150840683.png)

Here it is found that it accepts a parameter called sort, check as long as it is not empty and not a space

Write a payload for manual testing

/sys/menu/list?sort=(select*from(select%2Bsleep(5)union%2F**%2Fselect%2B1)a)

![image-20230323151035013](../pics/image-20230323151035013.png)

The delay is successful, in order to better demonstrate the effect, use sqlmap to test the local environment, grab the package to save the file, and then test

![image-20230323151048813](../pics/image-20230323151048813.png)

As you can see, using the sqlmap tool, the database vulnerability of the target host was successfully obtained