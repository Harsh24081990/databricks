Databricks Data Engneering Interview Questions

 𝑰𝐟 𝐲𝐨𝐮 𝐡𝐚𝐯𝐞 𝒕𝒐 𝒑𝒓𝒐𝒄𝒆𝒔𝒔 100 𝐆𝐁 𝐨𝐟 𝐝𝐚𝐭𝐚? 𝑯𝒐𝒘 𝒚𝒐𝒖 𝒘𝒊𝒍𝒍 𝒅𝒆𝒄𝒊𝒅𝒆 𝒕𝒉𝒆 𝒄𝒍𝒖𝒔𝒕𝒆𝒓 𝒄𝒐𝒏𝒇𝒊𝒈𝒖𝒓𝒂𝒕𝒊𝒐𝒏 ?

𝐒𝐓𝐄𝐏 1: 𝐍𝐮𝐦𝐛𝐞𝐫 𝐨𝐟 𝐞𝐱𝐞𝐜𝐮𝐭𝐨𝐫 𝐜𝐨𝐫𝐞𝐬 𝐫𝐞𝐪𝐮𝐢𝐫𝐞𝐝?

- We start by deciding how many executor cores we need 🤔
- One partition is of 128MB of size by default — 𝘪𝘮𝘱𝘰𝘳𝘵𝘢𝘯𝘵 𝘵𝘰 𝘳𝘦𝘮𝘦𝘮𝘣𝘦𝘳
- To calculate number of cores required, you have to calculate total number of partitions you will end up having
 100GB = 100*1024 MB = 102400MB
 Number of partitions = 102400/128 = 800
 
Therefore, 800 executor cores in total are required

𝐒𝐓𝐄𝐏 2: 𝐍𝐮𝐦𝐛𝐞𝐫 𝐨𝐟 𝐞𝐱𝐞𝐜𝐮𝐭𝐨𝐫𝐬 𝐫𝐞𝐪𝐮𝐢𝐫𝐞𝐝?

Now that we know how many cores, next we need to find how many executors are required.

- On an average, its recommended to have 2–5 executor cores in one executor
- If we take number of executors cores in one executor = 4 then, total number of executors = 800/4 = 200
 
Therefore, 200 executors are required to perform this task


𝐒𝐓𝐄𝐏 3: 𝐓𝐨𝐭𝐚𝐥 𝐞𝐱𝐞𝐜𝐮𝐭𝐨𝐫 𝐦𝐞𝐦𝐨𝐫𝐲 𝐫𝐞𝐪𝐮𝐢𝐫𝐞𝐝?
Important step! how much memory to be assigned to each executor 🤨

-By default, total memory of an executor core should be
4 times the (default partition memory) = 4*128 = 512 MB
 
Therefore, total executor memory = number of cores*512 = 4*512 = 2GB

𝐒𝐔𝐌𝐌𝐀𝐑𝐈𝐙𝐄: 𝐓𝐨𝐭𝐚𝐥 𝐦𝐞𝐦𝐨𝐫𝐲 𝐫𝐞𝐪𝐮𝐢𝐫𝐞𝐝 𝐭𝐨 𝐩𝐫𝐨𝐜𝐞𝐬𝐬 100𝐆𝐁 𝐨𝐟 𝐝𝐚𝐭𝐚

We are here! 🥳lets finalize on total memory to be required to process 100GBs of data
 
- Each executor has 2GB of memory
- We have total of 200 executors

Therefore, 400GB of total minimum memory required to process 100GB of data completely in parallel.


## Point to Note: -
- 400 GB minimum memory to process 100 GB of data, isn't it too much??
- I assume this is the resouce needed to finish this 100 Gb in 1 second or less. Now if I want to finish the job in 15 mins or 30 mins The resouce configuration should be  different.
- 