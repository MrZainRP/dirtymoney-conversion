### "dirtymoney" a guide to converting your qb-core server from rewarding players in "markedbills" to rewarding players in a new item.

By Mr_Zain#4139

### A. INTRODUCTION

1. The "markedbills" system used by qb-core to reward players for illegal activity has its pros and cons.

2. If you are looking to shift from a "markedbills" to a "dirtymoney" type of illegal activity reward system please follow the steps set out in this readme. 

### B. QB-CORE/SHARED/ITEMS.LUA

3. First, you will need to create a new item in your qb-core/shared/items.lua. You can call it whatever you like. I will be calling it "dirtymoney". You may copy and paste the following:

```lua
	["dirtymoney"]                   = {["name"] = "dirtymoney",                    ["label"] = "Dirty Money",              ["weight"] = 0,         ["type"] = "item",      ["image"] = "dirtymoney.png",           ["unique"] = false,		["useable"] = true,     ["shouldClose"] = false,    ["combinable"] = nil,  ["description"] = "The ill-gotten proceeds of criminal activity."},
```

### C. INVENTORY IMAGES

4. You will see from the above "image" field that the server will be looking for an item within your inventory system named "dirtymoney.png". 

5. You can use any image you like although it is suggested that you keep your image to the pixel size 100x100 and retain its transparency. 

6. An example image is included in this repository. Add it to your inventory images folder. If using lj-inventory, add the images to: lj-inventory/html/images/

7. If you attend to the above steps you will need to restart the server in order for the new added items to be recognised by qb-core. Attempting to refer to "dirtymoney" without doing this will cause errors. 

### C. USING YOUR NEW "DIRTYMONEY"

8. Now that dirtymoney exists in your city/server (following a restart of the server after adding it in), your goal is to go through each of the resources/scripts you are using and replace any references to "markedbills" to references to "dirtymoney". Running two different illegal proceeds systems is very much not recommended (pick one or the other).

9. The conversion is, unfortunately, not as simple as changing the name "markedbills" to "dirtymoney" - but it is close. 

10. The reason for one extra layer of complexity is because "markedbills" contain their value in the "info" field of the item. 1x markedbills can be worth $2000 for example. Whereas with dirtymoney, 2000x dirtymoney = 2000 dirty dollars - any description is purely cosmetic and serves no arithmatic function. 

11. If we take qb-storerobbery as an example of a base qb-core script that rewards players in "markedbills" and which will need to be converted (please note: mz-storerobbery already has these options set and you adjust via config. But assuming you are using qb-storerobbery), please open qb-storerobbery/server/main.lua and refer to lines 47-52 which are, by default (as at the time of writing this), as follows: 

```lua 
    local bags = math.random(1,3)
    local info = {
        worth = math.random(cashA, cashB)
    }
    Player.Functions.AddItem('markedbills', bags, false, info)
    TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['markedbills'], "add")
```

12. To convert this to a reward in dirtymoney, the "info" and the "bags" field are unnecessary. You can remove the reference to those references so that you are left with the variable/argument of relevance - in this case "worth" - as follows:

```lua
    local worth = math.random(cashA, cashB)
    Player.Functions.AddItem('markedbills', bags, false, info)
    TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['markedbills'], "add")
```

13. "worth" is how much the player will receive from the robbery (in this function it is what a player gets from robbing the cash register). "cashA" and "cashB" are references to lines 3 and 4 of the script which provide as follows: 

```lua
local cashA = 250 				--<<how much minimum you can get from a robbery
local cashB = 450				--<< how much maximum you can get from a robbery
```

14. To keep things together, you can delete these references and replace them with the dollar amounts which you wish to use in your server. For example, in this case, math.random(cashA, cashB) - defining cashA as 250 and cashB as 450 - is the same as saying math.random(250, 450).

15. You are then ready to remove the reference to "bags", "false" and "info" from the "AddItem" function - and to add the "worth" to the "inventory:client:ItemBox" reference. Remember, the variable for how much dirty money is awarded in this example is "worth" so that is the variable/arhgument taht we are including instead of the arguments we have removed. What you want to end up with is as follows:

```lua
    local worth = math.random(cashA, cashB)
    Player.Functions.AddItem('dirtymoney', worth)
    TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['dirtymoney'], "add", worth)
```

16. As said above, you can then either re-define "cashA" and "cashB" as you see fit or remove the variables being defined and refer directly to the monetary amounts you want to award a player as raw numbers. For example: 

```lua
    local worth = math.random(250, 450)
    Player.Functions.AddItem('dirtymoney', worth)
    TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['dirtymoney'], "add", worth)
```

17. Once completed, save this code and re-ensure qb-storerobbery in your server (in your F8 console type ensure qb-storerobbery). Try robbing a cash register and note the results. 

18. Now, when a player robs a cash register, they should receive somewhere between 250 and 450 dirtymoney for their trouble. 

18. The other reference to "markedbills" in qb-storerobbery is at lines 97-102 regarding the reward for robbing the safe. The reference is as follows:

```lua
	local bags = math.random(1,3)
	local info = {
		worth = math.random(cashA, cashB)
	}
	Player.Functions.AddItem('markedbills', bags, false, info)
	TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['markedbills'], "add")
```

19. The same conversion applies. You do not want "bags" or "info", you simply want a math.random function performed to calculate the amount of dirtymoney a player gets. For example: 

```lua 
    local worth = math.random(10000, 15000)
    Player.Functions.AddItem('dirtymoney', worth)
    TriggerClientEvent('inventory:client:ItemBox', src, QBCore.Shared.Items['dirtymoney'], "add", worth)
```

21. The above changes deal conclusively with qb-storerobbery - quite simple once you recognise what variables/arguments are needed from what variables/arguments are not needed.

22. Loading up the whole of your server resources/scripts in Visual Studio Code and searching for the term "markedbills" will tell you how many conversions need to be made. Once all references have been changed to dirtymoney, congratulations, your server is now running a dirtymoney criminal economy. 
