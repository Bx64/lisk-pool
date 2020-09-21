# SHIFT-pool distribution software
This software was originally created by lisk delegate "dakk", which I've edited slightly for easier use. 
Please consider a small donation to dakk if you use this software: 
- "2324852447570841050L" for lisk
- "7725849364280821971S" for shift
- "AZAXtswaWS4v8eYMzJRjpd5pN3wMBj8Rmk" for ark
- "8691988869124917015R" for rise

## Configuration
Configuration options include:

- pubkey: your delegate pubkey
- address: your delegate address
- percentage: percentage to distribute (change P to an integer, for example 50)
- minpayout: the minimum amount for a payout (change M to a decimal number, for example 0.01)
- secret: your secret
- secondsecret: your second secret or null if disabled
- donations: a list of object (address: amount) for send static amount every payout, can be used for reserve & donations (change reserve_address to your reserve/donations address and change R to an integer or decimal number)
- donationspercentage: a list of object (address: percentage) for send same percentage every payout
- node: the shift node where you get forging info + API port (mainnet: localhost:9305, testnet: localhost:9306, custom: IP:port)
- nodepay: the shift node used for payments + API port (mainnet: localhost:9305, testnet: localhost:9306, custom: IP:port)
- logfile: file where you want to write pending and sent amounts (default: poollogs.json)
- feededuct: true if you want to subtract fees from user payouts (default: false)
- private: true or false for private pool
- whitelist: list of addresses to include in private pool
- skip: a list of address to skip

You can also edit pages/index.html and customize the webpage, or leave as is if not running webpage.

## Setting up & running the shift-pool script

First clone the shift-pool repository: 

```git clone https://github.com/Bx64/shift-pool```

```cd shift-pool```

Edit your config.json file with the above entries:

```nano config.json```

You'll need to change the following lines:

```
"pubkey": "delegate_pubkey",	
"address": "delegate_address",	
"percentage": P,	
"minpayout": M,	
"secret": "delegate_secret",	
"secondsecret": "delegate_secret2",
"donations": { "reserve_address": R
```

Further modifications are possible, but may require edits in shiftpool.py as well. This is at your own risk.

Edit poollogs.json and put in lastpayout the unix-timestamp of your last payout, or the date of pool starting:

```nano poollogs.json```

Install python3-pip and requests:

```sudo apt-get install python3```

```sudo apt-get install python3-pip```

```pip3 install requests```

Then start it:

```python3 shiftpool.py```

It produces a file "payments.sh" with all payments shell commands. Run this file with:

```bash payments.sh```

The payments will be broadcasted (every 10 seconds). At the end you can move your generated poollogs.json to pages/poollogs.json:

```cp poollogs.json pages/poollogs.json```

### Private pool
If you want to run a private pool, you need to edit config.json and:

- private: set to true
- whitelist: put a list of address you wish to include

### Possible issues and fixes when installing/running

#### Cache folder ownership issue

Several users have mentioned running into permission issues with their ``.cache`` folder when installing requests. The fix for this is running this additional command AFTER installing python3-pip & BEFORE installing requests, replacing ``$USER`` with your username:

```cd && sudo chown -R $USER .cache```

#### shiftpool.py cannot get rewards

You should add ``"127.0.0.1"`` to the whitelist section of the configuration file for your SHIFT node, then reload your node:

```nano shift-lisk/config.json```

```shift-lisk/shift_manager.bash reload```

## Using a personal shift-pool repository on GitHub as your pool's front-end

It is also possible to use GitHub as your pool front-end. First, fork this repository. Then you can follow the above instructions, BUT replace 'Bx64' in the ``git clone`` command to your username. Assuming this is $USER, the first command will be:

```git clone https://github.com/$USER/shift-pool```

After that, follow the rest of the instructions exactly as described above. After completing the setup and running your payout, you can send the update to your Git repo:

```git add pages/poollogs.json```

```git commit -m "payouts update"```

```git push -u origin master```

Or run the following script and have those automated:

```bash pool-update.sh```

To display the pool frontend, enable GitHub Pages on GitHub repository settings. 

## Batch mode

The script is also runnable by cron using the -y argument:

```python3 shiftpool.py -y```

There is also a 'pay.sh' file which runs shiftpool.py, then payments.sh and copy the poollogs.json in the pages folder.

## Cronjob to automate payouts

Set this command as cronjob to automate payouts at chosen intervals:

```cd shift-pool && python3 shiftpool.py -y && sleep 5 && bash payments.sh && sleep 5 && cp poollogs.json pages/poollogs.json```

Payouts are not automatically uploaded to GitHub using this cronjob, you'll need to do that manually or using the pool-update.sh script.

### Avoid vote hoppers

In some DPOS, voters switch their voting weight from one delegate to another for receiving payout from multiple pools. A solution for that is the following flow:

1. Run shiftpool.py every hour with --min-payout=10000000000 (a very high minpayout, so no payouts will be done but the pending will be updated);
2. Run shiftpool.py normally to broadcast the payments

If no new forged blocks are discovered by the API request, shift-pool.py will exit automatically. You can set the commands as cronjobs to automate the above flow. Additionally, instead of setting commands, you can also set the cronjobs using these scripts: (1) prevent-poolhopping.sh && (2) pay.sh

## Command line usage

```
usage: shiftpool.py [-h] [-c config.json] [-y] [--min-payout MINPAYOUT]

SHIFT delegate pool script

optional arguments:
  -h, --help            show this help message and exit
  -c config.json        set a config file (default: config.json)
  -y                    automatic yes for log saving (default: no)
  --min-payout MINPAYOUT
                        override the minpayout value from config file
```

## License
Copyright 2017-2018 Davide Gessa

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
