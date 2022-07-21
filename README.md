# Monitoring forks (forktester.py)

Forktester can be used to manage if your node is on fork. It uses controlnodes which are used to compare block headers between your node and the control nodes. Forktester requests 5 blocks (counting from lastblock-2) and compares these blocks between your node and the controlnodes defined in the forkconfig.json file (key "controlnodes" : { ... }) Forktester integrates alerting via Telegram. If problems are found (like a fork), alerting is send to your telegram account. Rollback can be activated also via forktester. This can be done manually or automatically (option "auto_rollback" : "yes").

WARNING Use config option "auto_rollback" : "yes" (default "no") with precaution! Alerting that a fork happened, can be a false positive. It's better to first use forktester for some time without the automatic rollback function turned to "yes" and do some manual investigation if an alert is send about a possible fork. If you concluded that a fork indeed happened on your node, there is a forkfile created with the name "forked.". If you start forktester and a forkfile is found it reports on the action you can do to execute rollback or to remove the forkfile if it is a false positive.

Forktester uses settings from forkconfig.json. For best fork control, execute forktester.py periodically from a job sceduler, like crontab. For more help, execute : forktester.py --help



## System preparation:
```
sudo apt install build-essential
sudo apt install python3-dev
sudo apt install python3-pip
sudo apt install python3-testresources
```


## EDIT settings in the configuration file 'forkconfig.json'.
   The default file looks like this;

```
{
	"thenode" : {
    "node_api" : "",
		"node_apikey" : "",
		"nodename" : ""
	},

	"forktoolsconfig" : {
	  "controlnodes" : {"http://#####:6869" : "up",
      "http://#####:6869" : "up",
      "http://#####:6869" : "up",
      "http://#####:6869" : "up",
      "http://#####:6869" : "up"},

		"auto_rollback" : "no",
		"lastblockheight" : "/blocks/height",
		"blockheaders" : "/blocks/headers/at/",
		"rollback_blocks" : "2000"

	},

	"telegramconfig" : {
		"use" : "no",
		"telegram_api" : "https://api.telegram.org/bot",
		"telegram_token" : "",
		"telegram_chat_id" : "",
		"telegram_group" : "",
		"telegram_botuser" : ""
	}

}
```

## Node (This part is for thenode)

```
- "node_api"
   This is the node (name or ip address) and tcp port of the API server where you run your queries to.

- "node_apikey"
  The API password of your node. Put it as PLAINTEXT in the forkconfig.json file!

NOTE
  This is the same password you need to use in the config file of your Waves node,
   but needs to be coded as base58 string in the node config file.

   - "nodename"
   This is a textual name identifying your node.

WARNING
  keep it safe and confidential to you.
      For security reasons, remove 'rwx' worldrights from forkconfig.json : chmod o-rwx forkconfig.json
```   

## forktoolsconfig (This part is for the forktesting tool)

```
- "controlnodes" (list)
  List of controlnodes used by forktester.py. Status should be up or down.
  Down nodes will not be used. Example: { "http://anode.blockchain.net:6869" : "up",
            "http://10.20.30.40:80" : "up" },

- "auto_rollback" (yes/no)
  This defines if your node automatically executes a rollback if it detects fork.
  Becarefull! Forktester can send alerts of a forked is detected and you have more
  control to rollback manually. Forks do normally not happen often. Default "no".
  ```

## telegramconfig (This part has all the telegram details)

  ```
- "use" (yes/no)
   Activate telegram usage if alerts are raised by tools.

 - All other JSON keys for telegram
   Create an account on telegram and fill out all details in forkconfig.json
```


## How to create Telegram bot

https://core.telegram.org/bots

To get your ID or group - https://t.me/myidbot


## Test the script

```
python3 forktester.py
```
Now, edit the crontab to run this script periodically:

```
crontab -e
```
Add this line to execute the forktester will check every 10 mins if a fork happended, (choose your own times - https://crontab.guru/), also correct the paths:

```
*/10 * * * * python3 /home/your_dir/forktester.py
```


Many thanks to original version of forktester Plukkieforger! https://t.me/Plukkie
