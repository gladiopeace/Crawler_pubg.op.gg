# Web Crawler for *pubg.op.gg*
In this file, I will talk you through the entire code I wrote for this web crawler. I don't have much documentation within each file because I find most of them tedious and straightforward if you have done any scraping. Therefore, I will introduce the idea of this project and clarify some code in file. If after reading this file, the whole thing still doesn't make sense to you, don't hesitate to leave a comment below or somewhere else in this repo.

Before the detailed introduction, check if you have **Python 3.6** installed on your machine, because I used **Python 3.6**. If you do have **Python 3.6**, you could move on to installing dependencies.

```
pip install bs4
pip install lxml
pip install requests
pip install selenium
```

## Composition
This entire project consists of four separate files, three of which are functional files and the remaining one is a simple wrapper.

###### main.py
This is a simple wrapper. Just remember to replace zsda123*(in line 13)* with a **real** PUBG user ID.

###### finder.py
This plays an important role in this entire project, because you will find that userID for each player in *[pubg.op.gg](https://pubg.op.gg)* in a random hexadecimal number. However, I still managed to find a pattern in the previous version of *finder.py*:

- UserID has a cluster-like distribution. In each cluster, there are 40 to 80 users.

I had my server run for a whole day and could not capture any cluster besides the starting one. Therefore, I decided to forgo this brutal way. Then, I decided to go with this new way:

Find the other players within the same game with the *user0*, In this case, *zsda123*. If you want to capture a larger sample, change **line 73** to the following.
```python
q = nameToId(userMap(userMap(q)))
```
By calling *userMap* twice, I was able to capture 4970 distinct users, and it took me an hour. The size of the data grows exponentially! Suppose there are 80 players in each game and you decided to call *userMap* 3 times. You should expect your server to do work proportionally to **512,000** users, which could eat up all of the memory of your server/PC. Be aware of that!

Let's talk more on documentation. In *Line 19* and *Line 42*, the query variable server is set to *as*. You can change it to other servers if you feel like doing that. Since the companying I am working for is a company focused on Greater China, most of the server settings are Asia or Southeast Asia or Japan/Korea.

In *Line 28*, remember to change that value to a real *op.gg* player ID. The reason I have this line is that sometimes a player with name **#unknown** appears~~, due to their rubbish servers~~.  I will tell you how to find a player ID now.
- Go to *[pubg.op.gg](https://pubg.op.gg)*
- Search a real PUBG player username
- Wait for the whole webpage to be fully loaded
- Inspect the webpage and go to Network section
- Scroll all the way to the bottom and click on More
- You should be able to see a request begins with *recent?*
- Check Request URL, the player ID is the string after *https://pubg.op.gg/api/users/*

In *Line 40*, don't forget to change the executable_path to the path of your driver. After El Capitan, Mac users no longer have access to /usr/bin. You can put the driver under /usr/local/bin and change the executable_path like what I did. Also, you have to have **that browser installed** on your machine, which means if you don't have Firefox and you use *geckodriver*, you will receive a bunch of warnings! Also, if you do use Firefox, make the change as such.
```
driver = webdriver.Firefox(executable_path = "/usr/local/bin/geckodriver")
```

In *Line 44,45,46*, you will see a big chuck of code there. Don't worry about it, I did not type it up. I will be mad if I did. It was generated by *Xpath Finder*, a plugin for Chrome. Line 44 is used to select the game with rank lower than 10, Line 45 for Top 10 games and Line 46 for Chicken Dinners. By clicking on those buttons, the game details are loaded into the webpage. Since it is asynchronous, I make the machine wait for a while (2 is tested to be not stable, so I go with 3 seconds).

The rest of the file is basically some beautiful soups, and I believe no further explanation is needed for that part.

###### scraper.py
I want to explain why I have a try statement in *Line 46*. Some users they just don't play solo games, so we could not get any data from there. Also, you could change the parameters passed into *query()* to extract something different. Besides these, we have another bunch of beautiful soups.

###### reader.py
The data I collect is quite primitive, so I also have a simple reader to summarize my data.
```
[userId, x['participant']['user']['nickname'], x['season'], x['server'], x['queue_size'], x['mode'], x['participant']['stats']['combat']['kda']['kills']]
```

## Acknowledgement
I thank *op.gg* for not banning my IP, because as you can see, I did not set up proxy. All credits go to *op.gg*, because I am using their backdoor APIs and database and I feel obligated to say so.

## Footnote
I know I said a lot. Today is the last business day of year 2017, and I finished this project on PUBG, my favorite game so far. I thank my co-workers here at Bullup Inc. for their generous help. To give something back to the world, I decided to make this repo public and write nice documentation for it XD.
```
<\2017>
<2018>
```