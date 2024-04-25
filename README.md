<h1>Understanding DNS 💡</h1>

> [!Note]
> What is DNS?
> DNS stands for Domain Name System. It's like a phonebook that translates a website address into a numerical IP address so computers can identify each other on the internet. It makes it more simpler for a computer to find the correct server host.

<h2>Expectations 🤔</h2>

> [!Important]
> You'll need two VMs for this: A Domain Controller and a Client with Active Directory installed. If you need guidance on how to create these, you can refer to the [Active Directory Configuration Guide](https://github.com/EMoniSmall/ad-configure).

- Inspect DNS A-Records on the server

- Create some custom A-Records to observe from the client VM

- Delete Records from the server and inspect/clear client DNS Cache

- Gain an understanding of "CNAME" records.

- Gain an understanding of Root Hints

<h2>Inspecting A-Records </h2>

Step 1: Log into DC-1 and Client-1 as your Admin account. (It will be John_Admins in this example.)

Step 2: From Client-1 Remote Desktop, open Command Prompt and type ping mainframe. You'll notice that it failed. 

![image](https://github.com/EMoniSmall/DNS/assets/166156618/8606c430-bd39-46b3-bb37-6341ea502510)

> [!Note]
> What happened is Client-1 attempted to check the cache, host file, and then DNS to find "mainframe" but shows that there are no results and thus the ping fails

Step 3: Switch to DC-1 Remote Desktop Connection and open Server Manager > Tools > DNS.

Step 4: In DNS Manager, Open DC-1 > Forward Lookup Zones > mydomain.com and you'll see some A-Records. 

![mstsc_xB6evpqD82](https://github.com/EMoniSmall/DNS/assets/166156618/ebb15a92-d611-43e0-b74a-18fd8f95dc7c)

Step 5: Right-Click inside the folder and create a New Host (A or AAAA...) and name it [mainframe] or whatever you like. In the IP Address bar, use the IP address for dc-1. Add Host.

![mstsc_oLG09jYhAv](https://github.com/EMoniSmall/DNS/assets/166156618/c5a236d7-48e8-427f-9f16-d5a470998657)

Step 6: Return to Client-1 and Attempt to ping mainframe again. This time should be successful.

> [!Note]
> By entering DC-1's IP for mainframe, we manually created a record. In CMD, you can type nslookup mainframe to see the record and the IP address returned. You can also enter ipconfig /display dns. It will show the records Client-1 has gone to. You'll notice that the A record for the mainframe will now be in the local cache which makes it faster for Client-1 to find.

<h2>Understanding Local DNS Cache</h2>

Step 1: Open up DC-1 Remote Desktop. 

Step 2: In DNS Manager, right-click mainframe > Properties > Update IP Address to 8.8.8.8. Apply.

Step 3: Return to Client-1 Remote Desktop and attempt to Ping mainframe. 

> [!Note]
> You'll notice that it's pinging the old IP Address (10.0.0.4). This happens because the old IP still exists in the local cache on Client-1. If you type ipconfig /displaydns, you'll see that mainframe.mydomain.com is still showing up as 10.0.0.4. Network resources might change but the local computer may keep the old record in it's local cache and may prevent you from reaching certain resources. This is where ipconfig /flushdns in command prompt is used.

Step 4: In Command Prompt, type ipconfig /flushdns. If you get the message, "The requested operation requires elevation" you may need to reopen Command Prompt as Admin first. 

![mstsc_MEQNm4FHT3](https://github.com/EMoniSmall/DNS/assets/166156618/1abc11ff-022a-4c01-97f1-f134bcdba589)

Step 5: Ping Mainframe once more, and you'll see that the IP for mainframe is now 8.8.8.8. 

> [!Note]
> Because the local cache has been flushed, pinging mainframe again will make Client-1 go through the DNS server to find the mainframe again but will return this time with its new IP address.

<h2>CNAME Records</h2>

> [!Note]
> A CNAME which stands for Canonical Name, is a type of DNS record that's used to map one domain to another. An alias for another name. 

Step 1: Open DC-1 Remote Desktop. Inside DNS Manager, Right-Click > New Alias (CNAME). Name it "search" and under Full qualified domain name for target host, enter www.google.com.

Step 2: Return to Client-1 Command Prompt and type in ping search. You'll see that pings google.com successfully. 

<h2>Root Hints</h2>

> [!Note]
> What is a Root Hint?
> Inside DC-1's properties, it describes "Root Hints resolves queries for zones that do not exist on the local DNS server. They are only used if forwarders are not configured or fail to respond." If a computer doesn't know a certain site, such as Netflix.com, it uses a root hint to give the computer an idea of where to begin searching for the website. An example of a Root Hint can be .com, .net, .org, and so on.





