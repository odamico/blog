---
title: "Data Science Project: Creating a Command Line Pokedex in Python3"
author: "Oscar D'amico"
date: 2020-01-12
draft: false
tags: 
  - python
  - datascience
  - webscraping
---

Solving interesting problems and creating neat applications have always been the best 
motivators for me to program. While playing Pokemon Sun, I found myself
googling the new species I would encounter quite often. I would take time to 
look at their stats and what they were weak to and strong against. These searches started 
taking up time very quickly. For reference, each page contains a table of contents like this:  

{{< figure src="https://odamico.github.io/contents.png" alt="Bulbapedia table of contents for Mimikyu" link="https://bulbapedia.bulbagarden.net/wiki/Mimikyu_(Pok%C3%A9mon)" caption="The bulbapedia page for one Pokemon - Mimikyu" >}}

That is quite a lot of information. Especially when all the data I needed could be represented
in a couple of lines of text. I felt like I could program a solution that fit my needs more
closely. My goal with minidex.py was to create a tool that met the following requirements:

* Provide a quick search by Pokemon name.
* Return relevant information for each pokemon that fit the search.
* Include the type effectiveness for each pokemon.
* Update regularly, in case new species are released, but don't constantly pull data from the internet. 

The source code for my solution can be found on my github at https://github.com/odamico/minidex.py along
with instructions for how to get the program up and running in the README.md file. Here, I will
be demonstrating the program's use.
 
You can run minidex.py program like so:

```sh
./minidex.py
```
```
[-] Critical file dex_data.csv corrupted...
[-] Critical file type_data.csv corrupted...
[-] Database file contains no data or is outdated
Updating it now...
[-] Type chart file contains no data or is outdated
Updating it now...

                                  ,'\
    _.----.        ____         ,'  _\   ___    ___     ____
_,-'       `.     |    |  /`.   \,-'    |   \  /   |   |    \  |`.
\      __    \    '-.  | /   `.  ___    |    \/    |   '-.   \ |  |
 \.    \ \   |  __  |  |/    ,','_  `.  |          | __  |    \|  |
   \    \/   /,' _`.|      ,' / / / /   |          ,' _`.|     |  |
    \     ,-'/  /   \    ,'   | \/ / ,`.|         /  /   \  |     |
     \    \ |   \_/  |   `-.  \    `'  /|  |    ||   \_/  | |\    |
      \    \ \      /       `-.`.___,-' |  |\  /| \      /  | |   |
       \    \ `.__,'|  |`-._    `|      |__| \/ |  `.__,'|  | |   |
        \_.-'       |__|    `-._ |              '-.|     '-.| |   |
                                `'                            '-._|
-------------------------------------------------------------------------
Search by Pokémon name [q --> quit, + --> keep printing]
> 
```
On the first run, we see that two database files, and two checksums are saved in 
the directory that the program was run from. At the minidex prompt, you can enter
a name to look up. For example, lets look up one of my personal favorites, Slaking:
```
-------------------------------------------------------------------------
Search by Pokémon name [q --> quit, + --> keep printing]
> Slaking
```
The following output is produced: 
```



----------------------------------------------------------------------------------+
 Pokédex #     Name    Type  Total   HP  Attack  Defense  Sp. Atk  Sp. Def  Speed |
----------------------------------------------------------------------------------+
       289  slaking  normal    670  150     160      100       95       65    100 |
	Nor Fir Wat Ele Gra Ice Fig Poi Gro Fly Psy Bug Roc Gho Dra Dar Ste Fai   |
	 -   -   -   -   -   -    2  -   -   -   -   -   -    0  -   -   -   -    |
----------------------------------------------------------------------------------+


Matches:  1
```

We get the pokedex number, name, type, stat total, and each individual stat in the first row
of data which appears after the header. The second row of data displays every type's effectiveness against Slaking. For example, 
we can see that Normal, Fire, and Water are unmodified, as they contain a value of "-". However, 
Slaking is two-times weak to Fighting, which shows a "2" and unaffected by Ghost which contains a "0".
Other possible values include 4, 1/2, and 1/4.

By default, the output is truncated to 10 entries when we enter a query that produces a large number
of matches. But we can extend the output to include all matches by using the '+' flag. For example: 

```
-------------------------------------------------------------------------
Search by Pokémon name [q --> quit, + --> keep printing]
> S+
```
The above query returns all pokemon whose names start with "s".

To speed things up a little bit, we can enter our query as an argument when running the 
program. For example, we can run the program as follows:

```sh
./minidex.py Muk
```
```



----------------------------------------------------------------------------------------------+
 Pokédex #            Name         Type  Total   HP  Attack  Defense  Sp. Atk  Sp. Def  Speed |
----------------------------------------------------------------------------------------------+
        89             muk       poison    500  105     105       75       65      100     50 |
	Nor Fir Wat Ele  Gra Ice  Fig  Poi Gro Fly Psy  Bug Roc Gho Dra Dar Ste  Fai          |
	 -   -   -   -   1/2  -   1/2  1/2   2  -    2  1/2  -   -   -   -   -   1/2          |
----------------------------------------------------------------------------------------------+
        89  muk alolan muk  poison dark    500  105     105       75       65      100     50 |
	Nor Fir Wat Ele  Gra Ice Fig  Poi Gro Fly Psy Bug Roc  Gho Dra  Dar Ste Fai           |
	 -   -   -   -   1/2  -   -   1/2   2  -    0  -   -   1/2  -   1/2  -   -            |
----------------------------------------------------------------------------------------------+
       771       pyukumuku        water    410   55      60      130       30      130      5 |
	Nor  Fir  Wat Ele Gra  Ice Fig Poi Gro Fly Psy Bug Roc Gho Dra Dar  Ste Fai           |
	 -   1/2  1/2   2   2  1/2  -   -   -   -   -   -   -   -   -   -   1/2  -            |
----------------------------------------------------------------------------------------------+


Matches:  3
```
